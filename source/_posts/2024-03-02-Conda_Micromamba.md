---
layout: post
title: Conda/Micromamba
categories: [Linux]
tags: [conda, Micromamba]
author: wpsze
---

The easiest way to export a .yml file from a conda environment is to first activate your environment by running:
```sh
conda activate myvenv
```
And then run the following command that generated your .yml file:
```sh
conda env export > list.yml
```
Finally, you can create a new environment using the .yml file, like:
```sh
conda env create --name myvenv --file=list.yml
```

or 
```sh
conda env export | grep -v "^prefix: " > environment.yml
conda env create -f environment.yml
```

Remove environment,
```sh
conda remove -n ENV_NAME --all
```

# Micromamba
Micromamba is a standalone version of Mamba which is an alternative to Conda. This stack delivers a custom anaconda/miniconda type deployment based on the communities conda-forge channel with micromamba as the package manager. For more information visit [micromamba documentation](https://mamba.readthedocs.io/en/latest/user_guide/micromamba.html).

## Installation
```sh
wget micro.mamba.pm/install.sh
chmod +x install.sh
./install.sh
```

## Parsing arguments
if [ -t 0 ] ; then
  printf "Micromamba binary folder? [~/.local/bin] "
  read BIN_FOLDER
  printf "Init shell ($shell)? [Y/n] "
  read INIT_YES
  printf "Configure conda-forge? [Y/n] "
  read CONDA_FORGE_YES
fi

- /home/wpsze/.local/bin
- init shell? Y
- configure-forge? Y

Finally, it will initalize micromambs config on .bashrc

### Create env by given yaml

```
micromamba env create --name newenv --file oldenv.yaml
```
### Source and activate env
```
$ source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
$ micromamba activate venv
```

### activate or execute
Transaction finished

To activate this environment, use:

    micromamba activate venv

Or to execute a single command in this environment, use:

    micromamba run -n venv mycommand

## clone an environment,
As stated in the answer to [Cloning environment with micromamba](https://stackoverflow.com/questions/76884520/cloning-environment-with-micromamba), **--clone** is not an option in micromamba.

Instead, export the environment yaml and create a new environment using the --file flag. Then remove the old environment.

1. export the env dependencies: micromamba env export -n oldenv > oldenv.yaml
2. create a new env with a new name: micromamba create -n newenv --file oldenv.yaml
3. remove the old env: micromamba env remove -n oldenv
4. delete the yaml file: rm oldenv.yaml

## check/add channel

```sh
$ micromamba config list --sources
channels:
  - conda-forge  # '~/.condarc'

$ micromamba config append channels conda-forge
```
