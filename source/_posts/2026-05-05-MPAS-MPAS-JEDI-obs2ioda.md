---
layout: post
title: MPAS | MPAS-JEDI | obs2ioda
categories: [MPAS-JEDI]
tags: [MPAS, DA, MPAS-JEDI, Spack, Spack-Stack]
author: wpsze
date: 2026-05-05 06:25:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/6oGdLJk.png
banner_img: https://i.imgur.com/6oGdLJk.png
---

- [**NWP | BUFR data | GFS - Binary Universal Form for the Representation of meteorological data**](https://waipangsze.github.io/2026/02/27/NWP-BUFR-data/)
- [MPAS | MPAS-JEDI](https://waipangsze.github.io/2024/05/29/MPAS-JEDI_note/)
- [**MPAS | MPAS-JEDI V3.0.2**](https://waipangsze.github.io/2026/02/13/MPAS-MPAS-JEDI-V302/)
- [**MPAS | MPAS-JEDI | spack-stack 1.9.3**](https://waipangsze.github.io/2026/04/28/MPAS-MPAS-JEDI-spack-stack-1-9-3/)
- [MPAS | Joint MPAS/WRF Users Workshop 2025 | MPASv8.3.0](https://waipangsze.github.io/2025/06/05/MPAS-Joint-MPAS-WRF-Users-Workshop-2025/)
- [**Spack-Stack: building JEDIbundles on your own machine | 202506NCAS** ](https://www2.mmm.ucar.edu/projects/mpas-jedi/tutorial/202506NCAS/lectures/12-spackstack.pdf)

---

# obs2ioda

- To convert **NCEP PREPBUFR/BUFR files** and **Himawari Standard Data FLDK files** to ioda-v1 or ioda-v2 format.
- To convert GOES-ABI netCDF to ioda-v1 format.
- File types that obs2ioda can handle:
  - prepbufr.gdas.YYYYMMDD.tHHz.nr
  - gdas.satwnd.tHHz.YYYYMMDD.bufr
  - gdas.gpsro.tHHz.YYYYMMDD.bufr
  - gdas.1bamua.tHHz.YYYYMMDD.bufr
  - gdas.1bmhs.tHHz.YYYYMMDD.bufr
  - gdas.airsev.tHHz.YYYYMMDD.bufr
  - gdas.cris.tHHz.YYYYMMDD.bufr
  - gdas.crisf4.tHHz.YYYYMMDD.bufr
  - gdas.mtiasi.tHHz.YYYYMMDD.bufr
  - HS_H08_YYYYMMDD_HH00_BNN_FLDK_R20_S0210.DAT
  - OR_ABI-L1b-Rad nc files

## Observation data sources

**Observation data sources**:

- <https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/>
- <https://gdex.ucar.edu/datasets/d337000/>
- <https://gdex.ucar.edu/datasets/d735000/>
- <https://registry.opendata.aws/noaa-goes/>
- <https://registry.opendata.aws/noaa-himawari/>

## github

- <https://github.com/jamiebresch/obs2ioda>
- <https://github.com/jamiebresch/obs2ioda/blob/main/obs2ioda-v2/README.md>
  - `Compile: cd obs2ioda-v2/src; make`
  - `NCEP BUFR` library (<https://github.com/NOAA-EMC/NCEPLIBS-bufr>) is required to compile obs2ioda-v2.x.
    - `cmake 3.15`
    - `bufr_v11.5.0`
  - Edit `obs2ioda-v2/src/Makefile` to set proper `BUFR_LIB` before make.

# conda env

- may apply channel `- conda-forge` only.

{% fold info @conda env: obs2ioda.yml %}
```sh
name: obs2ioda
channels:
- anaconda
- conda-forge
dependencies:
- _libgcc_mutex=0.1=main
- _openmp_mutex=5.1=1_gnu
- attr=2.5.2=h47b2149_0
- binutils_impl_linux-64=2.44=h4b9a079_2
- blas=1.0=openblas
- blosc=1.21.6=hef167b5_0
- bzip2=1.0.8=h5eee18b_6
- c-ares=1.34.6=hd44998d_0
- ca-certificates=2025.12.2=h06a4308_0
- certifi=2026.01.04=py314h06a4308_0
- cftime=1.6.5=py314h6a40391_0
- gcc=10.4.0=hb92f740_13
- gcc_impl_linux-64=10.4.0=h5231bdf_19
- gfortran=10.4.0=h0c96582_13
- gfortran_impl_linux-64=10.4.0=h7d168d2_19
- hdf4=4.2.15=h2a13503_7
- hdf5=1.14.6=nompi_h19486de_106
- icu=78.2=h33c6efd_0
- kernel-headers_linux-64=4.18.0=h3108a97_1
- keyutils=1.6.3=hb9d3cd8_0
- krb5=1.22.2=ha1258a1_0
- ld_impl_linux-64=2.44=h153f514_2
- libaec=1.1.5=h088129d_0
- libcurl=8.18.0=hcf29cc6_1
- libedit=3.1.20250104=pl5321h7949ede_0
- libev=4.33=h7f8727e_1
- libexpat=2.7.4=h7354ed3_0
- libffi=3.5.2=h3435931_0
- libgcc=15.2.0=h69a1729_7
- libgcc-devel_linux-64=10.4.0=hd38fd1e_19
- libgcc-ng=15.2.0=h166f726_7
- libgfortran=15.2.0=h166f726_7
- libgfortran5=15.2.0=hc633d37_7
- libgomp=15.2.0=h4751f2c_7
- libiconv=1.18=h3b78370_2
- libjpeg-turbo=3.1.3=h47b2149_0
- liblzma=5.8.2=hb03c661_0
- liblzma-devel=5.8.2=hb03c661_0
- libmpdec=4.0.0=h5eee18b_0
- libnetcdf=4.9.3=nompi_hbf2fc22_104
- libnghttp2=1.67.0=had1ee68_0
- libopenblas=0.3.31=hf7dbefb_0
- libsanitizer=10.4.0=h5246dfb_19
- libsqlite=3.51.2=hf4e2dac_0
- libssh2=1.11.1=hcf80075_0
- libstdcxx=15.2.0=h39759b7_7
- libstdcxx-ng=15.2.0=hc03a8fd_7
- libuuid=2.41.3=h5347b49_0
- libxml2=2.15.1=he237659_1
- libxml2-16=2.15.1=hca6bf5a_1
- libzip=1.11.2=h6991a6a_0
- libzlib=1.3.1=hb25bd0a_0
- lz4-c=1.9.4=h6a678d5_1
- ncurses=6.5=h7934f7d_0
- netcdf-fortran=4.6.2=nompi_h90de81b_102
- netcdf4=1.7.2=py314ha2d4c0d_2
- numpy=2.4.2=py314hda7dee8_0
- numpy-base=2.4.2=py314h5cadfd5_0
- openssl=3.6.1=h35e630c_1
- packaging=25.0=py314h06a4308_1
- pip=26.0.1=pyhc872135_0
- python=3.14.3=h32b2ec7_101_cp314
- python_abi=3.14=2_cp314
- readline=8.3=hc2a1206_0
- setuptools=80.10.2=py314h06a4308_0
- snappy=1.2.2=h4bcf44c_1
- sysroot_linux-64=2.28=h3108a97_1
- tk=8.6.13=noxft_h366c992_103
- tzdata=2025c=he532380_0
- wheel=0.46.3=py314h06a4308_0
- xz=5.8.2=ha02ee65_0
- xz-gpl-tools=5.8.2=ha02ee65_0
- xz-tools=5.8.2=hb03c661_0
- zstd=1.5.7=h11fc155_0
```
{% endfold %}

{% fold info @install_obs2ioda-v2.sh %}
```sh
#!/bin/bash

#------------------------------------------------#
#Author:         wpsze
#Email：         waipangsze@gmail.com
#date:           
#Version:        0.0 
#Description:    Install libraries that MPAS needs 
#Copyright (C)： 2022 All rights reserved
#------------------------------------------------#

############################## Directory Listing ############################
source /home/wpsze/micromamba/bin/activate obs2ioda
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

export HOME=`pwd`

### NCEP BUFR library environment
export NCEP_BUFR_library_dir="/home/wpsze/cpas/CPAS-DA/cpas-da/NCEPLIBS-bufr/NCEPLIBS-bufr/selflibbufr/"
export PATH=$NCEP_BUFR_library_dir/bin:$PATH
export LD_LIBRARY_PATH=$NCEP_BUFR_library_dir/lib64:$LD_LIBRARY_PATH
export LIBRARY_PATH=$NCEP_BUFR_library_dir/lib64:$LIBRARY_PATH

export NETCDF="/home/wpsze/micromamba/envs/obs2ioda"

echo "====== gfortran version ====="
#
# gfortran: error: unrecognized command line option ‘-fallow-argument-mismatch’; did you mean ‘-Wno-argument-mismatch’?
# gfortran: error: unrecognized command line option ‘-fallow-invalid-boz’; did you mean ‘-Wno-invalid-pch’?
# 1. The most recommended approach is to update your compiler to GFortran version 10 or newer, which officially supports the flag
# 2. Replace -fallow-argument-mismatch with -Wno-argument-mismatch in your build configuration or command line.  

#ldd /home/wpsze/DA-obs2ioda/obs2ioda-H9/obs2ioda-v2/src/obs2ioda-v2.x 
gfortran --version

echo "====== netcdf.mod ======="
# 
# Fatal Error: Cannot open module file 'netcdf.mod' for reading at (1): No such file or directory
#
nf-config --fflags
nf-config --flibs

#cd ./obs2ioda-H9/obs2ioda-v2/src/
#cd ./obs2ioda-v2/src/
cd ./obs2ioda-v2-jamiebresch/src/

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