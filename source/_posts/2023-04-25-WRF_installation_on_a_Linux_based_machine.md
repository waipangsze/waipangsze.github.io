---
layout: post
title: "WRF installation on a Linux-based machine"
categories: [WRF]
tags: [NWP,WRF,WPS,Installation,gnu,gcc]
mathjax: true
author: wpsze
---

# WRF

- Version 1.0: WRF was first released December 2000
- Version 2.0: May 2004
- Version 3.0: April 2008
- Version 4.0: June 2018 (add hybrid vertical coordinate)
- Version 4.1: April 2019
- Version 4.2: April 2020
- Version 4.3: May 2021
- Version 4.4: April 2022
- Version 4.5: April 2023
     - 4.5.1: July 2023
     - 4.5.2: December 2023
- Version 4.6: May 2024

# WRF installation on a Linux-based machine

Weather Research & Forecasting Model (WRF) is a state of the art mesoscale numerical weather prediction system designed for both atmospheric research and operational forecasting applications [^1].

The installation steps are,

1. Source gcc compiler
2. Test gcc compiler
3. Install all requried libraries
4. Test libraries
5. Install WRF
6. Install WPS

Three shell scripts are under the same directory,

> gcc_env.sh
> wrf_env.sh
> wrf_env_install.sh
> wrf_wps_install.sh

## GCC version (gcc_env.sh)

```sh
#!/bin/bash

export gcc_libs_DIR=/home/wpsze/gcc/
export LD_LIBRARY_PATH=$gcc_libs_DIR/lib:$LD_LIBRARY_PATH
export PATH=$gcc_libs_DIR/bin:$PATH

export gcc_version="gcc-v4.8.5" # 4.8.5/8.3.0/9.3.0/10.3.0/12.2.0

export PATH=$gcc_libs_DIR/${gcc_version}/bin:$PATH
export LD_LIBRARY_PATH=$gcc_libs_DIR/${gcc_version}/lib:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=$gcc_libs_DIR/${gcc_version}/lib64:$LD_LIBRARY_PATH

gcc --version
gfortran --version
```

### Testing System Compatibility

We have to test the compiler system in your computer/clsuter.
#### Download test tar files,

> http://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/Fortran_C_tests.tar

#### run_gcc_test.sh

```sh
#!/bin/bash
source gcc_env.sh
echo "=========================================================================================="
echo " All tests are from http://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/Fortran_C_tests.tar "
gfortran --version
gcc --version
cpp --version
echo "=========================================================================================="
echo "=============================== Fortran_C_tests.tar  ====================================="
echo "=========================================================================================="
# Unpack the test files by entering the following input into the terminal:
# tar -xf Fortran_C_tests.tar

# TEST #1: FIXED FORMAT FORTRAN TEST
gfortran TEST_1_fortran_only_fixed.f
./a.out
# SUCCESS test 1 fortran only fixed format

# TEST #2: FREE FORMAT FORTRAN TEST
gfortran TEST_2_fortran_only_free.f90
./a.out
# Assume Fortran 2003: has FLUSH, ALLOCATABLE, derived type, and ISO C Binding
# SUCCESS test 2 fortran only free format

# TEST #3: C
gcc TEST_3_c_only.c
./a.out
# SUCCESS test 3 c only

# TEST #4: FORTRAN CALLING A C FUNCTION (GCC AND GFORTRAN HAVE DIFFERENT DEFAULTS, 
# SO WE FORCE BOTH TO ALWAYS USE 64 BIT (-M64) WHEN COMBINING THEM)
gcc -c -m64 TEST_4_fortran+c_c.c
gfortran -c -m64 TEST_4_fortran+c_f.f90
gfortran -m64 TEST_4_fortran+c_f.o TEST_4_fortran+c_c.o
./a.out
# C function called by Fortran
# Values are xx = 2.00 and ii = 1
# SUCCESS test 4 fortran calling c

# TEST #5: CSH
./TEST_csh.csh
# SUCCESS csh test
#  TEST #6: PERL
./TEST_perl.pl
# SUCCESS perl test
#  TEST #7: SH
./TEST_sh.sh
# SUCCESS sh test
```

## WRF v3.8.1 [^2]

Using a version of gfortran which is 4.4.0 or newer is recommended. 

