---
layout: post
title: MPAS -- Installation by Spack and Intel
categories: [MPAS]
tags: [MPAS, Spack, Intel, Installation]
author: wpsze
mathjax: true
mathjax_autoNumber: true
---

# Using Spack

Spack is a package management tool designed to support multiple versions and configurations of software on a wide variety of platforms and environments. It was designed for large supercomputing centers, where many users and application teams share common installations of software on clusters with exotic architectures, using libraries that do not have a standard ABI. Spack is non-destructive: installing a new version does not break existing installations, so many configurations can coexist on the same system.
Most importantly, Spack is simple. It offers a simple spec syntax so that users can specify versions and configuration options concisely. Spack is also simple for package authors: package files are written in pure Python, and specs allow package authors to maintain a single file for many different builds of the same package.

<https://spack.readthedocs.io/en/latest/>

## Search packages online

> <https://packages.spack.io/>

# Install Intel compiler and intel-onempi

```sh
$ spack env create intel_oneapi 
$ spack env activate intel_oneapi
$ spack info --all intel-oneapi-mpi
$ spack info --all intel-oneapi-compilers
```

Try to install [intel-oneapi-mpi/2021.4.0](https://spack.readthedocs.io/en/latest/build_systems/inteloneapipackage.html ), 

```sh
$ spack install --add intel-oneapi-compilers@2021.4.0
# Add the compilers to your compilers.yaml so spack can use them:
# For 2023.x and earlier versions, use:
$ spack compiler add `spack location -i intel-oneapi-compilers@2021.4.0`/compiler/latest/linux/bin/intel64
$ spack compiler add `spack location -i intel-oneapi-compilers@2021.4.0`/compiler/latest/linux/bin
# Verify that the compilers are available:
$ spack compiler list
$ spack location -i intel-oneapi-compilers@2021.4.0
```

Then, install intel-onempi,

```sh
$ spack install --add intel-oneapi-mpi@2021.4.0%intel
```

Done.

And, check

```sh
spack env activate intel_oneapi

which mpiicc && mpiicc --version
which mpiifort && mpiifort --version
which mpiicpc && mpiicpc --version
which icc && icc --version
which ifort && ifort --version
```

Additionally, install curl and cmake,

```sh
$ spack info --all curl
$ spack install --add curl%intel
$ spack info --all cmake
$ spack install --add cmake%intel
```

# Install enviroment for MPAS

Prepare 

```sh
#!/bin/bash

#------------------------------------------------#
#Author:         wpsze
#Email：         
#date:           
#Version:        
#Description:    The purpose of the script
#Copyright (C)： 2022 All rights reserved
#------------------------------------------------#

export libs_DIR=/home/wpsze/MPAS-A/intel/modules_library/Library/

# intel compiler & intel onempi
spack env activate intel_oneapi

which mpiicc && mpiicc --version
which mpiifort && mpiifort --version
which mpiicpc && mpiicpc --version
which icc && icc --version
which ifort && ifort --version

# MPAS env
export PATH=$libs_DIR/bin:$PATH
export LD_LIBRARY_PATH=$libs_DIR/lib:$LD_LIBRARY_PATH
export LDFLAGS=-L$libs_DIR/lib
export CPPFLAGS=-I$libs_DIR/include

export PNETCDF_PATH=$libs_DIR
export PNETCDF=$libs_DIR
export NETCDF_PATH=$libs_DIR
export NETCDF=$libs_DIR
export PIO=$libs_DIR


export CC=mpiicc
export CXX=mpiicpc
export FC=mpiifort
export F77=mpiifort
export F90=mpiifort
export MPIF90=mpiifort
export MPIF77=mpiifort
export MPIFC=mpiifort
export MPICC=mpiicc
export MPICXX=mpiicpc

export CFLAGS='-O3 -fPIC -static-intel'
export CXXFLAGS='-O3 -fPIC -static-intel'
export FFLAGS='-O3 -fPIC -static-intel'
```

Then, env sh

```sh
############################## Directory Listing ############################
export HOME=`pwd`

source /home/wpsze/MPAS-A/intel/modules_library/mpas_env_intel.sh

export download_DIR=$HOME/mpas_install/

TARGET=ifort

which mpiicc && mpiicc --version
which mpiifort && mpiifort --version
which mpiicpc && mpiicpc --version
which icc && icc --version
which ifort && ifort --version

echo $HOME
echo $download_DIR
echo $libs_DIR

mpiicc --version
mpiifort --version

echo $HOME
echo $download_DIR
echo $libs_DIR

mkdir -p mpas_install
mkdir -p Library

do_download=False # True or False
compile_all=True

# Currently Loaded Modules:
#  1) ncarenv/1.0 (xx)      4) ncarcompilers/1.0 (xx)  7) pnetcdf/1.11.2  10) ncview/2.1.7 (already)
#  2) ncarbinlibs/1.1 (xx)  5) netcdf/4.7.0        8) pio/1.9.23 (2.5.2)      11) metis/5.1.0
#  3) gnu/8.3.0 (done)         6) mpich/3.3.1         9) ncl/6.6.2 (already)       12) hdf5/1.10.5

#do_mpich=False ## intel-onempi
do_zlib=False
do_hdf5=True
do_pnetcdf=False
do_netcdfC=False
do_netcdfF=False
do_pio=False
do_metis=fTrue

############################## Downloading Libraries ############################
if [[ $do_download  == True ]]; then
    cd $download_DIR
    wget -c https://zlib.net/zlib-1.2.13.tar.gz
    wget -c https://www.gfd-dennou.org/arch/netcdf/unidata-mirror/netcdf-c-4.7.4.tar.gz
    wget -c https://www.gfd-dennou.org/arch/netcdf/unidata-mirror/netcdf-fortran-4.5.3.tar.gz
    # wget -c https://github.com/pmodels/mpich/releases/download/v4.0.2/mpich-4.0.2.tar.gz
    wget -c https://www.mpich.org/static/downloads/3.3.1/mpich-3.3.1.tar.gz
    wget -c https://parallel-netcdf.github.io/Release/pnetcdf-1.11.2.tar.gz 
    # Pio is from https://github.com/NCAR/ParallelIO/releases
    # metis is from http://glaros.dtc.umn.edu/gkhome/metis/metis/download
    wget -c https://hdf-wordpress-1.s3.amazonaws.com/wp-content/uploads/manual/HDF5/HDF5_1_12_0/source/hdf5-1.12.0.tar.gz

fi

if [[ $compile_all == True ]]; then
	###################################################################
	export LD_LIBRARY_PATH=$libs_DIR/lib:$LD_LIBRARY_PATH
	export LDFLAGS=-L$libs_DIR/lib
	export CPPFLAGS=-I$libs_DIR/include
	export PATH=$libs_DIR/bin:$PATH
	
	#export PNETCDF_PATH=$libs_DIR/bin:$PATH
	#export PNETCDF=$libs_DIR/bin:$PATH
	
	#export NETCDF_PATH=$libs_DIR/bin:$PATH
	#export NETCDF=$libs_DIR/bin:$PATH
	
	############################## zlib ############################ *** zlib test OK ***
	if [[ ${do_zlib}  == True ]]; then
		echo "=== zlib ===="
		cd $download_DIR
		tar xvf zlib-1.2.13.tar.gz 1>/dev/null 2>&1 
		cd zlib-1.2.13/

		./configure --prefix=$libs_DIR #--static
		make -j 4
		make install
		make check
		cd ..
		rm -rf zlib-1.2.13
		echo "=== zlib (end) ===="
	fi
	
	############################## HDF5 ############################ 
	if [[ ${do_hdf5} == True ]]; then
		echo "=== hdf5 ===="
		cd $download_DIR
		tar xvf hdf5-1.12.0.tar.gz 1>/dev/null 2>&1 
		cd hdf5-1.12.0/

		./configure --prefix=$libs_DIR --enable-parallel --with-zlib=$libs_DIR --enable-fortran #--disable-shared
		make -j 4
		make check
		make install
		cd ..
		rm -rf hdf5-1.12.0
		echo "=== hdf5 (end) ===="
	fi
	
	############################## pnetcdf ############################ PnetCDF has been successfully installed 
	if [[ ${do_pnetcdf} == True ]]; then
		echo "=== pnetcdf ===="
		cd $download_DIR
		tar xvf pnetcdf-1.11.2.tar.gz 1>/dev/null 2>&1 
		cd pnetcdf-1.11.2/

		./configure --prefix=$libs_DIR
		make -j 4
		make check
		make ptest
		make testing
		make install
		cd ..
		rm -rf pnetcdf-1.11.2
		echo "=== pnetcdf (end) ===="
	fi
	
	############################## netcdf-C ############################  Congratulations! You have successfully installed netCDF!
	if [[ ${do_netcdfC} == True ]]; then
		echo "=== netcdf-C ===="
		## -disable-dap：--disable-dap的原因是缺少一个‘curl’的lib
		## 4.1.3以后的版本Fortran和C分开了。把disable--netcdf-4 写进去也无所谓
		cd $download_DIR
		tar xvf netcdf-c-4.7.4.tar.gz 1>/dev/null 2>&1 
		cd netcdf-c-4.7.4/
		
		#export LIBS="-lhdf5_hl -lhdf5 -lz -ldl"
		
		export LD_LIBRARY_PATH=$libs_DIR/lib:$LD_LIBRARY_PATH
		export LDFLAGS=-L$libs_DIR/lib
		export CPPFLAGS=-I$libs_DIR/include
		
		#./configure --prefix=$libs_DIR --disable-dap --enable-pnetcdf --enable-netcdf4 --enable-cdf5 --enable-parallel-tests --disable-shared
		./configure --prefix=$libs_DIR --enable-shared --enable-netcdf4 --disable-filter-testing --disable-dap
		make -j 4 
		make check
		make install
		cd ..
		rm -rf netcdf-c-4.7.4
		echo "=== netcdf-C (end) ===="
	fi
	
	############################## netcdf-F ############################ 
	if [[ ${do_netcdfF} == True ]]; then
		echo "=== netcdf-F ===="
		cd $download_DIR
		tar xvf netcdf-fortran-4.5.3.tar.gz 1>/dev/null 2>&1 
		cd netcdf-fortran-4.5.3/
		
		
		export LD_LIBRARY_PATH=$libs_DIR/lib:$LD_LIBRARY_PATH
		export LDFLAGS=-L$libs_DIR/lib
		export CPPFLAGS=-I$libs_DIR/include
		
		#
		# configure: error: netcdf-c version 4.7.4 or greater is required
		
		nc-config --cflags
		nc-config --libs
		./configure --prefix=$libs_DIR --enable-shared #--enable-parallel-tests #--disable-shared
		
		#CFLAGS=$(nc-config --cflags) LDFLAGS=$(nc-config --libs) ./configure --prefix=$libs_DIR --enable-parallel-tests #--disable-shared
		
		make -j 4 
		make check
		make install
		cd ..
		rm -rf netcdf-fortran-4.5.3
		echo "=== netcdf-F (end) ===="
	fi
	
	############################## Pio ############################
	if [[ ${do_pio} == True ]]; then
		echo "=== PIO ===="
		cd $download_DIR
		tar xvf ParallelIO-pio2_5_8.tar.gz 1>/dev/null 2>&1
		cd ParallelIO-pio2_5_8/

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

	############################## metis ############################
	if [[ ${do_metis} == True ]]; then
		echo "=== metis ===="

        ## metis is serial 
        export CC=icc
        export CXX=icpc
        export FC=ifort
        export F77=ifort
        export F90=ifort

		cd $download_DIR
		tar xvf metis-5.1.0.tar.gz 1>/dev/null 2>&1
		cd metis-5.1.0/
		#./configure --prefix=$libs_DIR
		
		make config prefix=$libs_DIR
		make
		#make check
		make install
		cd ..
		rm -rf metis-5.1.0
		echo "=== metis (end) ===="
	fi
	
fi
```

# MPASv7.3

# Reference

1. [Full WRF and WPS Installation Example (Intel)](https://forum.mmm.ucar.edu/threads/full-wrf-and-wps-installation-example-intel.15229/)

2. [Make ifort error](https://forum.mmm.ucar.edu/threads/make-ifort-error.340/)

In Makefile,

```sh
ifort:
	( $(MAKE) all \
	"FC_PARALLEL = mpif90" \
	"CC_PARALLEL = mpicc" \
	"CXX_PARALLEL = mpicxx" \
	"FC_SERIAL = ifort" \
	"CC_SERIAL = icc" \
	"CXX_SERIAL = icpc" \
	"FFLAGS_PROMOTION = -real-size 64" \
	"FFLAGS_OPT = -O3 -convert big_endian -free -align array64byte" \
	"CFLAGS_OPT = -O3" \
	"CXXFLAGS_OPT = -O3" \
	"LDFLAGS_OPT = -O3" \
	"FFLAGS_DEBUG = -g -convert big_endian -free -CU -CB -check all -fpe0 -traceback" \
	"CFLAGS_DEBUG = -g -traceback" \
	"CXXFLAGS_DEBUG = -g -traceback" \
	"LDFLAGS_DEBUG = -g -fpe0 -traceback" \
	"FFLAGS_OMP = -qopenmp" \
	"CFLAGS_OMP = -qopenmp" \
	"CORE = $(CORE)" \
	"DEBUG = $(DEBUG)" \
	"USE_PAPI = $(USE_PAPI)" \
	"OPENMP = $(OPENMP)" \
	"CPPFLAGS = $(MODEL_FORMULATION) -D_MPI" )
```

where use **mpif90/mpicc/mpicxx** but now we want to use **mpiifort/mpiicc/mpiicpc**.

```sh
# After reading your comment I decided to edit the Makefile, and in the lines of ifort I made the following change:
# Code:
ifort:
        ( $(MAKE) all \
        "FC_PARALLEL = mpiifort" \
        "CC_PARALLEL = mpiicc" \
        "CXX_PARALLEL = mpiicpc" \
        "FC_SERIAL = ifort" \
        "CC_SERIAL = icc" \
        "CXX_SERIAL = icpc" \
```

# For MPASv8.1.0,

<https://github.com/MPAS-Dev/MPAS-Model/releases/tag/v8.1.0>

The top-level Makefile provides a new intel build target for the Intel oneAPI Fortran, C, and C++ compiler suite.
{:.info}

```sh
intel-mpi:   # BUILDTARGET Intel compiler suite with Intel MPI library
	( $(MAKE) all \
	"FC_PARALLEL = mpiifort" \
	"CC_PARALLEL = mpiicc" \
	"CXX_PARALLEL = mpiicpc" \
	"FC_SERIAL = ifort" \
	"CC_SERIAL = icc" \
	"CXX_SERIAL = icpc" \
	"FFLAGS_PROMOTION = -real-size 64" \
	"FFLAGS_OPT = -O3 -convert big_endian -free -align array64byte" \
	"CFLAGS_OPT = -O3" \
	"CXXFLAGS_OPT = -O3" \
	"LDFLAGS_OPT = -O3" \
	"FFLAGS_DEBUG = -g -convert big_endian -free -CU -CB -check all -fpe0 -traceback" \
	"CFLAGS_DEBUG = -g -traceback" \
	"CXXFLAGS_DEBUG = -g -traceback" \
	"LDFLAGS_DEBUG = -g -fpe0 -traceback" \
	"FFLAGS_OMP = -qopenmp" \
	"CFLAGS_OMP = -qopenmp" \
	"PICFLAG = -fpic" \
	"BUILD_TARGET = $(@)" \
	"CORE = $(CORE)" \
	"DEBUG = $(DEBUG)" \
	"USE_PAPI = $(USE_PAPI)" \
	"OPENMP = $(OPENMP)" \
	"CPPFLAGS = $(MODEL_FORMULATION) -D_MPI" )
```

$ make intel-mpi CORE=core options
{:.info}