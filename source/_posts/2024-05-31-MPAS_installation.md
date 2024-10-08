---
layout: post
title: MPAS | MPAS Installation
categories: [MPAS]
tags: [Installation]
author: wpsze
index_img: 
banner_img: 
---

# For MPASv7.0 and above,

```sh
#!/bin/bash

#------------------------------------------------#
#Author:         wpsze
#Email：         
#date:           
#Version:        0.0 
#Description:    Install libraries that MPAS needs 
#Copyright (C)： 2022 All rights reserved
#------------------------------------------------#

## ./mpas_env_install_gcc10_3_0.sh 2>&1 |tee log.log

############################## Directory Listing ############################
export HOME=`pwd`

source /home/wpsze/MPAS-A/mpas_env.sh

gcc --version
g++ --version
gfortran --version
mpif90 --version
mpicc --version
make --version
cmake --version

echo $NETCDF
echo $PNETCDF
echo $PIO

##--Intel MPI与Open MPI、MPICH等MPI实现不同：
##--mpiicc、mpiicpc和mpiifort命令：使用Intel编译器
##--mpicc、mpif90和mpifc命令：默认使用GNU编译器

do_init_atmosphere=True # True, False
do_atmosphere=True
do_build_tables=True

mpas_version="MPASv8"

echo "Copy MPAS-Model to build-mpas ... "
rm -rf build-mpas
cp -r MPAS-Model-8.0.0 build-mpas
cd build-mpas

############################## init_atmosphere ############################ -g -O0 -fbacktrace
if [[ ${do_init_atmosphere} == True ]]; then
	echo "=== init_atmosphere ===="
	
	make clean CORE=init_atmosphere
	make clean CORE=atmosphere
	
	# mpas_dmpar.F:
 	# call MPI_Allreduce(inArray, outArray, nElements, MPI_INTEGERKIND, MPI_MAX, dminfo % comm, mpi_ierr) 
	# Warning: Rank mismatch between actual argument at (1) and actual argument at (2) (scalar and rank-1)
	
	#export FFLAGS="-w -fallow-argument-mismatch -O2"
	#export FCFLAGS="-w -fallow-argument-mismatch -O2"

	# make gfortran CORE=init_atmosphere PRECISION=single DEBUG=true USE_PIO2=true >& log_build2.txt
	
	make -j8 gfortran CORE=init_atmosphere PRECISION=single USE_PIO2=true 2>&1 |tee log_build_init.txt
	
	### Check MPAS model.
	#ls -la init_atmosphere_model
	echo "=== init_atmosphere (end) ===="
: '
*******************************************************************************
MPAS was built with default single-precision reals.
Debugging is off.
Parallel version is on.
Papi libraries are off.
TAU Hooks are off.
MPAS was built without OpenMP support.
MPAS was built with .F files.
The native timer interface is being used
Using the PIO 2 library.
*******************************************************************************
make[1]: Leaving directory /home/wpsze/MPAS-A/mpasv73/MPAS-Model-7.3
If compilation of the init_atmosphere core was successful, we should also have an executable file named init_atmosphere_model
'
fi

############################## atmosphere_model ############################
if [[ ${do_atmosphere} == True ]]; then
	echo "=== atmosphere_model ===="
	# To preserve all executables except atmosphere_model and clean the MPAS infrastructure, run: 
	make clean CORE=atmosphere
	
	#export FFLAGS="-w -fallow-argument-mismatch -O2"
	#export FCFLAGS="-w -fallow-argument-mismatch -O2"

	# DEBUG=true
	
	make gfortran CORE=atmosphere PRECISION=single USE_PIO2=true 2>&1 |tee log_build_atm.txt
	
	### Check MPAS model.
	#ls -la atmosphere_model
	echo "=== atmosphere_model (end) ===="
: '
*******************************************************************************
MPAS was built with default single-precision reals.
Debugging is off.
Parallel version is on.
Papi libraries are off.
TAU Hooks are off.
MPAS was built without OpenMP support.
MPAS was built with .F files.
The native timer interface is being used
Using the PIO 2 library.
*******************************************************************************
'

wait

fi

if [[ -f atmosphere_model ]]; then
	mkdir -p $HOME/${mpas_version}
	mv streams.init_atmosphere $HOME/${mpas_version}
	mv namelist.init_atmosphere $HOME/${mpas_version}
	mv init_atmosphere_model $HOME/${mpas_version}

	mv atmosphere_model $HOME/${mpas_version}
	mv build_tables $HOME/${mpas_version}
	mv namelist.atmosphere $HOME/${mpas_version}
	mv streams.atmosphere $HOME/${mpas_version}
	mv stream_list.atmosphere.diagnostics $HOME/${mpas_version}
	mv stream_list.atmosphere.output $HOME/${mpas_version}
	mv stream_list.atmosphere.surface $HOME/${mpas_version}

	mv ./src/core_atmosphere/physics/physics_wrf/files/* $HOME/${mpas_version}/

	if [[ ${do_build_tables} == True ]]; then
		cd $HOME/${mpas_version}
		./build_tables
	fi

fi
```

