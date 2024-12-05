---
layout: post
title: "CFD | OpenFOAM | In parallel"
categories: [CFD]
tags: [OpenFOAM,HPC]
author: wpsze
date: 2024-12-04 15:42:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/fFT42Yk.png
banner_img: https://i.imgur.com/fFT42Yk.png
---

# Running applications in parallel

{% note danger %}
Both **domain decomposition and reconstruction processes** can only be done on a **single core**.
{% endnote %}

This section describes how to run OpenFOAM in parallel on distributed processors. 

{% note danger %}
The method of parallel computing used by OpenFOAM is known as **domain decomposition**, in which the geometry and associated fields are broken into pieces and allocated to separate processors for solution. 
{% endnote %}

The process of parallel computation involves: decomposition of mesh and fields; running the application in parallel; and, post-processing the decomposed case as described in the following sections. The parallel running uses the public domain **openMPI** implementation of the standard message passing interface (MPI).

## Decomposition of mesh and initial field data

- The mesh and fields are decomposed using the decomposePar utility.
- The geometry and fields are broken up according to a set of parameters specified in a dictionary named `decomposeParDict` that must be located in the `system` directory of the case of interest.

An example `decomposeParDict` dictionary can be copied from the `interFoam/damBreak/damBreak` tutorial if the user requires one; the dictionary entries within it are reproduced below:

```c++
17 numberOfSubdomains 8;
18
19 method          simple;
20
21 simpleCoeffs
22 {
23     n           (4 1 2);
24 }
25
26 // ******************************* //
```

