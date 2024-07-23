---
layout: post
title: Spack-Stack (MPAS-JEDI)
categories: [Spack]
tags: [Spack, Spack-Stack, MPAS, DA]
author: wpsze
mathjax: true
mathjax_autoNumber: true
---

* content
{:toc}

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

run 
```sh
git clone --recurse-submodules https://github.com/JCSDA/spack-stack
```

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

$ cd spack-stack

$ git status
On branch release/1.5.1
Your branch is up to date with 'origin/release/1.5.1'.

nothing to commit, working tree clean

$ source setup.sh

$ spack stack create env --site linux.default --name jedi_gccv850_linux
Configuring basic directory information ...
  ... script directory: /home/wpsze/spack-stack/spack-stack/spack/lib/jcsda-emc/spack-stack/stack
  ... base directory: /home/wpsze/spack-stack/spack-stack/spack/lib/jcsda-emc/spack-stack
  ... spack directory: /home/wpsze/spack-stack/spack-stack/spack
Creating environment from command-line args
Successfully wrote environment at /home/wpsze/spack-stack/spack-stack/envs/jedi_gccv850_linux/spack.yaml

WARNING! User umask is neither 0022 nor 0027, check before proceeding

==> Created environment /home/wpsze/spack-stack/spack-stack/envs/jedi_gccv850_linux

$ spack env activate envs/jedi_gccv850_linux

$ spack find
==> In environment /home/wpsze/spack-stack/spack-stack/envs/jedi_gccv850_linux
==> No root specs
==> 0 installed packages


## MPAS-JEDI
$ spack add ecbuild eigen udunits gsl-lite environment-modules
$ spack add mpich
$ spack add hdf5^mpich parallelio^mpich
$ spack add ecmwf-atlas@0.31.1^mpich

$ spack install

# all are successful.

 $ spack find
==> In environment /home/wpsze/spack-stack/spack-stack/envs/jedi_gccv850_linux
==> Root specs
ecbuild   ecmwf-atlas@0.31.1   eigen   environment-modules   gsl-lite   hdf5   mpich   parallelio   udunits 

==> Installed packages
-- linux-rocky8-haswell / gcc@8.5.0 -----------------------------
autoconf@2.69                       diffutils@3.9              fiat@1.2.0       libfabric@1.18.1   m4@1.4.19               pigz@2.7                util-macros@1.19.3
automake@1.16.5                     ecbuild@3.7.2              findutils@4.9.0  libffi@3.4.4       mpich@4.1.1             pkg-config@0.29.2       xz@5.4.1
berkeley-db@18.1.40                 eckit@1.23.1               flex@2.6.3       libiconv@1.17      ncurses@6.4             python@3.10.8           yaksa@0.2
bison@3.8.2                         ecmwf-atlas@0.31.1         gdbm@1.23        libmd@1.0.4        netcdf-c@4.9.2          readline@8.2            zlib@1.2.13
boost@1.78.0                        ectrans@1.2.0              gettext@0.21.1   libpciaccess@0.17  netcdf-fortran@4.6.0    snappy@1.1.10           zstd@1.5.2
bzip2@1.0.8                         eigen@3.4.0                gmake@4.4.1      libsigsegv@2.14    openblas@0.3.19         sqlite@3.42.0
c-blosc@1.21.4                      environment-modules@5.3.1  gsl-lite@0.37.0  libtool@2.4.7      openssl@1.1.1u          tar@1.34
ca-certificates-mozilla@2023-01-10  expat@2.5.0                hdf5@1.14.0      libxcrypt@4.4.35   parallel-netcdf@1.12.2  tcl@8.6.12
cmake@3.23.1                        fckit@0.10.1               libaec@1.0.6     libxml2@2.10.3     parallelio@2.5.10       udunits@2.2.28
curl@8.1.2                          fftw@3.3.10                libbsd@0.11.7    lz4@1.9.4          perl@5.38.0             util-linux-uuid@2.38.1
==> 65 installed packages