> https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/
> netcdf-4.1.3
> mpich-3.0.4
> zlib-1.2.7
> libpng-1.2.50
> JasPer-1.900.1

The above libraries are requried for running WRF and download to folder wrf_instsall/. 

```sh
source gcc_env.sh
#export gcc_version="gcc-v4.8.5" # and change gcc_env.sh !!!!!!

export HOME=`pwd`
export download_DIR=$HOME/wrf_install/
export libs_DIR=$HOME/Library/

export LD_LIBRARY_PATH=$libs_DIR/lib:$LD_LIBRARY_PATH
export LDFLAGS=-L$libs_DIR/lib
export CPPFLAGS=-I$libs_DIR/include

gcc --version
gfortran --version

echo $HOME
echo $download_DIR
echo $libs_DIR

mkdir -p wrf_install
mkdir -p Library

############################## zlib ############################
cd $download_DIR
rm -rf zlib-1.2.7/
tar xvf zlib-1.2.7.tar.gz

cd zlib-1.2.7/

./configure --prefix=$libs_DIR
make
make install

############################## libpng ############################
cd $download_DIR
rm -rf libpng-1.2.50
tar xvf libpng-1.2.50.tar.gz

cd libpng-1.2.50/

./configure --prefix=$libs_DIR
make
make install

############################## netcdf-C ############################
cd $download_DIR
rm -rf netcdf-4.1.3
tar xvf netcdf-4.1.3.tar.gz

cd netcdf-4.1.3/

export LD_LIBRARY_PATH=$libs_DIR/lib:$LD_LIBRARY_PATH
export LDFLAGS=-L$libs_DIR/lib
export CPPFLAGS=-I$libs_DIR/include

# ./configure --prefix=$libs_DIR
./configure --prefix=$libs_DIR --disable-dap --disable-netcdf-4 --disable-shared
make
make install

export PATH=$libs_DIR/bin:$PATH
export NETCDF=$libs_DIR
############################## JasPer ############################
cd $download_DIR
rm -rf jasper-1.900.1
tar vxf jasper-1.900.1.tar.gz

cd jasper-1.900.1

#sed -i 's|char|const char|g' src/libjasper/jpg/jpg_dummy.c

./configure --prefix=$libs_DIR

make
make install
##############################MPICH############################
cd $download_DIR
rm mpich-3.0.4
tar vxf mpich-3.0.4.tar.gz

cd mpich-3.0.4/
./configure --prefix=$libs_DIR #--with-device=ch3

make
make install

export PATH=$libs_DIR/bin:$PATH
```

and, combine all required libaries in wrf_env.sh

```sh
#!/bin/bash
source gcc_env.sh
export HOME=`pwd`
export libs_DIR=$HOME/Library/
export LD_LIBRARY_PATH=$libs_DIR/lib:$LD_LIBRARY_PATH
export LDFLAGS=-L$libs_DIR/lib
export CPPFLAGS=-I$libs_DIR/include
export PATH=$libs_DIR/bin:$PATH
export NETCDF=$libs_DIR
```

### Library Compatibility Tests

We have to test the Library in your computer/clsuter.
#### Download test tar files,

> http://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/Fortran_C_NETCDF_MPI_tests.tar

#### run_library_test.sh
```sh
#!/bin/bash
source wrf_env.sh

echo "================================================================="
echo "=================== Fortran_C_NETCDF_MPI_tests.tar =============="
echo "================================================================="

# To unpack the tar file, type:
# wget -c https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/Fortran_C_NETCDF_MPI_tests.tar
# tar -xf Fortran_C_NETCDF_MPI_tests.tar

# Test #1: Fortran + C + NetCDF

# The NetCDF-only test requires the include file from the NETCDF package be in this directory. Copy the file here:
cp ${NETCDF}/include/netcdf.inc .

# Compile the Fortran and C codes for the purpose of this test 
# (the -c option says to not try to build an executable). Type the following commands:
gfortran -c 01_fortran+c+netcdf_f.f
gcc -c 01_fortran+c+netcdf_c.c
gfortran 01_fortran+c+netcdf_f.o 01_fortran+c+netcdf_c.o \
     -L${NETCDF}/lib -lnetcdff -lnetcdf
./a.out

# The following should be displayed on your screen:
# C function called by Fortran
# Values are xx = 2.00 and ii = 1
# SUCCESS test 1 fortran + c + netcdf

echo "================================================================="
# Test #2: Fortran + C + NetCDF + MPI

# The NetCDF+MPI test requires include files from both of these packages be in this directory, 
# but the MPI scripts automatically make the mpif.h file available without assistance, 
# so no need to copy that one. Copy the NetCDF include file here:
cp ${NETCDF}/include/netcdf.inc .

# Note that the MPI executables mpif90 and mpicc are used below when compiling. Issue the following commands:
mpif90 -c 02_fortran+c+netcdf+mpi_f.f
mpicc -c 02_fortran+c+netcdf+mpi_c.c
mpif90 02_fortran+c+netcdf+mpi_f.o \
02_fortran+c+netcdf+mpi_c.o \
     -L${NETCDF}/lib -lnetcdff -lnetcdf
mpirun ./a.out

# The following should be displayed on your screen:
# C function called by Fortran
# Values are xx = 2.00 and ii = 1
# status = 2
# SUCCESS test 2 fortran + c + netcdf + mpi
echo "================================================================="
```

