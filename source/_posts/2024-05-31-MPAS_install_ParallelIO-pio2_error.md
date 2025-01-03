---
layout: post
title: MPAS | install ParallelIO-pio2_5_8
categories: [MPAS]
tags: [PIO, Installation]
author: wpsze
index_img: 
banner_img: 
---

# Issue & solution

1. needs cmake
> $ micromamba env create -n mpas_env python=3.9
> micromamba install anaconda::cmake

2. cmake

```sh
export libs_DIR=$HOME/Library/
### Compilers
export MPI_FC=mpifort
export MPI_F77=mpifort
export MPI_F90=mpifort
export MPI_CC=mpicc
export MPI_CXX=mpic++
### all serial are same as MPI
export FC=${MPI_FC}
export F77=${MPI_F77}
export F90=${MPI_F77}
export CC=${MPI_F77}
export CXX=${MPI_F77}
```

```sh
############################## Pio ############################
if [[ ${do_pio} == True ]]; then
    echo "=== PIO ===="
    cd $download_DIR
    tar xvf ParallelIO-pio2_5_8.tar.gz 1>/dev/null 2>&1
    cd ParallelIO-pio2_5_8/

    export CC=mpicc 
    export FC=mpif90 
    #./configure --prefix=$libs_DIR --enable-fortran
    #cmake -DNetCDF_C_PATH=$libs_DIR -DNetCDF_Fortran_PATH=$libs_DIR -DPnetCDF_PATH=$libs_DIR -DPIO_ENABLE_TIMING=OFF -DCMAKE_INSTALL_PREFIX=$libs_DIR
    cmake -DNetCDF_C_PATH=$NETCDF -DNetCDF_Fortran_PATH=$NETCDF -DPnetCDF_PATH=$PNETCDF -DHDF5_PATH=$libs_DIR -DCMAKE_INSTALL_PREFIX=$libs_DIR -DPIO_USE_MALLOC=ON -DCMAKE_VERBOSE_MAKEFILE=1 -DPIO_ENABLE_TIMING=OFF ./
    make
    #make check
    make install
    cd ..
    rm -rf ParallelIO-pio2_5_8
    echo "=== PIO (end) ===="
fi
```

where ./ is PIO directory.

```
568 Install the project...
569 /home/wpsze/micromamba/envs/mpas_env/bin/cmake -P cmake_install.cmake
570 -- Install configuration: ""
571 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/lib/libpio.settings
572 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/lib/libpioc.a
573 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/pio.h
574 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/uthash.h
575 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/lib/libpiof.a
576 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/pio.mod
577 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/pio_nf.mod
578 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/pio_types.mod
579 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/piolib_mod.mod
580 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/pionfget_mod.mod
581 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/pio_kinds.mod
582 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/pio_support.mod
583 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/piodarray.mod
584 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/pionfatt_mod.mod
585 -- Installing: /home/wpsze/MPAS-A/modules_library/Library/include/pionfput_mod.mod
```

Output files are,

```
 $ ls Library/lib/*pio*
Library/lib/libpioc.a  Library/lib/libpiof.a
 $ ls Library/include/*pio*
Library/include/H5FDmpio.h  Library/include/piodarray.mod  Library/include/piolib_mod.mod    Library/include/pionfget_mod.mod  Library/include/pio_support.mod
Library/include/mpiof.h     Library/include/pio.h          Library/include/pio.mod           Library/include/pio_nf.mod        Library/include/pio_types.mod
Library/include/mpio.h      Library/include/pio_kinds.mod  Library/include/pionfatt_mod.mod  Library/include/pionfput_mod.mod
```

Ref:
1. [MPAS-A installation failed due to pio1.f90 and pio2.f90](https://forum.mmm.ucar.edu/threads/mpas-a-installation-failed-due-to-pio1-f90-and-pio2-f90.9902/)
2. 