and,

```sh
#!/bin/bash

#------------------------------------------------#
#Author:         wpsze
#homeail：        
#date:           
#Version:        0.0 
#Description:    Install libraries that MPAS needs 
#Copyright (C)： 2022 All rights reserved
#------------------------------------------------#

############################## Directory Listing ############################
export HOME=`pwd`
export download_DIR=$HOME/mpas_install/
export libs_DIR=$HOME/Library/

export PATH=/home/wpsze/GNU_GCC/Library/gcc-v9.3.0/bin:$PATH
export LD_LIBRARY_PATH=/home/wpsze/GNU_GCC/Library/gcc-v9.3.0/lib/${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}
#export LD_LIBRARY_PATH=/home/wpsze/GNU_GCC/Library/gcc-v9.3.0/lib:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/home/wpsze/GNU_GCC/Library/gcc-v9.3.0/lib64:$LD_LIBRARY_PATH

export LD_LIBRARY_PATH=$libs_DIR/lib:$LD_LIBRARY_PATH
export LDFLAGS=-L$libs_DIR/lib
export CPPFLAGS=-I$libs_DIR/include

source /home/wpsze/micromamba/etc/profile.d/micromamba.sh # install cmake
micromamba activate mpas_env

gcc --version
gfortran --version

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

do_mpich=False
do_zlib=False
do_hdf5=False
do_pnetcdf=False
do_netcdfC=False
do_netcdfF=False
do_pio=False
do_metis=True

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
	
	#export PIO=$libs_DIR/bin:$PATH

	############################## MPICH ############################
	if [[ ${do_mpich}  == True ]]; then
		#export PATH=$libs_DIR/gcc-v8.3.0/bin:$PATH
		#export LD_LIBRARY_PATH=$libs_DIR/gcc-v8.3.0/lib:$LD_LIBRARY_PATH
		#export LD_LIBRARY_PATH=$libs_DIR/gcc-v8.3.0/lib64:$LD_LIBRARY_PATH
			
		echo "=== MPICH ===="
		cd $download_DIR
		tar xvf mpich-4.0.2.tar.gz 1>/dev/null 2>&1  # 3.3.1 4.0.2
		cd mpich-4.0.2/

		./configure --prefix=$libs_DIR --with-device=ch3 #--enable-pic
		make -j 4
		make check
		make install
		#make testing
		cd ..
		rm -rf mpich-4.0.2
		echo "=== MPICH (end) ===="
	fi

	### Compilers
	## commment out for mpich
	export SERIAL_FC=gfortran
	export SERIAL_F77=gfortran
	export SERIAL_CC=gcc
	export SERIAL_CXX=g++
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
		
		# have to define below parameters
		export FC=mpifort
		export F77=mpifort
		export F90=mpifort
		export CC=mpicc
		export CXX=mpic++
		
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

	############################## metis ############################
	if [[ ${do_metis} == True ]]; then
		echo "=== metis ===="
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

# For MPASv6.3 and before

Install cmake, curl and unzip !!! 
{:.warning}

```sh
source /home/wpsze/micromamba/etc/profile.d/micromamba.sh # install cmake
micromamba activate mpas_env
```

# mpas_env.sh

```sh
#!/bin/bash

#------------------------------------------------#
#Author:         wpsze
#Email：         
#date:           2022-10-21 23:13:49
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2022 All rights reserved
#------------------------------------------------#

# Install: make/cmake
# Actually: can install gnu 
source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate mpas_env

export libs_DIR=/EM/wpsze/MPAS-A/modules_library/Library/

# GCC env
export PATH=/home/wpsze/GNU_GCC/Library/gcc-v9.3.0/bin:$PATH
export LD_LIBRARY_PATH=/home/wpsze/GNU_GCC/Library/gcc-v9.3.0/lib/${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}
export LD_LIBRARY_PATH=/home/wpsze/GNU_GCC/Library/gcc-v9.3.0/lib64:$LD_LIBRARY_PATH

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

##--Intel MPI与Open MPI、MPICH等MPI实现不同：
##--mpiicc、mpiicpc和mpiifort命令：使用Intel编译器
##--mpicc、mpif90和mpifc命令：默认使用GNU编译器
## 
export FC=mpifort
export F77=mpifort
export F90=mpifort
export CC=mpicc
export MPIF90=mpifort
export MPIF77=mpifort
export MPIFC=mpifort
export MPICC=mpicc

gcc --version
gfortran --version
```