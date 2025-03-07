---
layout: post
title: WRF | WRF-DA Installation
categories: [WRF]
tags: [DA, Installation, gnu, MPAS, NWP, WRF, WRFDA]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/RwpxULg.png
banner_img: https://i.imgur.com/RwpxULg.png
---

# Preparation

Install all requried libraries of WRF.

Beginning with version 4.0, the basic WRF tar file now includes WRFDA, WRFPLUS, and WRF-Chem. Next to the WRFDA tar file link, there is a link to 'updates.' Take a look at that for additional information regarding this change. You can also find updated compiling instructions in the Users Guide:
<http://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.0/users_guide_chap6.html>

# WRF-DA

[WRF Data Assimilation System Users Page](https://www2.mmm.ucar.edu/wrf/users/wrfda/)

The WRFDA system is in the public domain and is freely available for community use. It is designed to be a flexible, state-of-the-art atmospheric data assimilation system that is portable and efficient on available parallel computing platforms. WRFDA is suitable for use in a broad range of applications, across scales ranging from kilometers for regional and mesoscale modeling to thousands of kilometers for global scale modeling.

The Mesoscale and Microscale Meteorology (MMM) Laboratory of NCAR currently maintains and supports a subset of the overall WRF code (Version 4) that includes:

- WRF Software Framework (WSF)
- Advanced Research WRF (ARW) dynamic solver, including one-way, two-way nesting and moving nests, grid and observation nudging
- WRF Pre-Processing System (WPS)
- **WRF Data Assimilation System (WRFDA)**
- Numerous physics packages contributed by WRF partners and the research community

## Installation

- wrf_env.sh

```sh
#!/bin/bash

export PATH=/home/wpsze/GNU_GCC/Library/gcc-v9.3.0/bin:$PATH
export LD_LIBRARY_PATH=/home/wpsze/GNU_GCC/Library/gcc-v9.3.0/lib/${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}
export LD_LIBRARY_PATH=/home/wpsze/GNU_GCC/Library/gcc-v9.3.0/lib64:$LD_LIBRARY_PATH

export LD_LIBRARY_PATH=/home/wpsze/WRF/WRFv440/Library/lib:$LD_LIBRARY_PATH
export LDFLAGS=-L/home/wpsze/WRF/WRFv440/Library/lib
export CPPFLAGS=-I/home/wpsze/WRF/WRFv440/Library/include
export NETCDF=/home/wpsze/WRF/WRFv440/Library/
export HDF5=/home/wpsze/WRF/WRFv440/Library/
export PATH=/home/wpsze/WRF/WRFv440/Library/bin:$PATH

gcc --version
gfortran --version
mpirun --version
which mpif90 & mpif90 --version

time mpif90 --version
```

- wrf_da_install.sh

```sh
#!/bin/bash
############################## Directory Listing ############################
export HOME=`pwd`
export download_DIR=$HOME/wrf_install/
export libs_DIR=$HOME/Library/

echo $HOME
echo $download_DIR
echo $libs_DIR

mkdir -p wrf_install
mkdir -p Library

############################## WRF ############################
source /home/wpsze/WRF/WRFv440/wrf_env.sh
##################################################################
# Prior to version 4.0, WRFPlus code had been developed and maintained 
# in a separated branch from WRF. From V4.0, it is now fully integrated 
# into the same WRF branch. TLM/ADJ code is located in the 'wrftladj' directory.

# Now WRF, WRFDA, and WRFPLUS are stored in the same package which you can get from GitHub. 
# After downloading WRF package, decompress it three times, 
# rename it to WRF, WRFDA, and WRFPLUS.

cd $download_DIR

rm -rf WRFVDA4.5

cp -r WRFV4.4-raw WRFDAV4.4

cd WRFDAV4.4/

./clean -a

./configure wrfda

## rewrite: The character class \s will match the whitespace characters <tab> and <space>.
## and don't include $(DM_FC) 
# ** Not for all version **
sed -i "s/\s\stime\s//g" configure.wrf

./compile all_wrfvar 2>&1 | tee compile_wrfda.out

# After successful compilation, you can find the EXE file of WRFDA in the var/build/ directory
# ./var/build/da_wrfvar.exe
```

End of compilation,

```sh
......
if [ "-DBUFR" = "-DBUFR" ] ; then \
		gfortran -o obsproc.exe \
		module_date.o module_namelist.o module_mm5.o module_map.o module_map_utils.o module_intp.o module_type.o module_func.o module_inside.o module_obs_merge.o module_per_type.o module_duplicate.o module_sort.o module_write.o module_complete.o module_recoverp.o module_diagnostics.o module_recoverh.o module_icao.o module_qc.o module_err_afwa.o module_err_ncep.o module_thin_ob.o \
		module_decoded.o module_stntbl.o \
		error_handler.o fm_decoder.o sort_platform.o qc_reduction.o check_obs.o setup.o \
		obsproc.o  -O2 -ftree-vectorize -funroll-loops -w -ffree-form -ffree-line-length-none -fconvert=big-endian -frecord-marker=4      ../../external/bufr/libbufr.a; \
		else \
		gfortran -o obsproc.exe \
		module_date.o module_namelist.o module_mm5.o module_map.o module_map_utils.o module_intp.o module_type.o module_func.o module_inside.o module_obs_merge.o module_per_type.o module_duplicate.o module_sort.o module_write.o module_complete.o module_recoverp.o module_diagnostics.o module_recoverh.o module_icao.o module_qc.o module_err_afwa.o module_err_ncep.o module_thin_ob.o \
		module_decoded.o module_stntbl.o \
		error_handler.o fm_decoder.o sort_platform.o qc_reduction.o check_obs.o setup.o \
		obsproc.o  -O2 -ftree-vectorize -funroll-loops -w -ffree-form -ffree-line-length-none -fconvert=big-endian -frecord-marker=4      ; \
		fi
make[2]: Leaving directory '/home/wpsze/WRF/WRFv440/wrf_install/WRFDAV4.4/var/obsproc/src'
( /bin/rm -f obsproc.exe ;   ln -s src/obsproc.exe . )
make[1]: Leaving directory '/home/wpsze/WRF/WRFv440/wrf_install/WRFDAV4.4/var/obsproc'
build started:   Fri Aug 16 15:09:14 HKT 2024
build completed: Fri Aug 16 15:13:51 HKT 2024
```

## Successful

Checking, completely successful there should be 44 exe files.

```sh
ls -l var/build/*exe var/obsproc/src/obsproc.exe
```

```sh
da_advance_time.exe               gen_be_diags_read.exe
da_bias_airmass.exe               gen_be_ensmean.exe
da_bias_scan.exe                  gen_be_ensrf.exe
da_bias_sele.exe                  gen_be_ep1.exe
da_bias_verif.exe                 gen_be_ep2.exe
da_rad_diags.exe                  gen_be_etkf.exe
da_tune_obs_desroziers.exe        gen_be_hist.exe
da_tune_obs_hollingsworth1.exe    gen_be_stage0_gsi.exe
da_tune_obs_hollingsworth2.exe    gen_be_stage0_wrf.exe
da_update_bc_ad.exe               gen_be_stage1_1dvar.exe
da_update_bc.exe                  gen_be_stage1.exe
da_verif_grid.exe                 gen_be_stage1_gsi.exe
da_verif_obs.exe                  gen_be_stage2_1dvar.exe
da_wrfvar.exe                     gen_be_stage2a.exe
gen_be_addmean.exe                gen_be_stage2.exe
gen_be_cov2d3d_contrib.exe        gen_be_stage2_gsi.exe
gen_be_cov2d.exe                  gen_be_stage3.exe
gen_be_cov3d2d_contrib.exe        gen_be_stage4_global.exe
gen_be_cov3d3d_bin3d_contrib.exe  gen_be_stage4_regional.exe
gen_be_cov3d3d_contrib.exe        gen_be_vertloc.exe
gen_be_cov3d.exe                  gen_mbe_stage2.exe
gen_be_diags.exe
```

and

```sh
/var/obsproc/src/obsproc.exe
```

## Error

```sh
make[3]: time: No such file or directory make[3]: [makefile:42: module_io_int_idx.o] Error 127 (ignored)
```

Solution: [make -- time No such file or directory](https://waipangsze.github.io/2024/05/29/WRF_install_time_error/)

# testcase

- [wrfda 4.5 with wrf 3.9 testcase](https://forum.mmm.ucar.edu/threads/wrfda-4-5-with-wrf-3-9-testcase.13678/)
- [ERROR: The input file appears to be from a pre-v4 version of WRF initialization routines](https://forum.mmm.ucar.edu/threads/error-the-input-file-appears-to-be-from-a-pre-v4-version-of-wrf-initialization-routines.5529/)
- <https://www2.mmm.ucar.edu/wrf/users/wrfda/download/>

>but in this link i don't have a 4.0 testdata and the last testdata that I have is for 3.9 So I've downloaded 3.9 testdata...
> As expected, it complained about old 3.9 data, so I had to include
>> force_use_old_data=True

# Online Tutorial

- [the WRFDA Online Tutorial!](https://www2.mmm.ucar.edu/wrf/users/wrfda/OnlineTutorial/index.html)
- [3DVAR: The basics](https://www2.mmm.ucar.edu/wrf/users/wrfda/OnlineTutorial/3dvar/index.html)

# References

- [2018沈阳海洋工程项目-WRFDA-混合数据同化技术/快速更新同化预报系统](https://2018syocean.readthedocs.io/zh-cn/feature-delivery/hybrid/hybrid.html)
- 