$ spack module tcl refresh
==> You are about to regenerate tcl module files for:

-- linux-rocky8-haswell / gcc@8.5.0 -----------------------------
kmosf2b autoconf@2.69                       owkksle expat@2.5.0       o5fm2ib libpciaccess@0.17       zzdldzz pkg-config@0.29.2
3zzqcqa automake@1.16.5                     z5frtgj fckit@0.10.1      bcnervk libsigsegv@2.14         vu7mzai python@3.10.8
rksajpa berkeley-db@18.1.40                 dyhv6t6 fftw@3.3.10       oi52wco libtool@2.4.7           nbav53o readline@8.2
k4yohcw bison@3.8.2                         abwhtim fiat@1.2.0        dh3pgna libxcrypt@4.4.35        awehs3f snappy@1.1.10
3zcm66d boost@1.78.0                        th5cmuq findutils@4.9.0   56a6xgk libxml2@2.10.3          xmupg6k sqlite@3.42.0
yd6i5au bzip2@1.0.8                         hjgtmaq flex@2.6.3        rwcgkls lz4@1.9.4               vetdjwa tar@1.34
at3s3ei c-blosc@1.21.4                      lisu52h gdbm@1.23         uwicvjw m4@1.4.19               m3xviog tcl@8.6.12
edma3gu ca-certificates-mozilla@2023-01-10  wzfhnm7 gettext@0.21.1    jefb5lu mpich@4.1.1             uadvmuz udunits@2.2.28
h5p5rs7 cmake@3.23.1                        ba4vudd gmake@4.4.1       dzxkixc ncurses@6.4             wdfeec7 util-linux-uuid@2.38.1
jf7usug curl@8.1.2                          t4udhou gsl-lite@0.37.0   6d2z7tb netcdf-c@4.9.2          evqjofh util-macros@1.19.3
32jm5xp diffutils@3.9                       fja7tdm hdf5@1.14.0       3ltfgi2 netcdf-fortran@4.6.0    72t2yvx xz@5.4.1
pzidwlf ecbuild@3.7.2                       vbf3elz libaec@1.0.6      u5n5zri openblas@0.3.19         iyrk2i7 yaksa@0.2
wov4yva eckit@1.23.1                        65boafj libbsd@0.11.7     mgcs3tx openssl@1.1.1u          x54v3wn zlib@1.2.13
v45ibbv ecmwf-atlas@0.31.1                  63u4otp libfabric@1.18.1  56vql6d parallel-netcdf@1.12.2  bteldyo zstd@1.5.2
kb6u64z ectrans@1.2.0                       frf3blg libffi@3.4.4      efx7bmd parallelio@2.5.10
t4jxg3j eigen@3.4.0                         pnmrtbc libiconv@1.17     se4zvg5 perl@5.38.0
om7l5xf environment-modules@5.3.1           vgtevgl libmd@1.0.4       l3xczgm pigz@2.7

==> Do you want to proceed? [y/n] y
==> Regenerating tcl module files

$ spack stack setup-meta-modules
...
Metamodule generation completed successfully in /home/wpsze/spack-stack/spack-stack/envs/jedi_gccv850_linux/install/modulefiles/Core
```

Error:
> Error: eigen-3.4.0-t4jxg3j727pxxsr75zstl2eblmkzapfz: timeout: The read operation timed out
>> rerun: $ spack install

> ==> Installing krb5-1.20.1-muktx57itqz6x2z74afwncpub2higpei
> msgfmt: error while opening "en_US.mo" for writing: Permission denied
> msgfmt: error while opening "de.mo" for writing: Permission denied
>> No install git-lfs

> Error: git-lfs-3.3.0-6ilwlfqmm7ud5crbugmcskju5gtkgk4j: Package was not installed
>> `$ micromamba activate git-lfs`
>> `$ micromamba install git-lfs`



Remark:
Git Large File Storage (LFS) replaces large files such as audio samples, videos, datasets, and graphics with text pointers inside Git, while storing the file contents on a remote server like GitHub.com or GitHub Enterprise.

source env,

```sh
#source ${SPACK_DIR}/setup.sh
#spack env activate ${SPACK_ENV}
```

## mpas_bundle

```sh
$ source spack/share/spack/setup-env.sh
$ source spack-stack/setup.sh
$ spack env activate spack-stack/envs/jedi_gcc_linux
$ spack find

