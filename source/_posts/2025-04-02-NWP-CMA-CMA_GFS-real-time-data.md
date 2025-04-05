---
layout: post
title: NWP | CMA | CMA_GFS real time data
categories: NWP
tags: [NWP, WRF, MPAS, CMA, CMA_GFS]
author: wpsze
date: 2025-04-02 16:15:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/TtrCqKa.png
banner_img: https://i.imgur.com/TtrCqKa.png
---

**中國氣象局（China Meteorological Administration，縮寫：CMA）**，是中華人民共和國國務院負責氣象行政管理與科學研究的副部級直屬事業單位。

**CMA-GFS (原名为GRAPES-GFS)** 作为我国自主研发的新一代全球数值预报模式，从初始场、变分同化、模式框架等核心技术均实现了自主化。CMA-GFS全球模式从2001年开始组织数值预报团队启动研发，2016年实现业务化并面向全国下发产品。该模式每天运行4次，分别是00、06、12、18 UTC，时间分辨率为3小时，空间分辨率为0.25˚ × 0.25˚，预报时长为10天(240小时)，提供了全球基础要素场和诊断要素场，基础要素场主要包括各层次的高度场、温度场、东西风场、湿度场、比湿场、露点场及海平面气压场、10 m东西风场、2 m温度场、2 m比湿场、2 m露点场、降水量等，诊断要素场主要包括10 m阵风、能量指数、最高最低温度、云量及各层次的涡度散度、垂直速度及相当位温等。