### Install WRFv3.8.1
#### Download 
> http://www2.mmm.ucar.edu/wrf/src/WRFV3.8.1.TAR.gz 

```sh
#!/bin/bash
# WRF will not compile using GCC version 6 or higher. 
# It will only work with version 4.8.5. 
# The GCC6 module loads automatically when you log in.
# If you unload it, the system will default to GCC 4.8.5.

source wrf_env.sh
export HOME=`pwd` 
export libs_DIR=/home/wpsze/WRFv381/Library/ # HOME = /home/wpsze/WRFv381/
export download_DIR=$HOME/wrf_install/

# gunzip WRFV3.8.1.TAR.gz
# tar -xf WRFV3.8.1.TAR

cd $download_DIR

cd WRFV3/

export WRF_DIR="WRFV3"

#./clean -a ## running or not

./configure 
##  34 (dmpar) GNU [gfortran/gcc]

./compile -j 4 em_real 2>&1 | tee compile.log
#./compile em_real 2>&1 | tee compile.log

# # #It should fail on the first time (namely main/real.exe is not created)
# # #with the following error:
# # #+++++++++++++++++++++++++++++++++++++++++++++++++++++++++
# # #real_em.f90:32.7:

# # #   USE module_initialize_real, ONLY : wrfu_initialize, find_my_parent, find_my_
# # 	1
# # #Fatal Error: Can't open module file 'module_initialize_real.mod' for reading at (1): No such file or
# # #directory
# # #nup_em.f90:90.7:

# # #   USE module_initialize_real, only : wrfu_initialize
# # #       1
# # #Fatal Error: Can't open module file 'module_initialize_real.mod' for reading at (1): No such file or
# # #directory
# # #+++++++++++++++++++++++++++++++++++++++++++++++++++++++++

# # #Then it should end successfully when running the
# # #command a second time (do not understand why but it works...)

# # # --->                  Executables successfully built                  <---
```

This should return the following four files:

> wrf.exe
> real.exe
> ndown.exe
> tc.exe

### Install WPSv3.8.1
#### Download

> http://www2.mmm.ucar.edu/wrf/src/WPSV3.8.1.TAR.gz

```sh
#!/bin/bash
source wrf_env.sh
export HOME=`pwd` 
export libs_DIR=/home/wpsze/WRFv381/Library/ # HOME = /home/wpsze/WRFv381/
export download_DIR=$HOME/wrf_install/

cd $download_DIR
#tar xvf xxxx.tar.gz
cd WPS/
export WRF_DIR=../WRFV3/
export JASPERLIB=$libs_DIR/lib/
export JASPERINC=$libs_DIR/include/

#then option 1 Linux x86_64, gfortran    (serial)

# ./clean -a

./configure

# 1 (dmpar) gfortran

./compile 2>&1 | tee compile.log
```

This should return the three executable files needed to run WPS.

> geogrid.exe
> metgrid.exe
> ungrib.exe

## WRF v4.4.0


[^1]: [WRF](https://www.mmm.ucar.edu/models/wrf)
[^2]: [WRFv3.8.1 installation on a Linux machine](https://metclim.ucd.ie/2017/06/wrf-installation-on-a-linux-machine/)
