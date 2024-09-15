---
layout: post
title: Spack and Intel OneAPI
categories: [Spack]
tags: [Spack, Intel]
author: wpsze
mathjax: true
mathjax_autoNumber: true
---

# Spack

Spack is a package management tool designed to support multiple versions and configurations of software on a wide variety of platforms and environments. It was designed for large supercomputing centers, where many users and application teams share common installations of software on clusters with exotic architectures, using libraries that do not have a standard ABI. Spack is non-destructive: installing a new version does not break existing installations, so many configurations can coexist on the same system.
Most importantly, Spack is simple. It offers a simple spec syntax so that users can specify versions and configuration options concisely. Spack is also simple for package authors: package files are written in pure Python, and specs allow package authors to maintain a single file for many different builds of the same package.

<https://spack.readthedocs.io/en/latest/>

## Search packages online

> <https://packages.spack.io/>

# Installation

Getting Spack is easy. You can clone it from the github repository using this command:

git clone -c feature.manyFiles=true https://github.com/spack/spack.git
{:.info}

This will create a directory called spack.

Once you have cloned Spack, we recommend sourcing the appropriate script for your shell:

. spack/share/spack/setup-env.sh
{:.info}

```sh
For bash/zsh/sh
. spack/share/spack/setup-env.sh
(add into .bashrc)
For tcsh/csh
source spack/share/spack/setup-env.csh
```

# Intel

IntelOneapi,
https://spack.readthedocs.io/en/latest/build_systems/inteloneapipackage.html

```sh
$ spack env create intel_oneapi 
$ spack env activate intel_oneapi
$ spack info --all intel-oneapi-mpi
$ spack info --all intel-oneapi-compilers
```

try: module load intel-oneapi-mpi/2021.4.0 module load intel-oneapi-compilers/2021.4.0
```sh
$ spack install --add intel-oneapi-compilers@2021.4.0
# Add the compilers to your compilers.yaml so spack can use them:
$ spack compiler add `spack location -i intel-oneapi-compilers@2021.4.0`/compiler/latest/linux/bin/intel64
$ spack compiler add `spack location -i intel-oneapi-compilers@2021.4.0`/compiler/latest/linux/bin
# Verify that the compilers are available:
$ spack compiler list or spack location -i intel-oneapi-compilers@2021.4.0
```

Check,

```sh
which mpiicc && mpiicc --version
which mpiifort && mpiifort --version
which mpiicpc && mpiicpc --version
which icc && icc --version
which ifort && ifort --version
```

one more step, install curl

```sh
$ spack info --all curl
$ spack install --add curl%intel
```

# Remark

## spack info --all intel-oneapi-mpi

