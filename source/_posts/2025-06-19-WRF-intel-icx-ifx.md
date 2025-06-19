---
layout: post
title: WRF | Intel OneAPI icx/ifx
categories: [WRF]
tags: [Installation, Intel OneAPI, MPAS, NWP, WRF, WPS, ifx, icx]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/lZuD41V.png
banner_img: https://i.imgur.com/lZuD41V.png
---

## WRF build with Intel

- [WRF build with Intel | Oct 29, 2021](https://forum.mmm.ucar.edu/threads/wrf-build-with-intel.10892/)

{% fold info @Full WRF and WPS Installation Example (Intel) %}
```log
I am not aware of anything specific regarding setting things up for WRF compiling. If you're able to compile successfully, I don't believe the installation of oneAPI would be incorrect. Have you verified that you can execute your WRF simulation to completion when NOT using oneAPI? I doubt this will help, but I have some notes I've written up for myself for installing Intel OneAPI. I'll paste them here and you can see if there is anything drastically different. These notes are somewhat specific to compiling on an Amazon Web Services cloud instance (just FYI).

For HPC computing, you must install both of the following toolkits in the order presented
OneAPI Base Toolkit
OneAPI HPC Toolkit

First go to the OneAPI Base Toolkit page and follow the prompts to select the correct version for your system.
1. Select operating system: Linux
2. Select distribution: web and local
3. Select installer: online
4. Follow “Command Line Download” instructions (enter 'wget' command to obtain code, and then the 'sudo bash' command to initiate the build)

Follow prompts to continue through (I chose to not install GPU or Eclipse)
default install location is /opt/intel/oneapi

After you exit, issue the following commands:
Code:
sudo yum update
sudo yum -y install cmake pkgconfig
sudo yum groupinstall “Development Tools”
which cmake pkg-config make gcc g++
you should get
/usr/bin/cmake
/usr/bin/pkg-config
/usr/bin/make
/usr/bin/gcc
/usr/bin/g++


Now install Intel oneAPI HPC Toolkit (that includes the compilers). From the website, choose the same prompts you chose in step 1, and then from your local machine, issue the given 'wget' and then 'sudo bash' commands to obtain the code and initiate installation.

Follow prompts to continue through (I chose to not install GPU or Eclipse)
default install location is /opt/intel/oneapi

Then go to .bashrc and add the following.

Code:
source /opt/intel/oneapi/setvars.sh
 
# settings for building libraries
export CC=icc
export CXX=icpc
export CFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
export CXXFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
export F77=ifort
export FC=ifort
export F90=ifort
export FFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
export CPP='icc -E'
export CXXCPP='icpc -E'
export LD_LIBRARY_PATH=/shared/libs/lib:/shared/libs/netcdf/lib:$LD_LIBRARY_PATH
export LDFLAGS=”-L/shared/libs/lib -L/shared/libs/netcdf/lib”
export CPPFLAGS=”-I/shared/libs/include -I/shared/libs/netcdf/include”
```
{% endfold %}

## Full WRF and WPS Installation Example (Intel)

- [ Full WRF and WPS Installation Example (Intel) | Jan 10, 2024 ](https://forum.mmm.ucar.edu/threads/full-wrf-and-wps-installation-example-intel.15229/)

{% fold info @Full WRF and WPS Installation Example (Intel) %}
```sh
Add something similar to .bashrc
*Note you will need to modify the paths for your specific environment.
*Note this includes a setting to the path for “DIR.” This is just for the sake of simplifying installation.

export PATH=.:/full-path-to-netcdf-directory/netcdf/bin:/full-path-to-libs-directory/bin:${PATH}
export LD_LIBRARY_PATH=/full-path-to-libs-directory/libs/lib:/full-path-to-libs-directory/libs/netcdf/lib:/full-path-to-libs-directory/libs/grib2/lib
export JASPERLIB=/full-path-to-libs-directory/libs/grib2/lib
export JASPERINC=/full-path-to-libs-directory/libs/grib2/include
export NETCDF=/full-path-to-netcdf-directory/netcdf

export DIR=/full-path-to-libs-directory/libs
export CC=icc
export CXX=icpc
export CFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
export CXXFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
export F77=ifort
export FC=ifort
export F90=ifort
export FFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
export CPP='icc -E'
export CXXCPP='icpc -E'
export LDFLAGS="-L/full-path-to-libs-directory/libs/lib -L/full-path-to-libs-directory/libs/netcdf/lib -L/full-path-to-libs-directory/libs/grib2/lib"
export CPPFLAGS="-I/full-path-to-libs-directory/libs/include -I/full-path-to-libs-directory/libs/netcdf/include -I/full-path-to-libs-directory/libs/grib2/include"

Source the .bashrc file
Code:
source .bashrc

Make a directory to install all the libraries.
Code:
mkdir libs

mpich

Code:
wget https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/mpich-3.0.4.tar.gz
tar -xf mpich-3.0.4.tar.gz
cd mpich-3.0.4
./configure --prefix=$DIR
make 2>&1
make install
cd ..
rm -rf mpich*


zlib

Code:
wget https://www2.mmm.ucar.edu/people/duda/files/mpas/sources/zlib-1.2.11.tar.gz
tar xzvf zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure --prefix=$DIR/grib2
make -j 4
make install
cd ..
rm -rf zlib*


HDF5

Code:
wget https://www2.mmm.ucar.edu/people/duda/files/mpas/sources/hdf5-1.10.5.tar.bz2
tar -xf hdf5-1.10.5.tar.bz2
cd hdf5-1.10.5
./configure --prefix=$DIR --with-zlib=$DIR/grib2 --enable-fortran --enable-shared
make -j 4
make install
cd ..
rm -rf hdf5*


NetCDF-c

Code:
wget https://github.com/Unidata/netcdf-c/archive/v4.7.2.tar.gz
tar -xf v4.7.2.tar.gz
cd netcdf-c-4.7.2
./configure --enable-shared --enable-netcdf4 --disable-filter-testing --disable-dap --prefix=$DIR/netcdf
make -j 4
make install
cd ..
rm -rf v4.7.2.tar.gz netcdf-c*


netcdf-fortran

Code:
export LIBS=”-lnetcdf -lhdf5_hl -lhdf5 -lz”
wget https://github.com/Unidata/netcdf-fortran/archive/v4.5.2.tar.gz
tar -xf v4.5.2.tar.gz
cd netcdf-fortran-4.5.2
./configure --enable-shared --prefix=$DIR/netcdf
make -j 4
make install
cd ..
rm -rf netcdf-fortran* v4.5.2.tar.gz


libpng

Code:
wget https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/libpng-1.2.50.tar.gz
tar xzvf libpng-1.2.50.tar.gz
cd libpng-1.2.50
./configure --prefix=$DIR/grib2
make -j 4
make install
cd ..
rm -rf libpng*

jasper

Code:
wget https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/jasper-1.900.1.tar.gz
tar xzvf jasper-1.900.1.tar.gz
cd jasper-1.900.1
./configure --prefix=$DIR/grib2
make -j 4
make install
cd ..
rm -rf jasper*

WRF

Code:
git clone --recurse-submodule https://github.com/wrf-model/WRF.git
cd WRF
./configure (choose options 15 and 1)
./compile em_real -j 4 >& log.compile

WPS

Code:
git clone https://github.com/wrf-model/WPS.git
cd WPS
export WRF_DIR=path-to-WRF-top-level-directory/WRF
./configure (choose option 17)
./compile >& log.compile
```
{% endfold %}

### WRFv4.4.0 with intel compilers

- need **pnetcdf** 
  - like **pnetcdf-1.11.2.tar.gz**

```log
==========================================================================
build started:   Thu Jun 19 16:29:20 HKT 2025
build completed: Thu Jun 19 17:13:09 HKT 2025
 
--->                  Executables successfully built                  <---
 
-rwxrwxr-x 1 wpsze wpsze 54508808 Jun 19 17:13 main/ndown.exe
-rwxrwxr-x 1 wpsze wpsze 54573632 Jun 19 17:13 main/real.exe
-rwxrwxr-x 1 wpsze wpsze 53839560 Jun 19 17:13 main/tc.exe
-rwxrwxr-x 1 wpsze wpsze 58977288 Jun 19 17:10 main/wrf.exe
 
==========================================================================
```

## Compiling WRF using icx instead of icc (Intel compilers)

- [ Compiling WRF using icx instead of icc (Intel compilers) | Mar 13, 2024](https://forum.mmm.ucar.edu/threads/compiling-wrf-using-icx-instead-of-icc-intel-compilers.16191/)
  - **option 76/78 (INTEL ifort/icx)**

{% note primary %}
Update: Issue was resolved with a simple git pull. If you're also running into this issue, you may be running an older version of WRF. In my case I'm using serial, so it was **option 76/78 (INTEL ifort/icx)**.
{% endnote %}

![](https://i.imgur.com/lZuD41V.png)

- These are some of the exports I use for my icx build in dmpar. Hope it helps

{% fold info @icx in dmpar %}
```sh
export METPLUS_Version=5.1.0
export MET_Version=11.1.0
export MET_VERSION=11.1
export METPLUS_DATA=5.1

export HDF5_Version=1_14_3
export Zlib_Version=1.2.13
export Netcdf_C_Version=4.9.0
export Netcdf_Fortran_Version=4.6.1
export Mpich_Version=4.1.2
export Libpng_Version=1.6.39
export Jasper_Version=1.900.1
export Pnetcdf_Version=1.12.3

export WRF_VERSION=4.5.2
export WPS_VERSION=4.5


  export CC=icx
  export CXX=icpx
  export FC=ifx
  export F77=ifx
  export F90=ifx
  export MPIFC='mpiifx'
  export MPIF77='mpiifx'
  export MPIF90='mpiifx'
  export MPICC='mpiicx'
  export MPICXX='mpiicpx'
  export CFLAGS="-fPIC -fPIE -O3 -Wno-implicit-function-declaration -Wno-incompatible-function-pointer-types" #-Wno-absolute-value -Wno-unknown-warning-option -Wno-unused-command-line-argument "
  export FFLAGS="-m64"
  export FCFLAGS="-m64"
```
{% endfold %}

# References

1. [Installing WRF from scratch in an HPC using Intel (Classic) Compilers](https://pratiman-91.github.io/2020/09/01/Installing-WRF-from-scratch-in-an-HPC-using-Intel-Compilers.html)
2. [WRF 4.5.1 compiling failed with INTEL 2023 version of ifort Fortran and icc 223. | Aug 8, 2023](https://forum.mmm.ucar.edu/threads/wrf-4-5-1-compiling-failed-with-intel-2023-version-of-ifort-fortran-and-icc-223.13798/#post-35179)
3. [Compile with OneAPI Intel | Oct 29, 2024](https://forum.mmm.ucar.edu/threads/compile-with-oneapi-intel.19689/)
   1. Has anyone tried to compile the WRF system and associated libraries with the OneAPI Intel compiler. Here there is an explanation on how to compile with classic Intel compiler. But soon (2023-2024), OneAPI C/C++ compiler (icx, icpx) will succeed to the classic (icc,icpc) compiler.
   2. The linked example should be a good starting point for using the OneAPI compilers, especially as they share similar flags. **You might be able to just replace icc -> icx and ifort -> ifx**
   3. Stanzas for the OneAPI compilers already exist in the latest WRF/WPS releases so once the dependencies are built you should be able to compile WRF.
4. [Issues configuring WPS 4.6.0 w/ WRF 4.7.0 using configure_new. (Solved) | May 29, 2025 ](https://forum.mmm.ucar.edu/threads/issues-configuring-wps-4-6-0-w-wrf-4-7-0-using-configure_new-solved.22161/#post-53696)
5. [Difference in seed_dim_stag Dimension After WRF Porting to New Cluster | Apr 26, 2025](https://forum.mmm.ucar.edu/threads/difference-in-seed_dim_stag-dimension-after-wrf-porting-to-new-cluster.21805/)
   1. I previously built WRF on our old Linux cluster using the PGI compiler. We've recently upgraded our cluster and I successfully built **WRF v4.6.1** on the new system using the **Intel compiler with option 78 (dmpar) – INTEL (ifx/icx): oneAPI LLVM**.
   2. These differences are randomly distributed and the values are small over most areas, --- we did see a similar model behavior when running the same case using different number of processors and in different machines. From this perspective, I would say that it is acceptable.
   3. We used to have a website that provides test data, NCL scripts and verification of model output. Unfortunately this page seems gone because we are reconstructing WRF website and update documents. Sorry for the inconvenience caused by this.
6. [使用Intel oneAPI编译器安装WRF和WPS | 2024](https://zhuanlan.zhihu.com/p/679043681)
   1. 修改configure.wrf文件
      1. 大概在第136行，修改MD_FC=mpiifx，DM_CC=mpiicx。
   2. 如果第2步选择了79，因为oneAPI升级icc命令为icx了，icx命令没有-auto这个选项，需要修改OMPCC=-qopenmp（删除-auto）