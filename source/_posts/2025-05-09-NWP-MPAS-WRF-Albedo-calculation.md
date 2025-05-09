---
layout: post
title: NWP | MPAS | WRF| Albedo calculation
categories: [NWP]
tags: [NWP, MPAS, WRF, Meteorology, Albedo, static.nc, geogrid.exe, LANDUSE]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-05-09 10:10:00
index_img: https://i.imgur.com/pP0QkKo.png
banner_img: https://i.imgur.com/pP0QkKo.png
---

# Albedo

- [Description of albedo calculation | Aug 30, 2018](https://forum.mmm.ucar.edu/threads/description-of-albedo-calculation.96/)

{% note primary %}
- If you map data to the 24-category USGS data, then albedo will be given at the model start time, based on the LANDUSE.TBL. Since the initial value is dependent on the landuse type, 
- however, the **Noah LSM** does not use the albedo (or any other land properties) from the LANDUSE.TBL. **It uses values from the VEGPARM.TBL, and albedo is computed based on vegetation fraction, and bounded by the max and min values given in the VEGPARM.TBL.**
{% endnote %}

- [Use of VEGPARM.TBL and LANDUSE.TBL | Aug 30, 2018](https://forum.mmm.ucar.edu/threads/use-of-vegparm-tbl-and-landuse-tbl.73/)

{% note primary %}
- WRF
- The LANDUSE.TBL was used by all land surface model (LSM) options prior to Version 3.1. 
- The **VEGPARM.TBL is used by the Noah and RUC LSMs**. 
- The **PX LSM uses data defined in module_sf_ssib.F**. 
- **The model reads fields from LANDUSE.TBL first, but then may be overwritten by values in VEGPARM.TBL if there is an overlap**.
{% endnote %}

## LANDUSE.TBL 

- <https://github.com/wrf-model/WRF/blob/master/run/LANDUSE.TBL>
- ALBD: Surface albedo
- SLMO: Surface moisture availability
- SFEM: Surface emissivity
- SFZO: Surface roughness
- THERIN: Surface thermal inertia
- SFHC: Soil heat capacity

![](https://i.imgur.com/F8UBLRf.png){width=400}

## VEGPARM.TBL

- <https://github.com/yyr/wrf/blob/master/run/VEGPARM.TBL>
- [WRF/phys/module_sf_noahdrv.F](https://github.com/wrf-model/WRF/blob/master/phys/module_sf_noahdrv.F)
```fortran
!-----SPECIFY VEGETATION RELATED CHARACTERISTICS :
!             ALBBCK: SFC albedo (in percentage)
!                 Z0: Roughness length (m)
!             SHDFAC: Green vegetation fraction (in percentage)
!  Note: The ALBEDO, Z0, and SHDFAC values read from the following table
!          ALBEDO, amd Z0 are specified in LAND-USE TABLE; and SHDFAC is
!          the monthly green vegetation data
!             CMXTBL: MAX CNPY Capacity (m)
!             NROTBL: Rooting depth (layer)
!              RSMIN: Mimimum stomatal resistance (s m-1)
!              RSMAX: Max. stomatal resistance (s m-1)
!                RGL: Parameters used in radiation stress function
!                 HS: Parameter used in vapor pressure deficit functio
!               TOPT: Optimum transpiration air temperature. (K)
!             CMCMAX: Maximum canopy water capacity
!             CFACTR: Parameter used in the canopy inteception calculati
!               SNUP: Threshold snow depth (in water equivalent m) that
!                     implies 100% snow cover
!                LAI: Leaf area index (dimensionless)
!             MAXALB: Upper bound on maximum albedo over deep snow
!
!-----READ IN VEGETAION PROPERTIES FROM VEGPARM.TBL
```

### Description of MIN and MAX albedo

- [Description of MIN and MAX albedo | Aug 30, 2018](https://forum.mmm.ucar.edu/threads/description-of-min-and-max-albedo.99/)

{% note primary %}
If you take a look in the **WRF/run/VEGPARM.TBL** file, you will see **ALBEDOMIN** and **ALBEDOMAX**, corresponding to each landuse type. 

- The **Noah LSM scheme** reads this file, then calculates albedo based on the vegetation cover fraction, and the max/min albedo. For more details, you can see the file *WRF/phys/module_sf_noahlsm.F*.
{% endnote %}

### usemonalb (monthly albedo)

- <https://github.com/wrf-model/WRF/blob/eeacb821626feb34c62c3c1e169594d886d13adb/run/README.namelist#L998>
- **(recommended for sst_update=1)**

```namelist
 sst_update                          = 0        ! time-varying sea-surface temp (0=no, 1=yes). If selected real 
                                                  puts SST, XICE, ALBEDO and VEGFRA in wrflowinp_d01 file, and wrf updates 
                                                  these from it at same interval as boundary file. Also requires
                                                  namelists in &time_control: auxinput4_interval, auxinput4_end_h,
                                                  auxinput4_inname = "wrflowinp_d<domain>", and io_form_auxinput4
 usemonalb                           = .false.  ! use monthly albedo map instead of table value
                                                  (recommended for sst_update=1)
 rdmaxalb                            = .true.   ! use snow albedo from geogrid; false means using values from table
 rdlai2d                             = .false.  ! use LAI from input; false means using values from table
                                                  if sst_update=1, LAI will also be in wrflowinp file
```

# More,

- [Modify landuse and GREENFRAC | Aug 25, 2020](https://forum.mmm.ucar.edu/threads/modify-landuse-and-greenfrac.9479/#post-18194)
  - As **GREENFRAC** is a 12-monthly field from geogrid, during the real.exe process, it will be interpolated to the model start time, and stored in the array "vegfra." It will also be included in wrflowinp_d* since you are using **sst_update**. As for modifying other variables, I guess it depends whether you think those particular variables will be affected in the area of your modifications.
  - **ALBEDO12M** isn't even actually used during real/wrf unless you set `usemonalb= .true.` in the namelist (it will be interpolated to **"albbck" during real**).
- [monthly albedo | Feb 19, 2024](https://forum.mmm.ucar.edu/threads/monthly-albedo.15849/)
  - You are right that **albedo over the ocean is not updated even if sst_update =1**.
- [WRF中修改反照率](https://github.com/HanXiang0721/Note-Numerical-Methods/blob/master/WRF%E4%B8%AD%E4%BF%AE%E6%94%B9%E5%8F%8D%E7%85%A7%E7%8E%87.md)
  - wrf本身的 albedo是根据 Vtable定的，所以是不停变化的。首先要将albedo改为月的 albedo，方法是在 namelist.input 中的 &physics 中加入 usemonalb = .true.命令，然后就是利用ncl修改即可。在模式中单纯修改反照率，如果面积过大，就会造成地面平衡破坏太严重，模式易于崩溃。
- [如何在WRF中使用2020年（最新）土地利用类型数据集？](https://cloud.tencent.com/developer/article/2297389)
  - WRF中土地利用类型最高分辨率是30s，且主要分为MODIS和USGS两种，**其中MODIS数据是从2000年（有的也说是2001年）的MODIS卫星遥感数据**，按照IGBP20分类标准得到的，**总共有21类（含第21类—Lake）**,**USGS数据则是1992~1993年的，总共分为24类**，具体类型可以参考userguide，这些数据时间都比较久远了，如果进行最新模拟的话相差20年了，所以进行了替换。

# Literature Review

- [WRF模式東亞地區土地利用資料庫之更新與個案研究 | 交通部中央氣象署](https://photino.cwa.gov.tw/rdcweb/lib/cd/cd01conf/dissertation/2009-1/029.pdf)
  - **USGS-24 vs MODIS-20**
  - ![](https://i.imgur.com/IjGhY1M.png){width=400}
- [AAS：卫星产品改进WRF模式反照率方案极大提升了青藏高原降雪模拟能力 | 2022-04-21](http://www.itpcas.cas.cn/new_kycg/new_kyjz/202204/t20220421_6434280.html)
  - 地表反照率表征地面对太阳辐射的吸收和反射能力，是地表能量平衡的关键参数。青藏高原冬春季降雪频繁，地表反照率在降雪和随后的积雪演变过程中剧烈变化，对地表能量平衡和水循环有很大影响。
  - 团队利用卫星遥感反演的反照率数据，综合考虑雪深和雪龄对反照率的影响，对Noah陆面过程模式中地表反照率方案进行青藏高原本地优化
  - **模式采用改进的反照率方案显著降低青藏高原反照率的高估，显著提高 模拟反照率 与 卫星反演反照率 之间的相关性**，改善了气温模拟的冷偏差，更准确模拟出强降雪的空间分布特征，并突破了Noah默认的反照率方案影响因子的局限性，为进一步改进反照率参数化方案提供参考，也为卫星遥感数据产品在数值模拟中的应用提供新视角。
- [陆云波, 王伦澈, 牛自耕, 王绍强, 王力哲. 2000—2017年中国区域地表反照率变化及其影响因子[J]. 地理研究, 2022, 41(2): 562-579 https://doi.org/10.11821/dlyj020210005](https://www.dlyj.ac.cn/CN/10.11821/dlyj020210005)
  - ![](https://i.imgur.com/5LzNyNZ.png){width=400}
- [高婷, 沈润平, 李磊, 等. 2021. 基于MODIS数据地表反照率时空变化特征及影响因子研究[J]. 气候与环境研究, 26(6): 648−662. DOI:  10.3878/j.issn.1006-9585.2021.20160](http://www.iapjournals.ac.cn/qhhj//article/qhyhjyj/2021/6/648)
  - 淮河流域地表反照率与归一化植被指数（Normalized Difference Vegetation Index，NDVI）、地表温度、气温和降水在大部分区域呈正相关

# WRFDA, CRTM

WRFDA observation operator H uses a **radiative transfer model (e.g., CRTM or RTTOV)** to **compute brightness temperatures** from model profiles of temperature, humidity, and surface properties like surface emissivity.
 
- <https://ntrs.nasa.gov/api/citations/20230008420/downloads/CRTM%20BAMS%20Paper_Mar12_2023_Rev1a.docx>
 
**The CRTM surface model includes surface emissivity/reflectivity for various surface types**, including snow, ice, land, and water/ocean.

# MPAS

## Noah LSM 

The **Noah LSM** does not use the albedo (or any other land properties) from the LANDUSE.TBL. **It uses values from the VEGPARM.TBL, and albedo is computed based on vegetation fraction, and bounded by the max and min values given in the VEGPARM.TBL.**

## for climatologically varying surface albedo

```xml
<nml_option name="config_sfc_albedo" type="logical" default_value="true" in_defaults="false"
        units="-"
        description="logical for configuration of surface albedo"
        possible_values=".true. for climatologically varying surface albedo; .false. for fixed input data"/>

<nml_option name="config_sfc_snowalbedo" type="logical" default_value="true" in_defaults="false"
        units="-"
        description="logical for configuration of maximum surface albedo for snow"
        possible_values=".true. for geographical distribution; .false. for fixed input data"/>
```

![](https://i.imgur.com/N1zPer9.png)
![For climatologically varying surface albedo](https://i.imgur.com/kGDTiGb.png)