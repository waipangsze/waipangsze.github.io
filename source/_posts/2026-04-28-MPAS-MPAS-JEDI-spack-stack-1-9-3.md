---
layout: post
title: MPAS | MPAS-JEDI | spack-stack 1.9.3
categories: [Spack]
tags: [MPAS, DA, MPAS-JEDI, Spack, Spack-Stack]
author: wpsze
date: 2026-04-28 06:25:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/6oGdLJk.png
banner_img: https://i.imgur.com/6oGdLJk.png
---

- [MPAS | MPAS-JEDI](https://waipangsze.github.io/2024/05/29/MPAS-JEDI_note/)
- [MPAS | Joint MPAS/WRF Users Workshop 2025 | MPASv8.3.0](https://waipangsze.github.io/2025/06/05/MPAS-Joint-MPAS-WRF-Users-Workshop-2025/)
- [**Spack-Stack: building JEDIbundles on your own machine | 202506NCAS** ](https://www2.mmm.ucar.edu/projects/mpas-jedi/tutorial/202506NCAS/lectures/12-spackstack.pdf)

---

- <https://spack-stack.readthedocs.io/en/latest/>
  - The spack-stack documentation moved from readthedocs to the JCSDA spack-stack Wiki (https://github.com/jcsda/spack-stack/wiki) with release 2.0.0.
  - The latest documentation here is for spack-stack-1.9.3.

---


# Pre-configured sites

- <https://spack-stack.readthedocs.io/en/latest/PreConfiguredSites.html>

# Creating a new environment

- <https://spack-stack.readthedocs.io/en/latest/NewSiteConfigs.html#linux>

{% note primary %}
Do not use Conda environment, and keep the environment definition clear.
{% endnote %}

It is recommended to increase the stacksize limit by using **`ulimit -S -s unlimited`**,

```sh
git clone [-b develop OR release/branch-name] --recurse-submodules https://github.com/jcsda/spack-stack.git
cd spack-stack

# Sources Spack from submodule and sets ${SPACK_STACK_DIR}
source setup.sh
```

```sh
spack stack create env --site linux.default [--template skylab-dev] --name skylab-dev.mylinux --compiler=gcc
cd envs/skylab-dev.mylinux/
spack env activate [-p] .
```

# command

{% note primary %}
Do not use Conda environment, and keep the environment definition clear.
{% endnote %}

```sh
spack env activate envs/gcc_mpich
spack env deactivate
spack env status
spack find
spack info --all xxx
spack add xxx
space remove xxx
spack install
spack module tcl refresh or lmod ?
```

# jedi-mpas-env

The `jedi-mpas-env` refers to **setting up the software environment required to run JEDI-MPAS**, a Joint Effort for Data assimilation Integration system for the Model for Prediction Across Scales (MPAS). It enables 3D/4DEnVar and 3DVar data assimilation using containers or HPC module files (e.g., NCAR/MMM, JCSDA).

- <https://www2.mmm.ucar.edu/projects/mpas-jedi/tutorial/202506NCAS/lectures/12-spackstack.pdf>
  - P.6
- `module load jedi-mpas-env`

![](https://i.imgur.com/K8MI4WN.png)

```console
 $ spack info jedi-mpas-env
BundlePackage:   jedi-mpas-env

Description:
    Development environment for mpas-bundle

Homepage: https://github.com/JCSDA/mpas-bundle

Preferred version:  
    1.0.0    

Safe versions:  
    1.0.0    

Deprecated versions:  
    None

Variants:
    build_system [bundle]        bundle
        Build systems supported by the package

Build Dependencies:
    None

Link Dependencies:
    None

Run Dependencies:
    jasper  jedi-base-env  metis  parallel-netcdf  parallelio

Licenses: 
    None
```

similar to <https://spack-stack.readthedocs.io/en/1.9.1/CreatingEnvironments.html>,

```sh
To generate a new spack-stack directory structure, run 'git clone --recurse-submodules https://github.com/JCSDA/spack-stack',
optionally with, e.g., '-b release/1.4.1' to specify the version

. <path to spack-stack directory>/setup.sh

spack stack create env --site <site name> --template <template name> --name <environment name> --compiler <compiler>

cd ${SPACK_STACK_DIR}/envs/<environment name>/

spack env activate .

If not using an existing site configuration, you may wish to modify config files and/or populate them using commands
such as 'spack external find' and 'spack compiler find'.
See https://spack-tutorial.readthedocs.io/en/latest/tutorial_configuration.html

spack concretize 2>&1 | tee log.concretize

spack install [--verbose] [--fail-fast] 2>&1 | tee log.install

# Create lua module files
spack module lmod refresh

# Create meta-modules for compiler, mpi, python
spack stack setup-meta-modules
```

the actual install `jedi-mpas-env`,

```sh
ulimit -s unlimited

# Clone repo
git clone -b spack-stack-1.9.3 --recurse-submodules https://github.com/jcsda/spack-stack.git spack-stack-1.9.3
cd spack-stack-1.9.3/

source setup.sh
spack stack create env --name=skylab-dev --site=linux.default --compiler gcc
spack env activate -p $PWD/envs/skylab-dev

spack add gcc@9.4.0
spack install
spack compiler find "$(spack location -i gcc)"

spack add lmod
spack add jedi-mpas-env%gcc@9.4.0

spack concretize 2>&1 | tee log.concretize
util/show_duplicate_packages.py -d log.concretize
spack install --verbose --fail-fast 2>&1 | tee log.install

# Finalize
spack module lmod refresh
#spack stack setup-meta-modules
```

How to source?

# Lmod/TCL

**Lmod** and **TCL** Modules are software tools used in high-performance computing (HPC) to manage user environments, specifically handling multiple software versions, libraries, and compiler paths. 

- Lmod is a modern, faster system based on Lua that supports hierarchical software organization, while 
- Tcl-based Environment Modules (Tmod) is the traditional C-based, older standard.

and,

- **Lmod (Lua-based)**: Uses Lua, supports hierarchical module naming (e.g., loading OpenMPI only after a specific GCC is loaded), provides cleaner output, and can handle complex software dependencies.
- **TCL Modules (Tcl-based)**: The older, traditional standard written in C and Tcl. It uses module files written in Tcl, which are generally slower to process than Lua scripts.
- Interoperability: Lmod can read and interpret traditional Tcl modulefiles, making it backwards-compatible with older TCL module repositories.

Usage Examples (Common to both):

- `module avail`: List all available modules. 
- `module load <name>`: Load a software package.
- `module unload <name>`: Remove a package.
- `module swap <old> <new>`: Replace one module with another.
- `module spider <name>`: (Lmod specific) Search for modules in a hierarchy.

go to <https://jointcenterforsatellitedataassimilation-jedi-docs.readthedocs-hosted.com/en/latest/using/jedi_environment/modules.html>,

```sh
module list # list modules you currently have loaded
module spider <string> # list all modules that contain <string>
module avail # list modules that are compatible with the modules you already have loaded
module load <package1> <package2> <...> # load specified packages
module unload <package1> <package2> <...> # unload specified packages
module purge # unload all modules
```

# How to source?

- <https://jointcenterforsatellitedataassimilation-jedi-docs.readthedocs-hosted.com/en/1.7.0/using/jedi_environment/modules.html>

```sh
. /home/wpsze/spack-stack/spack-stack-1.9.3/envs/skylab-dev/install/gcc/9.4.0/lmod-8.7.37-2qs4zuc/lmod/lmod/init/bash
module use /home/wpsze/spack-stack/spack-stack-1.9.3/envs/skylab-dev/install/modulefiles

module load gcc/8.5.0/gcc/9.4.0
module load openmpi/5.0.5/gcc/9.4.0/jedi-mpas-env/1.0.0
module available
```

## module available

{% note primary %}
```sh
  Where:
   **L:  Module is loaded**
   D:  Default Module
```
{% endnote %}

{% fold info @module available %}
```console
 $ module available

--------------------------------------------- /home/wpsze/spack-stack/spack-stack-1.9.3/envs/skylab-dev/install/modulefiles ---------------------------------------------
   Core/autoconf/2.72-2rqg2it                                      gcc/9.4.0/pigz/2.8                                             (L,D)
   Core/automake/1.16.5-p7xvb7s                                    gcc/9.4.0/pmix/5.0.3-w35traa
   Core/berkeley-db/18.1.40-s2fi3wu                                gcc/9.4.0/pmix/5.0.3                                           (L,D)
   Core/bzip2/1.0.8-74mzgge                                        gcc/9.4.0/py-attrs/23.1.0-qb3kmig
   Core/diffutils/3.10-amc63op                                     gcc/9.4.0/py-attrs/23.1.0                                      (L,D)
   Core/findutils/4.9.0-hm3lf5l                                    gcc/9.4.0/py-beniget/0.4.1-mthrgvy
   Core/gawk/5.3.1-dkzimlg                                         gcc/9.4.0/py-beniget/0.4.1                                     (D)
   Core/gcc-runtime/8.5.0-4ihak4k                                  gcc/9.4.0/py-calver/2022.6.26-jrxc36x
   Core/gcc/9.4.0-75cgril                                          gcc/9.4.0/py-calver/2022.6.26                                  (D)
   Core/gdbm/1.23-pcxfb2y                                          gcc/9.4.0/py-certifi/2023.7.22-hns3e3c
   Core/gettext/0.22.5-4dohhcb                                     gcc/9.4.0/py-certifi/2023.7.22                                 (L,D)
   Core/glibc/2.28-7rs64fv                                         gcc/9.4.0/py-cffi/1.17.1-i3mmr55
   Core/gmake/4.2.1-kkmqix4                                        gcc/9.4.0/py-cffi/1.17.1                                       (L,D)
   Core/gmp/6.3.0-paweks5                                          gcc/9.4.0/py-cftime/1.0.3.4-gnwyhug
   Core/libiconv/1.17-gkinqxy                                      gcc/9.4.0/py-cftime/1.0.3.4                                    (L,D)
   Core/libsigsegv/2.14-a5p244x                                    gcc/9.4.0/py-cython/3.0.11-atcdeql
   Core/libtool/2.4.7-u5cbll2                                      gcc/9.4.0/py-cython/3.0.11                                     (D)
   Core/libxml2/2.13.4-jv2fle7                                     gcc/9.4.0/py-eccodes/1.5.0                                     (L)
   Core/m4/1.4.19-zgfwk3s                                          gcc/9.4.0/py-eccodes/1.5.0-5k2vjst                             (D)
   Core/mpc/1.1.0-7egxxfn                                          gcc/9.4.0/py-editables/0.5
   Core/mpfr/3.1.6-chh253f                                         gcc/9.4.0/py-editables/0.5-6ap6to3                             (D)
   Core/ncurses/6.5-nt76ksf                                        gcc/9.4.0/py-f90nml/1.4.3-uw66fbv
   Core/perl/5.40.0-mptwu7i                                        gcc/9.4.0/py-f90nml/1.4.3                                      (L,D)
   Core/pigz/2.8-2nj5y44                                           gcc/9.4.0/py-findlibs/0.0.2-xntdyue
   Core/pkg-config/0.29.2-iwrftvt                                  gcc/9.4.0/py-findlibs/0.0.2                                    (L,D)
   Core/readline/8.2-czsm57q                                       gcc/9.4.0/py-flit-core/3.8.0-m5zxy3k
   Core/tar/1.34-bptsazx                                           gcc/9.4.0/py-flit-core/3.8.0                                   (D)
   Core/texinfo/7.1-3xxabpt                                        gcc/9.4.0/py-gast/0.5.4-ucplhpr
   Core/xz/5.4.6-ytyxnwd                                           gcc/9.4.0/py-gast/0.5.4                                        (D)
   Core/zlib-ng/2.2.1-lcmvnyn                                      gcc/9.4.0/py-hatch-fancy-pypi-readme/23.1.0-shcrbgh
   Core/zstd/1.5.6-ovzwwtl                                         gcc/9.4.0/py-hatch-fancy-pypi-readme/23.1.0                    (D)
   gcc/8.5.0/gawk/5.3.1                                            gcc/9.4.0/py-hatch-vcs/0.4.0-kng5wl5
   gcc/8.5.0/gcc/9.4.0                                    (L)      gcc/9.4.0/py-hatch-vcs/0.4.0                                   (D)
   gcc/8.5.0/gettext/0.22.5                                        gcc/9.4.0/py-hatchling/1.25.0-jcqgguu
   gcc/8.5.0/glibc/2.28                                   (L)      gcc/9.4.0/py-hatchling/1.25.0                                  (D)
   gcc/8.5.0/gmp/6.3.0                                    (L)      gcc/9.4.0/py-meson-python/0.16.0-vl5nfuk
   gcc/8.5.0/mpc/1.1.0                                    (L)      gcc/9.4.0/py-meson-python/0.16.0                               (D)
   gcc/8.5.0/mpfr/3.1.6                                   (L)      gcc/9.4.0/py-numpy/1.26.4-duwcukw
   gcc/8.5.0/pigz/2.8                                              gcc/9.4.0/py-numpy/1.26.4                                      (L,D)
   gcc/8.5.0/tar/1.34                                              gcc/9.4.0/py-packaging/24.1-s3puxyh
   gcc/8.5.0/texinfo/7.1                                           gcc/9.4.0/py-packaging/24.1                                    (L,D)
   gcc/8.5.0/zlib-ng/2.2.1                                (L)      gcc/9.4.0/py-pandas/2.2.3                                      (L)
   gcc/8.5.0/zstd/1.5.6                                   (L)      gcc/9.4.0/py-pandas/2.2.3-7boebhc                              (D)
   gcc/9.4.0/autoconf/2.72-6vcee6b                                 gcc/9.4.0/py-pathspec/0.11.1-ceuqqwz
   gcc/9.4.0/automake/1.16.5-an4jeq7                               gcc/9.4.0/py-pathspec/0.11.1                                   (D)
   gcc/9.4.0/bash/5.2-ui5zv7n                                      gcc/9.4.0/py-pip/23.1.2                                        (L)
   gcc/9.4.0/bash/5.2                                     (D)      gcc/9.4.0/py-pip/23.1.2-4ymkaav                                (D)
   gcc/9.4.0/bc/1.07.1-g6t3xj7                                     gcc/9.4.0/py-pkgconfig/1.5.5
   gcc/9.4.0/bc/1.07.1                                    (D)      gcc/9.4.0/py-pkgconfig/1.5.5-2sy77nb                           (D)
   gcc/9.4.0/bison/3.8.2-tkz4foa                                   gcc/9.4.0/py-pluggy/1.5.0-sdsyclg
   gcc/9.4.0/boost/1.84.0                                 (L)      gcc/9.4.0/py-pluggy/1.5.0                                      (D)
   gcc/9.4.0/boost/1.84.0-5tlfzry                         (D)      gcc/9.4.0/py-ply/3.11-xmtutcc
   gcc/9.4.0/bufr/12.1.0-yxrz5bl                                   gcc/9.4.0/py-ply/3.11                                          (D)
   gcc/9.4.0/bufr/12.1.0                                  (L,D)    gcc/9.4.0/py-poetry-core/1.8.1-a3nv3sn
   gcc/9.4.0/c-blosc/1.21.5                               (L)      gcc/9.4.0/py-poetry-core/1.8.1                                 (D)
   gcc/9.4.0/c-blosc/1.21.5-7maowyd                       (D)      gcc/9.4.0/py-pybind11/2.13.5-vyjp22a
   gcc/9.4.0/ca-certificates-mozilla/2023-05-30-f2qjx23            gcc/9.4.0/py-pybind11/2.13.5                                   (L,D)
   gcc/9.4.0/ca-certificates-mozilla/2023-05-30           (D)      gcc/9.4.0/py-pycodestyle/2.11.0-wwgomzk
   gcc/9.4.0/cmake/3.27.9-crscvsn                                  gcc/9.4.0/py-pycodestyle/2.11.0                                (L,D)
   gcc/9.4.0/cmake/3.27.9                                 (L,D)    gcc/9.4.0/py-pycparser/2.21-mbvzmwu
   gcc/9.4.0/curl/8.10.1-bnnyytl                                   gcc/9.4.0/py-pycparser/2.21                                    (L,D)
   gcc/9.4.0/curl/8.10.1                                  (L,D)    gcc/9.4.0/py-pyhdf/0.11.4-vjrj5up
   gcc/9.4.0/ecbuild/3.7.2                                (L)      gcc/9.4.0/py-pyhdf/0.11.4                                      (L,D)
   gcc/9.4.0/ecbuild/3.7.2-4oowtxb                        (D)      gcc/9.4.0/py-pyproject-metadata/0.7.1-w6axdlv
   gcc/9.4.0/eccodes/2.33.0-i6dk5yc                                gcc/9.4.0/py-pyproject-metadata/0.7.1                          (D)
   gcc/9.4.0/eccodes/2.33.0                               (L,D)    gcc/9.4.0/py-python-dateutil/2.8.2-rnj2vrb
   gcc/9.4.0/ed/1.4-fjtyose                                        gcc/9.4.0/py-python-dateutil/2.8.2                             (L,D)
   gcc/9.4.0/ed/1.4                                       (D)      gcc/9.4.0/py-pythran/0.16.1-iwjgrco
   gcc/9.4.0/eigen/3.4.0-bp3zgui                                   gcc/9.4.0/py-pythran/0.16.1                                    (D)
   gcc/9.4.0/eigen/3.4.0                                  (L,D)    gcc/9.4.0/py-pytz/2023.3-fiwwlkh
   gcc/9.4.0/expat/2.6.4-er6ynlv                                   gcc/9.4.0/py-pytz/2023.3                                       (L,D)
   gcc/9.4.0/flex/2.6.4-xecnlko                                    gcc/9.4.0/py-pyyaml/6.0.2-lkqvoqb
   gcc/9.4.0/gcc-runtime/9.4.0-zpxkolg                             gcc/9.4.0/py-pyyaml/6.0.2                                      (L,D)
   gcc/9.4.0/gettext/0.22.5-uv3tuhs                                gcc/9.4.0/py-scipy/1.14.1-jjzy7fb
   gcc/9.4.0/gettext/0.22.5                               (L,D)    gcc/9.4.0/py-scipy/1.14.1                                      (L,D)
   gcc/9.4.0/git-lfs/3.5.1-emsn5e3                                 gcc/9.4.0/py-setuptools-scm/8.0.4-grkbq6p
   gcc/9.4.0/git-lfs/3.5.1                                (L,D)    gcc/9.4.0/py-setuptools-scm/8.0.4                              (L,D)
   gcc/9.4.0/git/2.47.0                                   (L)      gcc/9.4.0/py-setuptools/69.2.0-geynsal
   gcc/9.4.0/git/2.47.0-52at7id                           (D)      gcc/9.4.0/py-setuptools/69.2.0                                 (L,D)
   gcc/9.4.0/glibc/2.28-mthvedq                                    gcc/9.4.0/py-six/1.16.0-stlpnd7
   gcc/9.4.0/glibc/2.28                                   (L,D)    gcc/9.4.0/py-six/1.16.0                                        (L,D)
   gcc/9.4.0/go-bootstrap/1.20.6-rrzryzb                           gcc/9.4.0/py-tomli/2.0.1-t3wmm3y
   gcc/9.4.0/go-bootstrap/1.20.6                          (D)      gcc/9.4.0/py-tomli/2.0.1                                       (L,D)
   gcc/9.4.0/go/1.23.2-azpi6er                                     gcc/9.4.0/py-trove-classifiers/2023.8.7
   gcc/9.4.0/go/1.23.2                                    (D)      gcc/9.4.0/py-trove-classifiers/2023.8.7-5bkz7zw                (D)
   gcc/9.4.0/grep/3.11-mbzbwcs                                     gcc/9.4.0/py-typing-extensions/4.12.2                          (L)
   gcc/9.4.0/grep/3.11                                    (D)      gcc/9.4.0/py-typing-extensions/4.12.2-7whfbse                  (D)
   gcc/9.4.0/gsl-lite/0.37.0-gchuewy                               gcc/9.4.0/py-tzdata/2023.3-nlhfk3d
   gcc/9.4.0/gsl-lite/0.37.0                              (L,D)    gcc/9.4.0/py-tzdata/2023.3                                     (L,D)
   gcc/9.4.0/hdf/4.2.15-dty75qz                                    gcc/9.4.0/py-versioneer/0.29-jdhqvnu
   gcc/9.4.0/hdf/4.2.15                                   (L,D)    gcc/9.4.0/py-versioneer/0.29                                   (D)
   gcc/9.4.0/help2man/1.49.3                                       gcc/9.4.0/py-wheel/0.41.2-sjcb4ny
   gcc/9.4.0/help2man/1.49.3-5cprm45                      (D)      gcc/9.4.0/py-wheel/0.41.2                                      (L,D)
   gcc/9.4.0/hwloc/2.11.1-z4ql67d                                  gcc/9.4.0/py-xarray/2024.7.0                                   (L)
   gcc/9.4.0/inputproto/2.3.2-ndmudmc                              gcc/9.4.0/py-xarray/2024.7.0-zvcsrqj                           (D)
   gcc/9.4.0/jasper/2.0.32-dfspk72                                 gcc/9.4.0/python-venv/1.0-buuzxqk
   gcc/9.4.0/jasper/2.0.32                                (L,D)    gcc/9.4.0/python-venv/1.0                                      (L,D)
   gcc/9.4.0/jedi-cmake/1.4.0                             (L)      gcc/9.4.0/python/3.11.7-qptkvxe
   gcc/9.4.0/jedi-cmake/1.4.0-6w5xqto                     (D)      gcc/9.4.0/python/3.11.7                                        (L,D)
   gcc/9.4.0/json-schema-validator/2.3.0                  (L)      gcc/9.4.0/qhull/2020.2-tlvi6yw
   gcc/9.4.0/json/3.11.3                                  (L)      gcc/9.4.0/qhull/2020.2                                         (L,D)
   gcc/9.4.0/kbproto/1.0.7-zptbu3r                                 gcc/9.4.0/re2c/3.1
   gcc/9.4.0/krb5/1.21.3-tjlxd2h                                   gcc/9.4.0/re2c/3.1-7hua6gb                                     (D)
   gcc/9.4.0/krb5/1.21.3                                  (L,D)    gcc/9.4.0/sed/4.9
   gcc/9.4.0/libaec/1.0.6-ytyxjqs                                  gcc/9.4.0/sed/4.9-7brnkur                                      (D)
   gcc/9.4.0/libbsd/0.12.2-dy3kimx                                 gcc/9.4.0/snappy/1.2.1-wj3xd64
   gcc/9.4.0/libedit/3.1-20240808-lgyfohi                          gcc/9.4.0/snappy/1.2.1                                         (L,D)
   gcc/9.4.0/libevent/2.1.12-dbtop3q                               gcc/9.4.0/sp/2.5.0                                             (L)
   gcc/9.4.0/libffi/3.4.6-dzwmz43                                  gcc/9.4.0/sp/2.5.0-7ek3dzd                                     (D)
   gcc/9.4.0/libice/1.1.1-uty7lrg                                  gcc/9.4.0/sqlite/3.46.0-t7k3el6
   gcc/9.4.0/libidn2/2.3.7-tb4ddxy                                 gcc/9.4.0/sqlite/3.46.0                                        (L,D)
   gcc/9.4.0/libidn2/2.3.7                                (L,D)    gcc/9.4.0/tar/1.34-lgytpv2
   gcc/9.4.0/libjpeg-turbo/2.1.0-n7usbh5                           gcc/9.4.0/tar/1.34                                             (L,D)
   gcc/9.4.0/libjpeg/2.1.0                                (L)      gcc/9.4.0/tcl/8.6.12
   gcc/9.4.0/libmd/1.0.4-izbaqk2                                   gcc/9.4.0/tcl/8.6.12-2a3lore                                   (D)
   gcc/9.4.0/libmd/1.0.4                                  (D)      gcc/9.4.0/texinfo/7.1-qcivmzc
   gcc/9.4.0/libpciaccess/0.17-qujzmtw                             gcc/9.4.0/texinfo/7.1                                          (D)
   gcc/9.4.0/libpng/1.6.37-z2ut3aj                                 gcc/9.4.0/udunits/2.2.28-fvhonxe
   gcc/9.4.0/libpng/1.6.37                                (L,D)    gcc/9.4.0/udunits/2.2.28                                       (L,D)
   gcc/9.4.0/libpthread-stubs/0.5-7y6d3lh                          gcc/9.4.0/unzip/6.0-d5iy3if
   gcc/9.4.0/libsm/1.2.4-brd3dq2                                   gcc/9.4.0/unzip/6.0                                            (D)
   gcc/9.4.0/libtirpc/1.3.3                               (L)      gcc/9.4.0/util-linux-uuid/2.40.2-sssyfeh
   gcc/9.4.0/libtirpc/1.3.3-6t2taej                       (D)      gcc/9.4.0/util-linux-uuid/2.40.2                               (L,D)
   gcc/9.4.0/libunistring/1.2-u3pminm                              gcc/9.4.0/util-macros/1.20.1-birozuu
   gcc/9.4.0/libunistring/1.2                             (L,D)    gcc/9.4.0/util-macros/1.20.1                                   (D)
   gcc/9.4.0/libx11/1.8.10-6et5vp2                                 gcc/9.4.0/wget/1.24.5-h5jw4fx
   gcc/9.4.0/libxau/1.0.11-5khjtrc                                 gcc/9.4.0/wget/1.24.5                                          (L,D)
   gcc/9.4.0/libxaw/1.0.16-aptalyj                                 gcc/9.4.0/xcb-proto/1.17.0-7bczwxw
   gcc/9.4.0/libxaw/1.0.16                                (L,D)    gcc/9.4.0/xextproto/7.3.0-wutirh3
   gcc/9.4.0/libxcb/1.17.0-f6m6qw2                                 gcc/9.4.0/xproto/7.0.31-suis5wp
   gcc/9.4.0/libxcrypt/4.4.35-cjhbpid                              gcc/9.4.0/xtrans/1.5.2-u7ifemw
   gcc/9.4.0/libxcrypt/4.4.35                             (D)      gcc/9.4.0/zlib/1.3.1-f4vfbfc
   gcc/9.4.0/libxdmcp/1.1.5-5nzoiyy                                gcc/9.4.0/zlib/1.3.1                                           (L,D)
   gcc/9.4.0/libxext/1.3.6-aglzehr                                 openmpi/5.0.5-5fwxp6t/gcc/9.4.0/base-env/1.0.0-nborhib
   gcc/9.4.0/libxml2/2.13.4-lg5a2ff                                openmpi/5.0.5-5fwxp6t/gcc/9.4.0/bufr-query/0.0.4-rkmjxgx
   gcc/9.4.0/libxmu/1.2.1                                 (L)      openmpi/5.0.5-5fwxp6t/gcc/9.4.0/eckit/1.28.3-c2o43a7
   gcc/9.4.0/libxmu/1.2.1-zpwcmj7                         (D)      openmpi/5.0.5-5fwxp6t/gcc/9.4.0/ecmwf-atlas/0.40.0-sorleuq
   gcc/9.4.0/libxpm/3.5.17-f45yhpg                                 openmpi/5.0.5-5fwxp6t/gcc/9.4.0/ectrans/1.5.0-n6i43lc
   gcc/9.4.0/libxpm/3.5.17                                (L,D)    openmpi/5.0.5-5fwxp6t/gcc/9.4.0/fckit/0.13.2-gnlieyp
   gcc/9.4.0/libxt/1.3.0-m6ohgbr                                   openmpi/5.0.5-5fwxp6t/gcc/9.4.0/fftw/3.3.10-4sse52w
   gcc/9.4.0/libxt/1.3.0                                  (L,D)    openmpi/5.0.5-5fwxp6t/gcc/9.4.0/fiat/1.4.1-ky2nfqb
   gcc/9.4.0/libyaml/0.2.5-lfm5d3z                                 openmpi/5.0.5-5fwxp6t/gcc/9.4.0/gsibec/1.2.1-e4qmoe2
   gcc/9.4.0/libyaml/0.2.5                                (L,D)    openmpi/5.0.5-5fwxp6t/gcc/9.4.0/hdf5/1.14.3-hmqlth4
   gcc/9.4.0/lmod/8.7.37                                           openmpi/5.0.5-5fwxp6t/gcc/9.4.0/jedi-base-env/1.0.0-5ptp3tx
   gcc/9.4.0/lmod/8.7.37-2qs4zuc                          (D)      openmpi/5.0.5-5fwxp6t/gcc/9.4.0/jedi-mpas-env/1.0.0-kfzw6c2
   gcc/9.4.0/lua-luafilesystem/1.8.0-i7odovy                       openmpi/5.0.5-5fwxp6t/gcc/9.4.0/nccmp/1.9.0.1-lhmkql5
   gcc/9.4.0/lua-luafilesystem/1.8.0                      (D)      openmpi/5.0.5-5fwxp6t/gcc/9.4.0/ncview/2.1.9-cbkefec
   gcc/9.4.0/lua-luaposix/36.1-y754jtf                             openmpi/5.0.5-5fwxp6t/gcc/9.4.0/netcdf-c/4.9.2-3zzahrd
   gcc/9.4.0/lua-luaposix/36.1                            (D)      openmpi/5.0.5-5fwxp6t/gcc/9.4.0/netcdf-cxx4/4.3.1-qdd3lng
   gcc/9.4.0/lua/5.4.6-hacn73f                                     openmpi/5.0.5-5fwxp6t/gcc/9.4.0/netcdf-fortran/4.6.1-evrukwq
   gcc/9.4.0/lua/5.4.6                                    (D)      openmpi/5.0.5-5fwxp6t/gcc/9.4.0/odc/1.5.2-clvb7ne
   gcc/9.4.0/lz4/1.10.0-nkvcbhr                                    openmpi/5.0.5-5fwxp6t/gcc/9.4.0/parallel-netcdf/1.12.3-nx2ekel
   gcc/9.4.0/meson/1.5.1-wxhnrsn                                   openmpi/5.0.5-5fwxp6t/gcc/9.4.0/parallelio/2.6.2-ajpdtjy
   gcc/9.4.0/meson/1.5.1                                  (D)      openmpi/5.0.5-5fwxp6t/gcc/9.4.0/py-h5py/3.12.1-aphssmk
   gcc/9.4.0/metis/5.1.0-c4f5nu5                                   openmpi/5.0.5-5fwxp6t/gcc/9.4.0/py-netcdf4/1.7.1.post2-bt6nbom
   gcc/9.4.0/metis/5.1.0                                  (L,D)    openmpi/5.0.5/gcc/9.4.0/base-env/1.0.0                         (L)
   gcc/9.4.0/nasm/2.16.03-phswyas                                  openmpi/5.0.5/gcc/9.4.0/bufr-query/0.0.4                       (L)
   gcc/9.4.0/nghttp2/1.63.0-dums4bx                                openmpi/5.0.5/gcc/9.4.0/eckit/1.28.3                           (L)
   gcc/9.4.0/nghttp2/1.63.0                               (L,D)    openmpi/5.0.5/gcc/9.4.0/ecmwf-atlas/0.40.0                     (L)
   gcc/9.4.0/ninja/1.12.1-jtx5qu7                                  openmpi/5.0.5/gcc/9.4.0/ectrans/1.5.0                          (L)
   gcc/9.4.0/ninja/1.12.1                                 (D)      openmpi/5.0.5/gcc/9.4.0/fckit/0.13.2                           (L)
   gcc/9.4.0/nlohmann-json-schema-validator/2.3.0-txk3xzg          openmpi/5.0.5/gcc/9.4.0/fftw/3.3.10                            (L)
   gcc/9.4.0/nlohmann-json/3.11.3-pck4jd6                          openmpi/5.0.5/gcc/9.4.0/fiat/1.4.1                             (L)
   gcc/9.4.0/numactl/2.0.18                               (L)      openmpi/5.0.5/gcc/9.4.0/gsibec/1.2.1                           (L)
   gcc/9.4.0/numactl/2.0.18-2yxdh43                       (D)      openmpi/5.0.5/gcc/9.4.0/hdf5/1.14.3                            (L)
   gcc/9.4.0/openblas/0.3.24-jgrqlz3                               openmpi/5.0.5/gcc/9.4.0/jedi-base-env/1.0.0                    (L)
   gcc/9.4.0/openblas/0.3.24                              (L,D)    openmpi/5.0.5/gcc/9.4.0/jedi-mpas-env/1.0.0                    (L)
   gcc/9.4.0/openjpeg/2.3.1-boofcmh                                openmpi/5.0.5/gcc/9.4.0/nccmp/1.9.0.1                          (L)
   gcc/9.4.0/openjpeg/2.3.1                               (L,D)    openmpi/5.0.5/gcc/9.4.0/ncview/2.1.9                           (L)
   gcc/9.4.0/openmpi/5.0.5                                (L)      openmpi/5.0.5/gcc/9.4.0/netcdf-c/4.9.2                         (L)
   gcc/9.4.0/openmpi/5.0.5-5fwxp6t                        (D)      openmpi/5.0.5/gcc/9.4.0/netcdf-cxx4/4.3.1                      (L)
   gcc/9.4.0/openssh/9.9p1-vbinfkj                                 openmpi/5.0.5/gcc/9.4.0/netcdf-fortran/4.6.1                   (L)
   gcc/9.4.0/openssl/3.4.0-vgxv7mi                                 openmpi/5.0.5/gcc/9.4.0/odc/1.5.2                              (L)
   gcc/9.4.0/pcre2/10.44                                  (L)      openmpi/5.0.5/gcc/9.4.0/parallel-netcdf/1.12.3                 (L)
   gcc/9.4.0/pcre2/10.44-zgoef44                          (D)      openmpi/5.0.5/gcc/9.4.0/parallelio/2.6.2                       (L)
   gcc/9.4.0/perl/5.40.0-mekatgh                                   openmpi/5.0.5/gcc/9.4.0/py-h5py/3.12.1                         (L)
   gcc/9.4.0/pigz/2.8-ldbpbxt                                      openmpi/5.0.5/gcc/9.4.0/py-netcdf4/1.7.1.post2                 (L)

  Where:
   L:  Module is loaded
   D:  Default Module

If the avail list is too long consider trying:

"module --default avail" or "ml -d av" to just list the default modules.
"module overview" or "ml ov" to display the number of modules for each name.

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".
```
{% endfold %}

check,

```sh
 $ which gcc
/home/wpsze/spack-stack/spack-stack-1.9.3/envs/skylab-dev/install/gcc/8.5.0/gcc-9.4.0-75cgril/bin/gcc
 $ which ecbuild
/home/wpsze/spack-stack/spack-stack-1.9.3/envs/skylab-dev/install/gcc/9.4.0/ecbuild-3.7.2-4oowtxb/bin/ecbuild
```

# Install MPAS-JEDI

## source

```sh
. /home/wpsze/spack-stack/spack-stack-1.9.3/envs/skylab-dev/install/gcc/9.4.0/lmod-8.7.37-2qs4zuc/lmod/lmod/init/bash
module use /home/wpsze/spack-stack/spack-stack-1.9.3/envs/skylab-dev/install/modulefiles

module load gcc/8.5.0/gcc/9.4.0
module load openmpi/5.0.5/gcc/9.4.0/jedi-mpas-env/1.0.0

module load \
  gcc/9.4.0/ecbuild/3.7.2 \
  gcc/9.4.0/git-lfs/3.5.1 \
  openmpi/5.0.5/gcc/9.4.0/ecmwf-atlas/0.40.0 \
  openmpi/5.0.5/gcc/9.4.0/parallelio/2.6.2 \
  gcc/9.4.0/gsl-lite/0.37.0 \
  gcc/9.4.0/udunits/2.2.28 \
```

## Error 

### Missing actual argument for argument 'omax' at (1)

```console
[ 60%] Building Fortran object MPAS/src/core_atmosphere/CMakeFiles/core_atmosphere.dir/physics/physics_wrf/module_bl_gwdo.F.o
/home/wpsze/cpas/CPAS-DA/cpas-da/jedi/my_302/mpas-bundle/MPAS/src/core_atmosphere/physics/physics_wrf/module_bl_gwdo.F:215:49:

  215 |                     ,errmsg=errmsg,errflg=errflg)
      |                                                 1
Error: Missing actual argument for argument 'omax' at (1)
make[2]: *** [MPAS/src/core_atmosphere/CMakeFiles/core_atmosphere.dir/build.make:2766: MPAS/src/core_atmosphere/CMakeFiles/core_atmosphere.dir/physics/physics_wrf/module_bl_gwdo.F.o] Error 1
make[2]: *** Waiting for unfinished jobs....
```