```sh
$ spack info --all intel-oneapi-mpi

Package:   intel-oneapi-mpi

Description:
    Intel MPI Library is a multifabric message-passing library that
    implements the open-source MPICH specification. Use the library to
    create, maintain, and test advanced, complex applications that perform
    better on high-performance computing (HPC) clusters based on Intel
    processors. LICENSE INFORMATION: By downloading and using this software,
    you agree to the terms and conditions of the software license agreements
    at https://intel.ly/393CijO.

Homepage: https://software.intel.com/content/www/us/en/develop/tools/oneapi/components/mpi-library.html

Maintainers: @rscohn2

Externally Detectable: 
    False

Tags: 
    None

Preferred version:  
    2021.13.1    https://registrationcenter-download.intel.com/akdlm/IRC_NAS/364c798c-4cad-4c01-82b5-e1edd1b476af/l_mpi_oneapi_p_2021.13.1.769_offline.sh

Safe versions:  
    2021.13.1    https://registrationcenter-download.intel.com/akdlm/IRC_NAS/364c798c-4cad-4c01-82b5-e1edd1b476af/l_mpi_oneapi_p_2021.13.1.769_offline.sh
    2021.13.0    https://registrationcenter-download.intel.com/akdlm/IRC_NAS/9f84e1e8-11b2-4bd1-8512-3e3343585956/l_mpi_oneapi_p_2021.13.0.719_offline.sh
    2021.12.1    https://registrationcenter-download.intel.com/akdlm/IRC_NAS/56b2dd0e-954d-4330-b0a7-b22992f7e6b7/l_mpi_oneapi_p_2021.12.1.8_offline.sh
    2021.12.0    https://registrationcenter-download.intel.com/akdlm/IRC_NAS/749f02a5-acb8-4bbb-91db-501ff80d3f56/l_mpi_oneapi_p_2021.12.0.538_offline.sh
    2021.11.0    https://registrationcenter-download.intel.com/akdlm//IRC_NAS/2c45ede0-623c-4c8e-9e09-bed27d70fa33/l_mpi_oneapi_p_2021.11.0.49513_offline.sh
    2021.10.0    https://registrationcenter-download.intel.com/akdlm/IRC_NAS/4f5871da-0533-4f62-b563-905edfb2e9b7/l_mpi_oneapi_p_2021.10.0.49374_offline.sh
    2021.9.0     https://registrationcenter-download.intel.com/akdlm/IRC_NAS/718d6f8f-2546-4b36-b97b-bc58d5482ebf/l_mpi_oneapi_p_2021.9.0.43482_offline.sh
    2021.8.0     https://registrationcenter-download.intel.com/akdlm/IRC_NAS/19131/l_mpi_oneapi_p_2021.8.0.25329_offline.sh
    2021.7.1     https://registrationcenter-download.intel.com/akdlm/IRC_NAS/19010/l_mpi_oneapi_p_2021.7.1.16815_offline.sh
    2021.7.0     https://registrationcenter-download.intel.com/akdlm/IRC_NAS/18926/l_mpi_oneapi_p_2021.7.0.8711_offline.sh
    2021.6.0     https://registrationcenter-download.intel.com/akdlm/IRC_NAS/18714/l_mpi_oneapi_p_2021.6.0.602_offline.sh
    2021.5.1     https://registrationcenter-download.intel.com/akdlm/IRC_NAS/18471/l_mpi_oneapi_p_2021.5.1.515_offline.sh
    2021.5.0     https://registrationcenter-download.intel.com/akdlm/IRC_NAS/18370/l_mpi_oneapi_p_2021.5.0.495_offline.sh
    2021.4.0     https://registrationcenter-download.intel.com/akdlm/IRC_NAS/18186/l_mpi_oneapi_p_2021.4.0.441_offline.sh
    2021.3.0     https://registrationcenter-download.intel.com/akdlm/IRC_NAS/17947/l_mpi_oneapi_p_2021.3.0.294_offline.sh
    2021.2.0     https://registrationcenter-download.intel.com/akdlm/IRC_NAS/17729/l_mpi_oneapi_p_2021.2.0.215_offline.sh
    2021.1.1     https://registrationcenter-download.intel.com/akdlm/IRC_NAS/17397/l_mpi_oneapi_p_2021.1.1.76_offline.sh

Deprecated versions:  
    None

Variants:
    build_system [generic]            generic
        Build systems supported by the package
    classic-names [false]             false, true
        Use classic compiler names, e.g mpiicc instead of mpiicx
    envmods [true]                    false, true
        Toggles environment modifications
    external-libfabric [false]        false, true
        Enable external libfabric dependency
    generic-names [false]             false, true
        Use generic names, e.g mpicc instead of mpiicx
    ilp64 [false]                     false, true
        Build with ILP64 support

Installation Phases:
    install

Build Dependencies:
    None

Link Dependencies:
    libfabric

Run Dependencies:
    libfabric

Virtual Packages: 
    intel-oneapi-mpi provides mpi@:3.1

Available Build Phase Test Methods:
    None

Available Install Phase Test Methods:
    None

Stand-Alone/Smoke Test Methods:
    Mpi.test_mpi_hello

Licenses: 
    https://intel.ly/393CijO
```