- [CMA-MESO 3km的模式预报场能作为WRF的初始场吗？ | 2023-8-11 ](http://bbs.06climate.com/forum.php?mod=viewthread&tid=108570&extra=page%3D1&page=1)
  - 等压面数据用CMA MESO 预报场，地面数据用CLDAS网格产品可以吗WPS里的Vtable没有现成的选项，有考虑自己写Vtable文件
  - 我知道**GRAPES-GFS全球版本可以**，中尺度版本可能与这个不同。
  - 这是全球版本数据：<http://data.wis.cma.cn/DCPC_WMC_BJ/open/nwp/>
  - **CMA-GFS成功了，但是分辨率不够**，我又用了CALMET降尺度，**用 CMA-GFS的话 改Vtable文件 就可以**，MESO数据缺很多土壤数据
  - 不好意思，我的 Vtable文件是老师给的，不能外传，**你可以用python读取grib文件信息对应修改一下，改Vtable.ECWMF就可以**
- Github
  - [Adding support for CMA-GFS in WPS |  Oct 10, 2023](https://github.com/wrf-model/WPS/pull/236)
  - The CMA-GFS grib2 datasets is now able to be ingested by the ungrib program. The GRAPES_GFS(here in after referred to as CMA_GFS), the new generation of Global/Regional Assimilation and Prediction Enhanced System which is independently developped by China Meteorological Administration (CMA), has been put into formal operation. So we are committed to solving the problem of WPS correctly handling CMA_GFS forecast data.
  - one `Vtable files(Vtable.CMA_GFS)` has been created with the fields required by the WRF model. Modifications to the ungrib source code: **Adding support for code 738** in `rd_grid2.F` because model levels of CMA_GFS are on generalized vertical height coordinates.
  - The **CMA_GFS real time data is here**: <http://data.wis.cma.cn/DCPC_WMC_BJ/open/nwp/gmf_gra/>
  - **I've tested the program with an Intel compiler and it passed without errors. CMA_GFS forecast data were also used to test, the results are normal.**
  - [*Dec 22, 2024*] These changes permit ungribbing of the CMA model output. **Tests on derecho were successful**. **Note that the global CMA output files are very large and time-consuming to download**.
- [WRF基础 | CMA-GFS运行WRF | Jan 2025](https://mp.weixin.qq.com/s?__biz=MzI2NTk2MzU3NA==&mid=2247485543&idx=1&sn=e4155bbc8ee16350c57dbf733e3d2386&chksm=ea94193edde39028ec6ec2ecfd5095521c6d1e0a627357bcc8a67ca093c2e462d01ff4a1660e&scene=178&cur_album_id=2858702555684864004#rd)

{% fold info @rd_grib2.F %}
```rd_grib2.F
line 738:  &               (gfld%ipdtmpl(1) .eq. g2code(2,j)) .and.  !! Added by Dr.Haiqing SONG, 20181201
```
{% endfold %}

{% fold info @Vtable.CMA_GFS %}
```Vtable.CMA_GFS
GRIB1| Level| From |  To  | metgrid  | metgrid | metgrid                                 |GRIB2|GRIB2|GRIB2|GRIB2|
Param| Type |Level1|Level2| Name     | Units   | Description                             |Discp|Catgy|Param|Level|
-----+------+------+------+----------+---------+-----------------------------------------+-----------------------+
  11 | 100  |   *  |      | TT       | K       | Temperature                             |  0  |  0  |  0  | 100 |
  33 | 100  |   *  |      | UU       | m s-1   | U                                       |  0  |  2  |  2  | 100 |
  34 | 100  |   *  |      | VV       | m s-1   | V                                       |  0  |  2  |  3  | 100 |
  52 | 100  |   *  |      | RH       | %       | Relative Humidity                       |  0  |  1  |  1  | 100 |
   7 | 100  |   *  |      | HGT      | m       | Height                                  |  0  |  3  |  5  | 100 |
  11 | 105  |   2  |      | TT       | K       | Temperature       at 2 m                |  0  |  0  |  0  | 103 |
  52 | 105  |   2  |      | RH       | %       | Relative Humidity at 2 m                |  0  |  1  |  1  | 103 |
  33 | 105  |  10  |      | UU       | m s-1   | U                 at 10 m               |  0  |  2  |  2  | 103 |
  34 | 105  |  10  |      | VV       | m s-1   | V                 at 10 m               |  0  |  2  |  3  | 103 |
   1 |   1  |   0  |      | PSFC     | Pa      | Surface Pressure                        |  0  |  3  |  0  |   1 |
   2 | 102  |   0  |      | PMSL     | Pa      | Sea-level Pressure                      |  0  |  3  |  1  | 101 |
 144 | 112  |   0  |  10  | SM000010 | fraction| Soil Moist 0-10 cm below grn layer (Up) |  0  |  1  |  0  | 106 |
 144 | 112  |  10  |  40  | SM010040 | fraction| Soil Moist 10-40 cm below grn layer     |  0  |  1  |  0  | 106 |
 144 | 112  |  40  | 100  | SM040100 | fraction| Soil Moist 40-100 cm below grn layer    |  0  |  1  |  0  | 106 |
 144 | 112  | 100  | 200  | SM100200 | fraction| Soil Moist 100-200 cm below gr layer    |  0  |  1  |  0  | 106 |
  85 | 112  |   0  |  10  | ST000010 | K       | T 0-10 cm below ground layer (Upper)    |  0  |  0  |  0  | 106 |
  85 | 112  |  10  |  40  | ST010040 | K       | T 10-40 cm below ground layer (Upper)   |  0  |  0  |  0  | 106 |
  85 | 112  |  40  | 100  | ST040100 | K       | T 40-100 cm below ground layer (Upper)  |  0  |  0  |  0  | 106 |
  85 | 112  | 100  | 200  | ST100200 | K       | T 100-200 cm below ground layer (Bottom)|  0  |  0  |  0  | 106 |
  11 |   1  |   0  |      | SKINTEMP | K       | Skin temperature                        |  0  |  0  |  0  |   1 |
   7 |   1  |   0  |      | SOILHGT  | m       | Terrain field of source analysis        |  0  |  3  |  5  |   1 |
  81 |   1  |   0  |      | LANDSEA  | proprtn | Land/Sea flag (1=land, 0 or 2=sea)      |  2  |  0  |  0  |   1 |
  65 |   1  |   0  |      | SNOW     | kg m-2  | Water equivalent snow depth             |  0  |  1  | 13  |   1 |
     |   1  |   0  |      | SNOWH    | m       | Physical Snow Depth                     |  0  |  1  |     |   1 |
  33 |   6  |   0  |      | UMAXW    | m s-1   | U                 at max wind           |  0  |  2  |  2  |   6 |
  34 |   6  |   0  |      | VMAXW    | m s-1   | V                 at max wind           |  0  |  2  |  3  |   6 |
   2 |   6  |   0  |      | PMAXW    | Pa      | Pressure of max wind level              |  0  |  3  |  0  |   6 |
     |   6  |   0  |      | PMAXWNN  | Pa      | PMAXW, used for nearest neighbor interp |  0  |  3  |  0  |   6 |
   2 |   6  |   0  |      | TMAXW    | K       | Temperature at max wind level           |  0  |  0  |  0  |   6 |
   7 |   6  |   0  |      | HGTMAXW  | m       | Height of max wind level                |  0  |  3  |  5  |   6 |
  33 |   7  |   0  |      | UTROP    | m s-1   | U                 at tropopause         |  0  |  2  |  2  |   7 |
  34 |   7  |   0  |      | VTROP    | m s-1   | V                 at tropopause         |  0  |  2  |  3  |   7 |
   2 |   7  |   0  |      | PTROP    | Pa      | Pressure of tropopause                  |  0  |  3  |  0  |   7 |
     |   7  |   0  |      | PTROPNN  | Pa      | PTROP, used for nearest neighbor interp |  0  |  3  |  0  |   7 |
   2 |   7  |   0  |      | TTROP    | K       | Temperature at tropopause               |  0  |  0  |  0  |   7 |
   7 |   7  |   0  |      | HGTTROP  | m       | Height of tropopause                    |  0  |  3  |  5  |   7 |
-----+------+------+------+----------+---------+-----------------------------------------+-----------------------+
#  This Vtable was created for CMA/CMA_GFS model data from the CMA server.(http://cemc.cma.cn/intro.html?idx=1)
#  The GRAPES_GFS(here in after referred to as CMA_GFS), the new generation of Global/Regional Assimilation and Prediction Enhanced System
#  which is independently developped by China Meteorological Administration (CMA), has been put into formal operation.
#  The data was accesed from:http://data.wis.cma.cn/DCPC_WMC_BJ/open/nwp/gmf_gra/thh00/    (note hh at end)
#  Developed by Dr. Haiqing SONG from Inner Mongolia Meteorological Service of China Meteorological Administration(CMA).
#  E-mail:haiqingsong@emails.imau.edu.cn
#  wgrib2 -var filename.grib2
```
{% endfold %}

# CMA

数据介绍

CMA-GFS 是我国自主研发的新一代全球数值预报模式，该模式由同化分系统和全球模式分系统两部分组成，于2016年6月业务化运行，向全国实时分发产品。2018年7月四维变分同化实现业务运行。2020年CMA-GFS升级为3.0，模式水平分辨率为25km，模式层顶0.1hPa，垂直分层87层。2022年CMA-GFS升级为3.3版本。2023年CMA-GFS升级为4.0版本

![https://data.cma.cn/data/detail/dataCode/F.0003.0008.S001.html](https://i.imgur.com/KDTgahf.png){width=400}

## http://data.wis.cma.cn/DCPC_WMC_BJ/open/nwp/gmf_gra/

- Index of /DCPC_WMC_BJ/open/nwp/gmf_gra/

![](https://i.imgur.com/H9MM6QW.png){width=500}

# ungrib

- re-compile **WPS** (**WRFv450-CMA-GFS**)

```
-rw-rw-r-- 1 wpsze wpsze 3.2G Apr  2 16:56 CMA:2025-01-12_00
lrwxrwxrwx 1 wpsze wpsze   62 Apr  2 16:50 GRIBFILE.AAA -> Z_NAFP_C_BABJ_20250112000000_P_NWPC-GRAPES-GFS-GLB-00000.grib2
-rw-rw-r-- 1 wpsze wpsze  272 Apr  2 16:50 namelist.wps
-rwxrwxr-x 1 wpsze wpsze 1.7K Apr  2 16:51 run_ungrid.sh*
lrwxrwxrwx 1 wpsze wpsze   67 Apr  2 16:51 ungrib.exe -> /home/wpsze/WRF/WRFv450-CMA-GFS/wrf_install/WPS-4.5/ungrib/ungrib.exe*
-rw-rw-r-- 1 wpsze wpsze  30K Apr  2 16:57 ungrib.log
lrwxrwxrwx 1 wpsze wpsze   14 Apr  2 16:51 Vtable -> Vtable.CMA_GFS
lrwxrwxrwx 1 wpsze wpsze   87 Apr  2 16:49 Vtable.CMA_GFS -> /home/wpsze/WRF/WRFv450-CMA-GFS/wrf_install/WPS-4.5/ungrib/Variable_Tables/Vtable.CMA_GFS
lrwxrwxrwx 1 wpsze wpsze   81 Apr  2 16:49 Z_NAFP_C_BABJ_20250112000000_P_NWPC-GRAPES-GFS-GLB-00000.grib2 -> ../202501/20250112/Z_NAFP_C_BABJ_20250112000000_P_NWPC-GRAPES-GFS-GLB-00000.grib2
```

# WRF

- geogrid
  - `LANDMASK` is added here.
    ```console
    float LANDMASK(Time, south_north, west_east) ;
		LANDMASK:FieldType = 104 ;
		LANDMASK:MemoryOrder = "XY " ;
		LANDMASK:units = "none" ;
		LANDMASK:description = "Landmask : 1=land, 0=water" ;
		LANDMASK:stagger = "M" ;
		LANDMASK:sr_x = 1 ;
		LANDMASK:sr_y = 1 ;
    ```
- ungrib.exe: done
- metgrib.exe: done
- real.exe: done
- wrf.exe: Segmentation Fault

# MPAS

## MPASv7.3

### init_atmosphere_model

#### Error 1

```log
ERROR: ********************************************************************************
ERROR: LANDSEA field not found in meteorological data file CMA:2025-01-12_00
CRITICAL ERROR: ********************************************************************************
```

- seems CMA_GFS doesn't contain `LANDSEA`

But, in **static.nc**, landmask is included. So, why does init_atmosphere_model have to check **LANDSEA**?

```console
	int landmask(nCells) ;
		landmask:units = "unitless" ;
		landmask:long_name = "land-ocean mask (1=land ; 0=ocean)" ;
```

- [Error while running ungrib.exe with IFS global data | Oct 28, 2023](https://forum.mmm.ucar.edu/threads/error-while-running-ungrib-exe-with-ifs-global-data.14439/#post-38395)
  - I tried removing all the fields that do not work with the new namelist record. **However, MPAS needs the LANDSEA mask to work but I don't know how to add it in the Intermediate Files**.
  - Landsea mask values in ECMWF data lie between 0 and 1, which is different to WRF static data that have values of either 0 (grid box is fully covered by water) or 1 (grid box is fully covered with land). Please see the website below that describes landsea mask in ECMWRF: <https://confluence.ecmwf.int/display/FUG/Section+2.1.3.1+Land-Sea+Mask>
  - **I am suspicious that Landsea mask values in IFS global data follow the rule of ECMWF data, i.e., its values could be between 0 and 1. This might give rise to troubles when we run MPAS initialization**.
- [generating initial files in 2 separate stages | Jan 2025](https://forum.mmm.ucar.edu/threads/generating-initial-files-in-2-separate-stages.20522/)
  - for instance, I found that landsea mask is a necessary parameter for processing pressure level data.

##### Take IFS's land sea mask

- `./extract_landseamask.sh`

```sh
#!/bin/bash

cdo selname,lsm 20250401000000-0h-oper-fc.grib2 lsm_nan.nc

cdo setmissval,-999 lsm_nan.nc lsm.nc

cdo -f grb copy lsm.nc lsm.grib

cdo showname lsm.grib

rm lsm_nan.nc lsm.nc 
```

- ungrib step
  - `ln -s lsm.grib GRIBFILE.AAB` # the CMA_GFS.grib2 is GRIBFILE.AAA
    - Error: `gb_info: can only decode GRIB edition 2.`
      - check: `$ grib_ls lsm.grib`
          ```console
          edition      centre       typeOfLevel  level        dataDate     stepRange    shortName    packingType  gridType     
          1         ecmf         surface      0            20250401     0            unknown      grid_simple  regular_ll  
          ```
      - check:
            ```sh
            $ wgrib2 -var lsm.grib 
            1:0:LAND
            ``` 
    - Solution: `cdo -f grb2 copy lsm.nc lsm.grib` on `extract_landseamask.sh`
      - check: `$ grib_ls lsm.grib`
          ```console
          edition      centre       date         dataType     gridType     stepRange    typeOfLevel  level        shortName    packingType  
          2            ecmf         20250401     fc           regular_ll   0            surface      0            lsm          grid_simple 
          ```
  - But `init_atmosphere_model` still exists: `ERROR: LANDSEA field not found in meteorological data file CMA:2025-01-12_00`
  - [not yet] Done and check: ` $ ncl plotfmt.ncl 'filename="CMA:2025-01-12_00"'`

#### Error 2

```log
CRITICAL ERROR: Error in interpolation of st_fg to MPAS grid: num_st = 18
```

- [Error creating initial conditions with NCEP FNL ds083.2 | May 8, 2024](https://forum.mmm.ucar.edu/threads/error-creating-initial-conditions-with-ncep-fnl-ds083-2.17113/)
  - I'm trying to create the initial conditions file for MPAS (v8.1) using data obtained from the NCEP FNL ds083.2
  - Just had to change `config_nfgsoillevels` to **2** and `config_extrap_airtemp` to **linear** in `namelist.init_atmosphere`. Once those two modifications were made `init_atmosphere_model` and `atmosphere_model` ran without issues.
  - Note that **FNL data only contains 2-level of soil information**, which is why we need to set **config_nfgsoillevels=2**. **The top of the FNL data is 10 hpa, and therefore extrapolation is needed if MPAS top level is above 10 hpa**.
  - For the option of `config_extrap_airtemp`, would you please let me know what is the model top of your MPAS run? Thanks.
- [Initial condition CRITICAL ERROR: Error in interpolation of st_fg to MPAS | Feb 16, 2024](https://forum.mmm.ucar.edu/threads/initial-condition-critical-error-error-in-interpolation-of-st_fg-to-mpas.15807/)
- ['Error in interpolation of st_fg to MPAS grid' with variable-resolution mesh simulation | Jul 23, 2020](https://forum.mmm.ucar.edu/threads/error-in-interpolation-of-st_fg-to-mpas-grid-with-variable-resolution-mesh-simulation.9387/)
  - **The error message generally indicates that there are grid cells that have a soil temperature value less than or equal to zero.**
  - I solved the problem by downloading the "Vtable from the RDR page/WRF" and using WPS version 4.0 or higher.
- [CRITICAL ERROR: Error in interpolation of st_fg to MPAS grid | Start dateMay 3, 2019](https://forum.mmm.ucar.edu/threads/critical-error-error-in-interpolation-of-st_fg-to-mpas-grid.5382/)
  - This error message is an indication that there were one or more points in the MPAS mesh that received a zero or negative value for soil temperature.
  - Can you also verify that there are soil moisture and soil temperature fields in your WPS intermediate file, and that the number of soil levels in your intermediate files matches the value of the config_nfgsoillevels in your namelist.init_atmosphere file?
- [CRITICAL ERROR: Error in interpolation of st_fg to MPAS grid: num_st = 18642 | Apr 12, 2023](https://forum.mmm.ucar.edu/threads/critical-error-error-in-interpolation-of-st_fg-to-mpas-grid-num_st-18642.12857/)
  - **MPAS can only process soil data at specific levels with specific field names. If your input soil data are on levels or with names unrecognized by MPAS, then it won't work**.
  - Please take a look at the code **"mpas_init_atm_cases.F"**, and the lines 3908 - 4118 process soil temperature data. This might give you some idea how MPAS works.
- [Trying to Use RAP as IC/BC | Start dateJun 26, 2019](https://forum.mmm.ucar.edu/threads/trying-to-use-rap-as-ic-bc.5549/)
  - The message "Error in interpolation of st_fg to MPAS grid: num_st = XXXX" is **generally indicative of an issue with the soil temperature**. Would it be possible to use some of the utilities that come with the WRF Pre-processing System (WPS) to check that there are valid soil temperature data in your intermediate file? Specifically, you could use the "`rd_intermediate`" utility to check that there are fields named, e.g., ST000010, and you could use the "`int2nc`" utility to convert the intermediate file to netCDF format, where it would be easier to view the soil fields with, e.g., `ncview`.

# cronjob

{% fold info @wpsze_download_CMA_GFS-cronjob-00z.sh %}
```sh
#!/bin/sh
#################################################################
# http://data.wis.cma.cn/DCPC_WMC_BJ/open/nwp/gmf_gra/t0000/f0_f240_6h/Z_NAFP_C_BABJ_20250401000000_P_NWPC-GRAPES-GFS-GLB-00000.grib2
#################################################################
source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate venv

BASE_DIR=$(pwd)
echo "Base Dir = ${BASE_DIR}"

for ifsday in $(seq 0 1 1); do # day-shift = 0, 1

    YYYYMMDDHH=$(date -d "today - ${ifsday} day" +%Y%m%d00)

    yyyy=${YYYYMMDDHH:0:4}
    mm=${YYYYMMDDHH:4:2}
    dd=${YYYYMMDDHH:6:2}
    hh=${YYYYMMDDHH:8:2}

    yyyymm=${yyyy}${mm}
    yyyymmdd=${yyyy}${mm}${dd}

    tmp_dir="./${yyyy}${mm}/${yyyy}${mm}${dd}"
    mkdir -p ${tmp_dir}
    cd ${tmp_dir}
    echo "Current Dir: $(pwd)"

    limit_cpu_core=15
    count_cpu_core=1

    #============== 00z ===================================
    tmp_cma_gfs_fname="Z_NAFP_C_BABJ_${YYYYMMDDHH}0000_P_NWPC-GRAPES-GFS-GLB-00000.grib2"

    echo "Now, ${tmp_cma_gfs_fname} ... "

    if [[ ! -f "${tmp_cma_gfs_fname}" ]]; then	
	    echo "Downloading ... ${tmp_cma_gfs_fname}"
	    #wget --no-check-certificate http://data.wis.cma.cn/DCPC_WMC_BJ/open/nwp/gmf_gra/t0000/f0_f240_6h/Z_NAFP_C_BABJ_20250401000000_P_NWPC-GRAPES-GFS-GLB-00000.grib2
	    wget --no-check-certificate http://data.wis.cma.cn/DCPC_WMC_BJ/open/nwp/gmf_gra/t0000/f0_f240_6h/${tmp_cma_gfs_fname}
    else
        echo "${tmp_cma_gfs_fname} exists ... "
    fi

    res=$(( ${count_cpu_core} % ${limit_cpu_core} ))
    if [[ ${res} == 0 ]]; then
	    echo "please wait ${tmp_cma_gfs_fname}, ${count_cpu_core}, ${res}."
	    wait
    fi
    
    cd ${BASE_DIR}
done    
```
{% endfold %}

- **HKT = UTC + 8**
- How to set the crontab: `$ crontab -e`


```console
$ crontab -l
00 16 * * * cd /home/wpsze/mpas/CMA_GFS; sh wpsze_download_CMA_GFS-cronjob-00z.sh
00 16 * * * cd /home/wpsze/mpas/CMA_GFS; sh wpsze_download_CMA_GFS-cronjob-12z.sh
```