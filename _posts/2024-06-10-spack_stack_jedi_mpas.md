---
layout: article
title: Spack-Stack
categories: [Spack]
tags: [Spack, Spack-Stack]
author: wpsze
mathjax: true
mathjax_autoNumber: true
---
# spack-stack

Spack-stack is a framework for installing software libraries to support NOAA's Unified Forecast System (UFS) applications and the Joint Effort for Data assimilation Integration (JEDI) coupled to several Earth system prediction models (MPAS, NEPTUNE, UM, FV3, GEOS, UFS).
 
Spack-stack provides a practical framework for setting up and managing software libraries that support the Unified Forecast System (UFS) and Joint Effort for Data assimilation Integration (JEDI) projects, along with several other Earth system prediction models, such as the Model for Prediction Across Scales (MPAS), the Navy Environmental Prediction System Utilizing a Nonhydrostatic Engine (NEPTUNE), the Unified Model (UM), and the Goddard Earth Observing System (GEOS). This project results from collaboration among the Joint Center for Satellite Data Assimilation (JCSDA), the National Oceanic and Atmospheric Administration’s (NOAA’s) Environmental Modeling Center (EMC) and Earth Prediction Innovation Center (EPIC), and the U.S. Naval Research Laboratory (NRL). Spack-stack utilizes the Spack package manager, originally developed at Lawrence Livermore National Laboratory to cater to scientific high-performance computing (HPC) workflows.

[github spack-stack](https://github.com/JCSDA/spack-stack)

[spack-stack.readthedocs.io](https://spack-stack.readthedocs.io/en/latest/#)

[Spack-stack](https://epic.noaa.gov/spack-stack/)

[ONE STACK TO BUILD THEM ALL: THE NOAA-EMC/JCSDA SPACK-STACK](https://www.google.com/search?q=ONE+STACK+TO+BUILD+THEM+ALL%3A+THE+NOAA-EMC%2FJCSDA+SPACK-STACK&rlz=1C5CHFA_enHK1003HK1003&sourceid=chrome&ie=UTF-8)

[SPACK-STACK 1.5.0](https://www.jcsda.org/news-blog/2023/10/2/jedi-spack-stack-150-the-new-standard)
> That started changing in 2021, when JCSDA and NOAA EMC began exploring Spack as an option for creating a single software stack that could be used for both the JEDI DA software environment and the Unified Forecast System (UFS) ecosystem.




# New spack-stack directory structure

To generate a new spack-stack directory structure, 

run 'git clone --recurse-submodules https://github.com/JCSDA/spack-stack',
{:.info}

optionally with, e.g., '-b release/1.4.1' to specify the version

. \<path to spack-stack directory\>/setup.sh
{:.info}

# New environment

```sh
$ spack stack create env --site <site name> --template <template name> --name <environment name>
$ cd ${SPACK_STACK_DIR}/envs/<environment name>/
$ spack env activate .

# If not using an existing site configuration, you may wish to modify config files and/or populate them using commands
# such as 'spack external find' and 'spack compiler find'.
# See https://spack-tutorial.readthedocs.io/en/latest/tutorial_configuration.html

$ spack concretize 2>&1 | tee log.concretize
$ spack install [--verbose] [--fail-fast] 2>&1 | tee log.install
$ spack module lmod refresh
$ spack stack setup-meta-modules
```

For example,

```sh
$ spack stack create env --site linux.default --name jedi-gccv930.mylinux
$ spack env activate envs/jedi-gccv930.mylinux
$ export SPACK_SYSTEM_CONFIG_PATH="$PWD/envs/jedi-gccv930.mylinux/site"
```