$ mkdir mpas_bundle_v2
$ cd mpas_bundle_v2
$ git clone -b release/2.0.0 https://github.com/JCSDA/mpas-bundle.git code

$ mkdir build
$ cd build

# (no need) $ module list
```

MPAS-JEDI uses cmake to automatically download the code from various github repositories, listed in CMakeLists.txt under ~code. Now type the command below under the build directory:

```sh
$ git lfs install
```

and then,
```sh
$ spack load ecbuild@3.7.2
$ spack load eigen@3.4.0 gsl-lite@0.37.0 hdf5@1.14.0 udunits@2.2.28 netcdf-c@4.9.2 netcdf-fortran@4.6.0 parallel-netcdf@1.12.2 parallelio@2.5.10
$ spack load openblas@0.3.19
$ spack load eckit@1.23.1
$ spack load fckit@0.10.1
$ spack load ecmwf-atlas@0.31.1

$ cmake ../code

......
-- Configuring done
-- Generating done
-- Build files have been written to: /home/wpsze/mpas_bundle_v2/build
```

After it completes, **you will see the actual source code of various reporitories (e.g., oops, saber, ufo, ioda, crtm, mpas-jedi, and MPAS) is now under the code directory**. Meanwhile, **Makefile files to build executables are generated under the build directory**. Now it is ready to compile MPAS-JEDI under 'build' using the standard make. However, it is NOT recommended to compile the code on the login node at the same time.

```sh
$ make -j14

.......
[100%]
```

This requests xx min walltime of a login node with 14 cores.
{:.info}

Once we reach 100% of the compilation, we can check mpas-jedi related executables:

```sh
$ ls bin/mpas*

bin/mpas_atmosphere                       bin/mpasjedi_forecast.x
bin/mpas_atmosphere_build_tables          bin/mpasjedi_gen_ens_pert_B.x
bin/mpas_data_checker.py                  bin/mpasjedi_hofx3d.x
bin/mpas_data_downloader.py               bin/mpasjedi_hofx.x
bin/mpas_init_atmosphere                  bin/mpasjedi_rtpp.x
bin/mpasjedi_convertstate.x               bin/mpasjedi_staticbinit.x
bin/mpasjedi_dirac.x                      bin/mpasjedi_variational.x
bin/mpasjedi_eda.x                        bin/mpas_namelist_gen
bin/mpasjedi_enkf.x                       bin/mpas_parse_atmosphere
bin/mpasjedi_enshofx.x                    bin/mpas_parse_init_atmosphere
bin/mpasjedi_error_covariance_training.x  bin/mpas_streams_gen

$ ls bin/ioda-up*
bin/ioda-upgrade-v1-to-v2.x  bin/ioda-upgrade-v2-to-v3.x
```

The last step is to ensure that the code was compiled properly by running the MPAS-JEDI ctests, with two lines of simple command:

```sh
$ cd mpas-jedi
$ ctest

100% tests passed, 0 tests failed out of 47

Label Time Summary:
executable    =  54.21 sec*proc (13 tests)
mpasjedi      = 380.26 sec*proc (47 tests)
mpi           = 287.59 sec*proc (43 tests)
script        = 326.05 sec*proc (34 tests)

Total Test time (real) = 380.91 sec
```

To determine if a test passes or fails, *a comparison of the test log and reference file is done internally taking as reference a tolerance value*. This tolerance is specified via YAML file. These files can be found under **mpas-jedi/test/testoutput** in the build folder.

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

