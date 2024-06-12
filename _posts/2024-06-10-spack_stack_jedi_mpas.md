---
layout: article
title: Spack-Stack (MPAS-JEDI)
categories: [Spack]
tags: [Spack, Spack-Stack, MPAS, DA]
author: wpsze
mathjax: true
mathjax_autoNumber: true
---
# spack-stack

Spack-stack is a framework for installing software libraries to support NOAA's Unified Forecast System (UFS) applications and the Joint Effort for Data assimilation Integration (JEDI) coupled to several Earth system prediction models (MPAS, NEPTUNE, UM, FV3, GEOS, UFS).
 
Spack-stack provides a practical framework for setting up and managing software libraries that support the Unified Forecast System (UFS) and Joint Effort for Data assimilation Integration (JEDI) projects, along with several other Earth system prediction models, such as the Model for Prediction Across Scales (MPAS), the Navy Environmental Prediction System Utilizing a Nonhydrostatic Engine (NEPTUNE), the Unified Model (UM), and the Goddard Earth Observing System (GEOS). This project results from collaboration among the Joint Center for Satellite Data Assimilation (JCSDA), the National Oceanic and Atmospheric Administration’s (NOAA’s) Environmental Modeling Center (EMC) and Earth Prediction Innovation Center (EPIC), and the U.S. Naval Research Laboratory (NRL). Spack-stack utilizes the Spack package manager, originally developed at Lawrence Livermore National Laboratory to cater to scientific high-performance computing (HPC) workflows.

