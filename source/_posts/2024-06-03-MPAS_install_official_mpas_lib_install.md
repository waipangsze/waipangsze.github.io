---
layout: post
title: MPAS | Official MPAS Installation lib -- Preliminary requirements
categories: [MPAS]
tags: [Installation]
author: wpsze
index_img: 
banner_img: 
---

# Preliminary requirements: the easiest route

Installing the full set of I/O libraries required by MPAS can be tedious.
To make this easier, we've prepared a shell script that may be helpful:

Preliminary requirements: the easiest route

<https://www2.mmm.ucar.edu/projects/mpas/scripts/mpas_lib_install.sh>

Notes:

- Before running or following the above script, you will need to have
downloaded the library sources from <https://www2.mmm.ucar.edu/projects/mpas/scripts>
- If you already have an MPI library, skip the MPICH installation
- After editing paths and compiler names at the top, you may be able
to run the script, but in general the script may be best used as a
guide

attached below,

```sh
#!/usr/bin/env bash

#
# Sources for all libraries used in this script can be found at
# http://www2.mmm.ucar.edu/people/duda/files/mpas/sources/ 
#

# Where to find sources for libraries - generally, the directory into which
# you have downloaded the sources from the URL, above
export LIBSRC=/sysdisk2/duda/sources

# Where to install libraries - this directory must be writable by you
export LIBBASE=/sysdisk2/duda/mpas-libs

# Compilers
export SERIAL_FC=gfortran
export SERIAL_F77=gfortran
export SERIAL_CC=gcc
export SERIAL_CXX=g++
export MPI_FC=mpifort
export MPI_F77=mpifort
export MPI_CC=mpicc
export MPI_CXX=mpic++


export CC=$SERIAL_CC
export CXX=$SERIAL_CXX
export F77=$SERIAL_F77
export FC=$SERIAL_FC
unset F90  # This seems to be set by default on NCAR's Cheyenne and is problematic
unset F90FLAGS
export CFLAGS="-g"
export FFLAGS="-g -fbacktrace"
export FCFLAGS="-g -fbacktrace -fallow-argument-mismatch"
export F77FLAGS="-g -fbacktrace -fallow-argument-mismatch"


########################################
# MPICH
########################################
tar xzvf ${LIBSRC}/mpich-3.3.1.tar.gz 
cd mpich-3.3.1
./configure --prefix=${LIBBASE}
make -j 4
#make check
make install
#make testing
export PATH=${LIBBASE}/bin:$PATH
export LD_LIBRARY_PATH=${LIBBASE}/lib:$LD_LIBRARY_PATH
cd ..
rm -rf mpich-3.3.1

########################################
# zlib
########################################
tar xzvf ${LIBSRC}/zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure --prefix=${LIBBASE} --static
make -j 4
make install
cd ..
rm -rf zlib-1.2.11

########################################
# HDF5
########################################
tar xjvf ${LIBSRC}/hdf5-1.10.5.tar.bz2
cd hdf5-1.10.5
export FC=$MPI_FC
export CC=$MPI_CC
export CXX=$MPI_CXX
./configure --prefix=${LIBBASE} --enable-parallel --with-zlib=${LIBBASE} --disable-shared
make -j 4
#make check
make install
cd ..
rm -rf hdf5-1.10.5

########################################
# Parallel-netCDF
########################################
tar xzvf ${LIBSRC}/pnetcdf-1.12.2.tar.gz
cd pnetcdf-1.12.2
export CC=$SERIAL_CC
export CXX=$SERIAL_CXX
export F77=$SERIAL_F77
export FC=$SERIAL_FC
export MPICC=$MPI_CC
export MPICXX=$MPI_CXX
export MPIF77=$MPI_F77
export MPIF90=$MPI_FC
### Will also need gcc in path
./configure --prefix=${LIBBASE}
make -j 4
#make check
#make ptest
#make testing
make install
export PNETCDF=${LIBBASE}
cd ..
rm -rf pnetcdf-1.12.2

########################################
# netCDF (C library)
########################################
tar xzvf ${LIBSRC}/netcdf-c-4.6.3.tar.gz
cd netcdf-c-4.6.3
export CPPFLAGS="-I${LIBBASE}/include"
export LDFLAGS="-L${LIBBASE}/lib"
export LIBS="-lhdf5_hl -lhdf5 -lz -ldl"
export CC=$MPI_CC
./configure --prefix=${LIBBASE} --disable-dap --enable-netcdf4 --enable-pnetcdf --enable-cdf5 --enable-parallel-tests --disable-shared
make -j 4 
#make check
make install
export NETCDF=${LIBBASE}
cd ..
rm -rf netcdf-c-4.6.3

########################################
# netCDF (Fortran interface library)
########################################
tar xzvf ${LIBSRC}/netcdf-fortran-4.5.2.tar.gz
cd netcdf-fortran-4.5.2
export FC=$MPI_FC
export F77=$MPI_F77
export LIBS="-lnetcdf -lpnetcdf ${LIBS}"
./configure --prefix=${LIBBASE} --enable-parallel-tests --disable-shared
make -j 4
#make check
make install
cd ..
rm -rf netcdf-fortran-4.5.2

########################################
# PIO
########################################
git clone https://github.com/NCAR/ParallelIO
cd ParallelIO
git checkout -b pio-2.5.8 pio2_5_8
export PIOSRC=`pwd`
cd ..
mkdir pio
cd pio
export CC=$MPI_CC
export FC=$MPI_FC
cmake -DNetCDF_C_PATH=$NETCDF -DNetCDF_Fortran_PATH=$NETCDF -DPnetCDF_PATH=$PNETCDF -DHDF5_PATH=$NETCDF -DCMAKE_INSTALL_PREFIX=$LIBBASE -DPIO_USE_MALLOC=ON -DCMAKE_VERBOSE_MAKEFILE=1 -DPIO_ENABLE_TIMING=OFF $PIOSRC
make
#make check
make install
cd ..
rm -rf pio ParallelIO
export PIO=$LIBBASE

########################################
# Other environment vars needed by MPAS
########################################
export MPAS_EXTERNAL_LIBS="-L${LIBBASE}/lib -lhdf5_hl -lhdf5 -ldl -lz"
export MPAS_EXTERNAL_INCLUDES="-I${LIBBASE}/include"
```