---
layout: post
title: MPAS | MPAS-JEDI V3.0.2
categories: [MPAS]
tags: [MPAS, DA, MPAS-JEDI]
author: wpsze
date: 2026-02-13 06:25:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/6oGdLJk.png
banner_img: https://i.imgur.com/6oGdLJk.png
---

- [MPAS | MPAS-JEDI](https://waipangsze.github.io/2024/05/29/MPAS-JEDI_note/)
- [MPAS | Joint MPAS/WRF Users Workshop 2025 | MPASv8.3.0](https://waipangsze.github.io/2025/06/05/MPAS-Joint-MPAS-WRF-Users-Workshop-2025/)

---

# obs2ioda

- <https://github.com/jamiebresch/obs2ioda>
- <https://github.com/jamiebresch/obs2ioda/blob/main/obs2ioda-v2/README.md>
  - `Compile: cd obs2ioda-v2/src; make`
  - `NCEP BUFR` library (<https://github.com/NOAA-EMC/NCEPLIBS-bufr>) is required to compile obs2ioda-v2.x.
    - `cmake 3.15`
    - `bufr_v11.5.0`
  - Edit obs2ioda-v2/src/Makefile to set proper BUFR_LIB before make.

## conda env

```sh
micromamba env create -n obs2ioda
micromamba install gfortran=10.4.0
micromamba install -c conda-forge netcdf-fortran
(micromamba install netcdf4)
```

- read <https://github.com/jamiebresch/obs2ioda/blob/main/obs2ioda-v2/src/Makefile>

{% fold info @run.sh %}
```sh
#!/bin/bash
############################## Directory Listing ############################
source /home/wpsze/micromamba/bin/activate obs2ioda
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

export HOME=`pwd`

### NCEP BUFR library environment
export NCEP_BUFR_library_dir="/home/wpsze/NCEPLIBS-bufr/NCEPLIBS-bufr/selflibbufr/"
export PATH=$NCEP_BUFR_library_dir/bin:$PATH
export LD_LIBRARY_PATH=$NCEP_BUFR_library_dir/lib64:$LD_LIBRARY_PATH
export LIBRARY_PATH=$NCEP_BUFR_library_dir/lib64:$LIBRARY_PATH

export NETCDF="/home/wpsze/micromamba/envs/obs2ioda"

# H8 or H9
# 0) src/hsd.f90:241:character(len=3)    :: satellite = 'H08'
# 1) copy /home/wpsze/mpas/obs2ioda/obs2ioda-v2/src/Makefile to new H9 src
# 2) Error: Logicals at (1) must be compared with .eqv. instead of ==
#   ./hsd.f90 
#   306:      if ( fexist(ij) == .false. ) then

echo "====== gfortran version ====="
#
# gfortran: error: unrecognized command line option ‘-fallow-argument-mismatch’; did you mean ‘-Wno-argument-mismatch’?
# gfortran: error: unrecognized command line option ‘-fallow-invalid-boz’; did you mean ‘-Wno-invalid-pch’?
# 1. The most recommended approach is to update your compiler to GFortran version 10 or newer, which officially supports the flag
# 2. Replace -fallow-argument-mismatch with -Wno-argument-mismatch in your build configuration or command line.  

#ldd obs2ioda-v2.x 
gfortran --version

echo "====== netcdf.mod ======="
# 
# Fatal Error: Cannot open module file 'netcdf.mod' for reading at (1): No such file or directory
#
nf-config --fflags
nf-config --flibs

cd ./obs2ioda/obs2ioda-v2/src/

# ld is the command for the GNU linker (link editor)
ld -lbufr #--verbose
ld -lbufr_4 #--verbose
ld -lbufr_d_DA #--verbose

echo "==== LD_LIBRARY_PATH ==="
echo $LD_LIBRARY_PATH
echo "==== LIBRARY_PATH    ==="
echo $LIBRARY_PATH
echo "========================"

rm log.log

make clean

## you can add sometings like -g -traceback in the FFLAGS for full debug msg,

make 2>&1 |tee log.log
```
{% endfold %}

Finally, check dependencies,

```sh
$ ldd obs2ioda-v2/src/obs2ioda-v2.x
```

# MPAS-JEDI v3.0.2

- <https://github.com/JCSDA/mpas-jedi/tree/release/3.0.2>

## Test

- All *yaml under
  - <https://github.com/JCSDA/mpas-jedi/tree/release/3.0.2/test/testinput/>