[SPACK-STACK 1.5.0](https://www.jcsda.org/news-blog/2023/10/2/jedi-spack-stack-150-the-new-standard)
> That started changing in 2021, when JCSDA and NOAA EMC began exploring Spack as an option for creating a single software stack that could be used for both the JEDI DA software environment and the Unified Forecast System (UFS) ecosystem.

[github spack-stack](https://github.com/JCSDA/spack-stack)

[spack-stack.readthedocs.io](https://spack-stack.readthedocs.io/en/latest/#)

[Spack-stack](https://epic.noaa.gov/spack-stack/)

[ONE STACK TO BUILD THEM ALL: THE NOAA-EMC/JCSDA SPACK-STACK (PPT)](https://www.google.com/search?q=ONE+STACK+TO+BUILD+THEM+ALL%3A+THE+NOAA-EMC%2FJCSDA+SPACK-STACK&rlz=1C5CHFA_enHK1003HK1003&sourceid=chrome&ie=UTF-8)

[spack-stack: A reproducible, many-platform software stack for operational weather applications (PPT)](https://epic.noaa.gov/wp-content/uploads/2023/08/UIFCW-2023-Thu-17.-RICHERT_spack-stack-UIFCW-July-2023.pdf)

spack-stack is a set of Spack configurations and extensions
{:.info}

spack-stack is mainly a collection of Spack configuration files, but provides a Spack extension to simplify the installation process
{:.info}

# New spack-stack directory structure

To generate a new spack-stack directory structure, 

run 'git clone --recurse-submodules https://github.com/JCSDA/spack-stack',
{:.info}

optionally with, e.g., '-b release/1.4.1' to specify the version

source \<path to spack-stack directory\>/setup.sh
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

## MPAS-JDEI

Ref:[Welcome to the MPAS-JEDI tutorial practice guide](https://www2.mmm.ucar.edu/projects/mpas-jedi/tutorial/202310NCU/)

For example, if default is gcc@8.5.0.

```sh
$ git clone -b release/1.5.1 --recurse-submodules https://github.com/JCSDA/spack-stack

$ git status
On branch release/1.5.1
Your branch is up to date with 'origin/release/1.5.1'.

nothing to commit, working tree clean

$ source setup.sh
Setting environment variable SPACK_STACK_DIR to /home/wpsze/spack-stack/spack-stack
Sourcing spack environment /home/wpsze/spack-stack/spack-stack/spack/share/spack/setup-env.sh

$ spack stack create env --site linux.default --name jedi_gccv_mylinux
Configuring basic directory information ...
  ... script directory: /home/wpsze/spack-stack/spack-stack/spack/lib/jcsda-emc/spack-stack/stack
  ... base directory: /home/wpsze/spack-stack/spack-stack/spack/lib/jcsda-emc/spack-stack
  ... spack directory: /home/wpsze/spack-stack/spack-stack/spack
Creating environment from command-line args
Successfully wrote environment at /home/wpsze/spack-stack/spack-stack/envs/jedi_gccv_mylinux/spack.yaml

WARNING! User umask is neither 0022 nor 0027, check before proceeding

==> Created environment /home/wpsze/spack-stack/spack-stack/envs/jedi_gccv_mylinux

$ spack env activate envs/jedi_gccv_mylinux

$ spack find
==> In environment /home/wpsze/spack-stack/spack-stack/envs/jedi_gccv_mylinux
==> No root specs
==> 0 installed packages

## MPAS-JEDI
$ spack add ecbuild eigen udunits gsl-lite 
$ spack concretize
...ecbuild@3.7.2%gcc@8.5.0...
$ spack find
==> In environment /home/wpsze/spack-stack/spack-stack/envs/jedi_gccv_mylinux
==> Root specs
ecbuild   eigen   gsl-lite   udunits 

==> 0 installed packages

$ spack add hdf5^mpich parallelio^mpich
$ spack concretize
...hdf5@1.14.0%gcc@8.5.0...mpich@4.1.1%gcc@8.5.0...
$ spack find
==> In environment /home/wpsze/spack-stack/spack-stack/envs/jedi_gccv_mylinux
==> Root specs
ecbuild   eigen   gsl-lite   hdf5   parallelio   udunits 

==> 0 installed packages

$ spack add git-lfs environment-modules
$ spack concretize

$ spack add ecmwf-atlas@0.31.1^mpich
$ spack concretize

$ spack install
$ spack module tcl refresh
$ spack stack setup-meta-modules
```

Error:
> Error: eigen-3.4.0-t4jxg3j727pxxsr75zstl2eblmkzapfz: timeout: The read operation timed out
>> rerun: $ spack install

> ==> Installing krb5-1.20.1-muktx57itqz6x2z74afwncpub2higpei
> msgfmt: error while opening "en_US.mo" for writing: Permission denied
> msgfmt: error while opening "de.mo" for writing: Permission denied

> Error: git-lfs-3.3.0-6ilwlfqmm7ud5crbugmcskju5gtkgk4j: Package was not installed

source env,

```sh
#source ${SPACK_DIR}/setup.sh
#spack env activate ${SPACK_ENV}
```

## mpas_bundle

```sh
$ mkdir mpas_bundle_v2
$ cd mpas_bundle_v2
$ git clone -b release/2.0.0 https://github.com/JCSDA/mpas-bundle.git code

$ mkdir build
$ cd build

$ spack env activate envs/jedi_gccv_mylinux
$ module list
```

MPAS-JEDI uses cmake to automatically download the code from various github repositories, listed in CMakeLists.txt under ~code. Now type the command below under the build directory:

```sh
$ git lfs install
$ cmake ../code
```

After it completes, **you will see the actual source code of various reporitories (e.g., oops, saber, ufo, ioda, crtm, mpas-jedi, and MPAS) is now under the code directory**. Meanwhile, **Makefile files to build executables are generated under the build directory**. Now it is ready to compile MPAS-JEDI under 'build' using the standard make. However, it is NOT recommended to compile the code on the login node at the same time.

```sh
$ make -j14
```

This requests xx min walltime of a login node with 14 cores.
{:.info}

Once we reach 100% of the compilation, we can check mpas-jedi related executables:

```sh
$ ls bin/mpas*
```

The last step is to ensure that the code was compiled properly by running the MPAS-JEDI ctests, with two lines of simple command:

```sh
$ cd mpas-jedi
$ ctest
```

The output to the terminal should look as following:
```sh
xxx
```

To determine if a test passes or fails, *a comparison of the test log and reference file is done internally taking as reference a tolerance value*. This tolerance is specified via YAML file. These files can be found under **mpas-jedi/test/testoutput** in the build folder:

```sh
xxx
```

## Converting NCEP BUFR observation into IODA-HDF5 format

```sh
$ cd mpas_jedi_tutorial 
$ git clone https://github.com/NCAR/obs2ioda
$ cd obs2ioda/obs2ioda-v2/src/
```

NCEP BUFR library (https://github.com/NOAA-EMC/NCEPLIBS-bufr) is required to compile **obs2ioda-v2.x**. Edit **obs2ioda-v2/src/Makefile** to set proper **BUFR_LIB** before make. Here we have pre-build BUFR_LIB on Taiwania-3 and we put the pre-build BUFR_LIB path in Makefile.

### NCEP BUFR library

```sh
$ git clone https://github.com/NOAA-EMC/NCEPLIBS-bufr

$ mkdir build && cd build
$ cmake -DCMAKE_INSTALL_PREFIX=path1 -DMASTER_TABLE_DIR=path2 ..
$ make -j4
$ ctest
$ make install
```
Both **path1** and **path2** may be full or relative pathnames on the system, up to a maximum of 90 characters each.

Installation of the library and utilities will be under **path1**. Installation of the master BUFR tables will be under **path2**, or *under path1 if -DMASTER_TABLE_DIR=path2 is omitted from the above cmake command*.

If Python interoperability is desired, -DENABLE_PYTHON=ON can also be added to the above cmake command. However, version 3 of Python must be installed and available on the system.

### Go back to IODA

Then compile the converter by using make command:

```sh
$ make
```
If the compilation is successful, the executable file **obs2ioda-v2.x** will be generated.