!["simple" geometric decomposition](https://i.imgur.com/rxTVXxT.png){width=600}

The user has a choice of four methods of decomposition, specified by the method keyword as described below.

- `simple`
  - Simple geometric decomposition in which the domain is split into pieces by direction.
- `hierarchical`
  - Hierarchical geometric decomposition which is the same as simple except the user specifies the order in which the directional split is done.
- `scotch`
  - Scotch decomposition which requires no geometric input from the user and attempts to minimise the number of processor boundaries. The user can specify a weighting for the decomposition between processors, through an optional processorWeights keyword which can be useful on machines with differing performance between processors. There is also an optional keyword entry strategy that controls the decomposition strategy through a complex string supplied to Scotch. For more information, see the source code file: `$FOAM_SRC/parallel/decompose/scotchDecomp/scotchDecomp.C`
- `manual`
  - Manual decomposition, where the user directly specifies the allocation of each cell to a particular processor.

For each method there are a set of coefficients specified in a sub-dictionary of `decompositionDict`, named `<method>Coeffs` as shown in the dictionary listing.

### Docs and github code:

- Ref: [The decomposeParDict | OpenFOAM: User Guide](https://www.openfoam.com/documentation/guides/latest/doc/openfoam-guide-parallel.html)
- [decomposeParDict code](https://github.com/OpenFOAM/OpenFOAM-4.x/blob/master/applications/utilities/parallelProcessing/decomposePar/decomposeParDict)

|   **Name**   |         **Class**        |
|:-------------|:-------------------------|
| none         | Foam::noDecomp           |
| manual       | Foam::manualDecomp       |
| simple       | Foam::simpleGeomDecomp   |
| hierarchical | Foam::hierarchGeomDecomp |
| kahip        | Foam::kahipDecomp        |
| metis        | Foam::metisDecomp        |
| scotch       | Foam::scotchDecomp       |
| structured   | Foam::structuredDecomp   |
| multiLevel   | Foam::multiLevelDecomp   |

If using metis,

```c++
numberOfSubdomains   N;
method               metis;

metisCoeffs
{
    method           k-way;  // recursive or k-way
    options          ( ...);
    processorWeights ( ... );
}
```

error,

```console
--> FOAM FATAL ERROR: 
You are trying to use metis but do not have the metisDecomp library loaded.
This message is from the dummy metisDecomp stub library instead.

Please install metis and make sure that libmetis.so is in your LD_LIBRARY_PATH.
The metisDecomp library can then be built from $FOAM_SRC/parallel/decompose/metisDecomp and dynamically loading or linking this library will add metis as a decomposition method.
Please be aware that there are license restrictions on using Metis.

    From function Foam::List<signed int> Foam::metisDecomp::decompose(const Foam::polyMesh &, const Foam::Field<Foam::Vector<double>> &, const Foam::Field<double> &)
    in file dummyMetisDecomp.C at line 93.

FOAM exiting
```

## run.sh script

```sh
## Loading OpenFOAM environment

# NP=Number of cores available in the system.
export NP=$(nproc)

# Create decomposeParDict file to support $NP number of processes.
cp system/decomposeParDict.6 system/decomposeParDict.${NP}

# Change the numberOfSubdomains in decomposeParDict to create $NP number of subdomains
sed -i "s/numberOfSubdomains.*/numberOfSubdomains ${NP};/" system/decomposeParDict.${NP}

#Change the grid size for $NP, here setting grid size for 192 core system (48*4*1)
sed -i "s/ n           (3 2 1)/ n           (48 4 1);/" system/decomposeParDict.${NP}

#Default Mesh size is very small (0.35M cells), scaling up the Mesh size to 20M cells
sed -i "s/(20 8 8)/(100 40 40)/" system/blockMeshDict

#To use the file "system/decomposeParDict.${NP}" in Allrun (run script)
sed -i "s#system/decomposeParDict.6#system/decomposeParDict.${NP}#" Allrun

#To run the benchmark
#Allrun script executes a combination multiple steps
#like Mesh creation, Setting the initial fields, Running the application, Postprocessing etc.
./Allrun
```

## `decomposePar`

The `decomposePar` utility is executed in the normal manner by typing

```sh
decomposePar
```

On completion, a set of subdirectories will have been created, one for each processor, in the case directory. The directories are named `processorN` represents a processor number and contains a time directory, containing the decomposed field descriptions, and a `constant/polyMesh` directory containing the decomposed mesh description.

## Example, `motorBike`

- `./Allrun`

```sh
#!/bin/sh
cd ${0%/*} || exit 1    # Run from this directory

# Source tutorial run functions
. $WM_PROJECT_DIR/bin/tools/RunFunctions

# Copy motorbike surface from resources directory
cp $FOAM_TUTORIALS/resources/geometry/motorBike.obj.gz constant/triSurface/
runApplication surfaceFeatures

runApplication blockMesh

runApplication decomposePar -copyZero
runParallel snappyHexMesh -overwrite

runParallel patchSummary
runParallel potentialFoam
runParallel $(getApplication)

runApplication reconstructParMesh -constant
runApplication reconstructPar -latestTime
```

and outputs are,

```console

Cleaning /EM/wpsze/openfoam/openfoam8/run/motorBike case
Running surfaceFeatures on /EM/wpsze/openfoam/openfoam8/run/motorBike
Running blockMesh on /EM/wpsze/openfoam/openfoam8/run/motorBike
Running decomposePar on /EM/wpsze/openfoam/openfoam8/run/motorBike
Running snappyHexMesh in parallel on /EM/wpsze/openfoam/openfoam8/run/motorBike using 6 processes
Running patchSummary in parallel on /EM/wpsze/openfoam/openfoam8/run/motorBike using 6 processes
Running potentialFoam in parallel on /EM/wpsze/openfoam/openfoam8/run/motorBike using 6 processes
Running simpleFoam in parallel on /EM/wpsze/openfoam/openfoam8/run/motorBike using 6 processes
Running reconstructParMesh on /EM/wpsze/openfoam/openfoam8/run/motorBike
Running reconstructPar on /EM/wpsze/openfoam/openfoam8/run/motorBike

real	5m5.399s
user	27m17.537s
sys	0m22.050s
=====================
Total of 311 seconds elapsed for process
Total of 5 minutes elapsed for process
=====================
```

# Post-processing parallel processed cases

When post-processing cases that have been run in parallel the user can:

- reconstruct the mesh and field data to recreate the complete domain and fields, which can be post-processed as normal;
- post-process each segment of decomposed domain individually; or
- use ParaView via the option paraFoam -vtk and select the decomposedCase from the GUI whereby the case will be assembled internally

## Reconstructing mesh and data

The `reconstructPar` utility performs such a reconstruction by executing the command:

```sh
reconstructPar
```

Executing `reconstructPar` without any additional options will process all stored time directories.

# Issues

- [OpenFOAM在并行运算结束后 能否并行reconstructPar？或者有没有其他方法能够加快reconstructPar？](https://cfd-china.com/topic/3170/openfoam%E5%9C%A8%E5%B9%B6%E8%A1%8C%E8%BF%90%E7%AE%97%E7%BB%93%E6%9D%9F%E5%90%8E-%E8%83%BD%E5%90%A6%E5%B9%B6%E8%A1%8Creconstructpar-%E6%88%96%E8%80%85%E6%9C%89%E6%B2%A1%E6%9C%89%E5%85%B6%E4%BB%96%E6%96%B9%E6%B3%95%E8%83%BD%E5%A4%9F%E5%8A%A0%E5%BF%ABreconstructpar)
  - 最近跑了几个网格总数比较大的案例（大概500w~1000w），并行计算结束后，执行了reconstructPar进行重构，但是重构时间特别长。
  - 试试这个 `mpirun -np 2 redistributePar -reconstruct -parallel`
  - 印象中以前看一个培训材料说分块和重构都只能单核
  - openfoam的decomposePar、reconstructPar是个老大难。这个应该需要大幅度的fund他们才会解决。目前他们对这方面还没有迫切的处理需求。目前openfoam针对reconstructPar这面的处理方式是后处理也是并行来搞，paraview直接看decompose的数据，各种postProcess也都是并行来。针对decomposePar慢的问题，openfoam这面是画网格snappyHexMesh直接并行来。所以我这面尝试处理2个亿网格，从生成到计算，也还可以。但是如果是第三方生成的2个亿网格，需要decomposePar，那可就慢了

# References

1. [3.2 Running applications in parallel](https://www.openfoam.com/documentation/user-guide/3-running-applications/3.2-running-applications-in-parallel)
2. [Instructions_for_OpenFOAM_2024_spring](https://hprc.tamu.edu/files/training/2024/Spring/Instructions_for_OpenFOAM_2024_spring.pdf)
3. [Build OpenFOAM using Spack](https://www.amd.com/zh-tw/developer/zen-software-studio/applications/spack/hpc-applications-openfoam.html)
4. [The decomposeParDict | OpenFOAM: User Guide](https://www.openfoam.com/documentation/guides/latest/doc/openfoam-guide-parallel.html)