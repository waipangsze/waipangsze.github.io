---
layout: post
title: "CFD | OpenFOAM | Allrun"
categories: [CFD]
tags: [OpenFOAM,HPC]
author: wpsze
date: 2024-12-03 12:49:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/fFT42Yk.png
banner_img: https://i.imgur.com/fFT42Yk.png
---

# Allrun

All OpenFOAM tutorials have `Allrun` and `Allclean` scripts which call some mysterious RunFunctions from even more mysterious sources.

Source OpenFOAM first,

{% note danger %}
source /home/wpsze/openfoam/openfoam8/source_openfoam.sh
{% endnote %}

## The Manual Way

To be honest, one could easily get along without those functions. Just call `OpenFOAM` applications by their name:

```sh
#!/bin/sh
 
# background mesh for Snappy
blockMesh > log.blockMesh
 
# decompose and mesh
decomposePar -force > log.decomposePar
mpirun -np 4 snappyHexMesh -overwrite > log.snappyHexMesh
mpirun -np 4 checkMesh > log.checkMesh
mpirun -np 4 renumberMesh -overwrite > log.renumberMesh
 
# see an explanation below
rm -r 0
cp -r 0.orig 0
 
# run the solver
mpirun -np 4 simpleFoam
```

As you can see, it works just fine, but it does get a little verbose and repetitive. And if you decide to change the number of processors, you have to edit `system/decomposeParDict` and every parallel-running application call in all your scripts.

## `RunFunctions` to the Rescue

These functions are defined in `$WM_PROJECT_DIR/bin/tools/RunFunctions` so if you want to use them, you have to tell Bash where they are (**mind the dot at the beginning**):

```sh
#!/bin/shs
cd ${0%/*} || exit 1    # run from this directory

. ${WM_PROJECT_DIR:?}/bin/tools/RunFunctions
 
# background mesh for Snappy
runApplication blockMesh
 
# decompose and mesh
runApplication decomposePar -force
runParallel snappyHexMesh -overwrite
runParallel checkMesh
runParallel renumberMesh -overwrite
 
# restore 0/ directory
restore0Dir
 
# run the solver
runParallel $(getApplication)
```

### runApplication

`runApplication` will, as the name suggests, run the specified application.

- **It will redirect the app’s output to a log file.**
- **Also, if the log exists, it will not run!**
- If you don’t care about existing logs, you can overwrite it with `-o` option:
  - `runApplication -o decomposePar -force`
- If you run the app multiple times and want the same log, append to an existing one with `-a` option:
  - `runApplication -a decomposePar -force`

### runParallel

The same as runApplication but it will first **read** `system/decomposeParDict` to get number of processors. It will then run your app in parallel with MPI. The same logging options apply as to runApplication.

### getApplication

This will **read** `system/controlDict`.application and return that command. You will usually need this just to avoid hard-coding solvers. According to DRY principle, **it is already specified in controlDict and that should be enough**.

Instead, feed getApplication’s output to runApplication / runParallel:

```sh
runApplication $(getApplication)
```

That saves you quite some steps:

- Matching decomposeParDict.numberOfSubdomains
- Matching controlDict.application
- Invoking MPI
- Keeping log files and whatsit.

### restore0Dir

If you run `potentialFoam`, `setFields` and similar apps, they **will overwrite boundary conditions** that you meticulously prepared in your `0/` directory.

To prevent that from happening, it is wise to keep your boundary conditions in a directory where solvers can’t touch them and copy them to `0/` just before runs. It’s easy to do that by hand but `restore0Dir` will also take care of some other stuff (`#include` directives, parallel runs, …).

Anyway, a line saved is a line less to maintain.

# CleanFunctions

There are more goodies that come in hand with RunFunctions! Type this into shell:

```
cat $WM_PROJECT_DIR/bin/tools/CleanFunctions
```

and take a scroll through the output to find commands such as:

- cleanDynamicCode
- cleanSnappyFiles
- cleanPostProcessing
- cleanCase
- cleanCase0

```sh
#!/bin/sh
cd ${0%/*} || exit 1    # run from this directory

# Source tutorial clean functions
. $WM_PROJECT_DIR/bin/tools/CleanFunctions

cleanCase
```

# References

1. [RunFunctions: a Quick Cheatsheet](https://damogranlabs.com/2020/10/runfunctions-a-quick-cheatsheet/)
2. 