---
layout: post
title: NWP | CMA | CMA_GFS real time data
categories: NWP
tags: [NWP, WRF, MPAS, CMA, CMA_GFS, Land-sea mask]
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

# grib2 file

- `./g2print.exe Z_NAFP_C_BABJ_20250112000000_P_NWPC-GRAPES-GFS-GLB-00000.grib2` 

{% fold info @log %}
```log
 ungrib - grib edition num           2
 reading from grib file = Z_NAFP_C_BABJ_20250112000000_P_NWPC-GRAPES-GFS-GLB-00000.grib2                                                          
      unknown model and orig center   
---------------------------------------------------------------------------------------
 rec Prod Cat Param  Lvl    Lvl      Lvl     Prod    Name            Time          Fcst
 num Disc     num    code   one      two     Templ                                 hour
---------------------------------------------------------------------------------------
   1   0    1  10       1       0       0       8     ACPCP    2025-01-12_00:00:00   00  PDT4.8  
   2   0    1   9       1       0       0       8     NCPCP    2025-01-12_00:00:00   00  PDT4.8  
   3   0    1   8       1       0       0       8     APCP     2025-01-12_00:00:00   00  PDT4.8  
   4   0    1  29       1       0       0       8     ASNOW    2025-01-12_00:00:00   00  PDT4.8  
   5   0    0   0       1       0       0       0     TMP      2025-01-12_00:00:00   00          
   6   0    5   5       1       0       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
   7   0    4   9       1       0       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
   8   2    0  24       1       0       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
   9   0    0  10       1       0       0       8     LHTFL    2025-01-12_00:00:00   00  PDT4.8  
  10   0    5   4       1       0       0       8     ULWRF    2025-01-12_00:00:00   00  PDT4.8  
  11   0    5   4       8********       0       8     ULWRF    2025-01-12_00:00:00   00  PDT4.8  
  12   0    5   8       1       0       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
  13   0    5 224       1       0       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
  14   0    5 224       8********       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
  15   0    4   8       1       0       0       8     USWRF    2025-01-12_00:00:00   00  PDT4.8  
  16   0    4   9       8********       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
  17   0    4   8       8********       0       8     USWRF    2025-01-12_00:00:00   00  PDT4.8  
  18   0    4  11       1       0       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
  19   0    4  53       1       0       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
  20   0    4  11       8********       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
  21   0    4  53       8********       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
  22   0    3 228       1       0       0       0     UNKNOWN  2025-01-12_00:00:00   00          
  23   0    3   5       1       0       0       0     HGT      2025-01-12_00:00:00   00          
  24   0    1   0     103       2       0       0     SPFH     2025-01-12_00:00:00   00          
  25   0    0   0     103       2       0       0     TMP      2025-01-12_00:00:00   00          
  26   0    2   2     103      10       0       0     UGRD     2025-01-12_00:00:00   00          
  27   0    2   3     103      10       0       0     VGRD     2025-01-12_00:00:00   00          
  28   0    6   1       1       0********       0     TCDC     2025-01-12_00:00:00   00          
  29   0    6   3       1       0********       0     LCDC     2025-01-12_00:00:00   00          
  30   0    6   4       1       0********       0     MCDC     2025-01-12_00:00:00   00          
  31   0    6   5       1       0********       0     HCDC     2025-01-12_00:00:00   00          
  32   0    1  64      10********       0       0     UNKNOWN  2025-01-12_00:00:00   00          
  33   0    1  69      10********       0       0     UNKNOWN  2025-01-12_00:00:00   00          
  34   0    1  70      10********       0       0     UNKNOWN  2025-01-12_00:00:00   00          
  35   0    3  18       1       0       0       0     HPBL     2025-01-12_00:00:00   00          
  36   0    2 227       1       0       0       0     UNKNOWN  2025-01-12_00:00:00   00          
  37   0    2 228       1       0       0       0     UNKNOWN  2025-01-12_00:00:00   00          
  38   0    1  11       1       0       0       0     SNOD     2025-01-12_00:00:00   00          
  39   0   19   1       1       0       0       0     ALBDO    2025-01-12_00:00:00   00          
  40   0    0   4     103       2       0       8     TMAX     2025-01-12_00:00:00   00  PDT4.8  
  41   0    0   5     103       2       0       8     TMIN     2025-01-12_00:00:00   00  PDT4.8  
  42   0    1 231     103       2       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
  43   0    1 232     103       2       0       8     UNKNOWN  2025-01-12_00:00:00   00  PDT4.8  
  44   0    3   0       1       0       0       0     PRES     2025-01-12_00:00:00   00          
  45   0    3   1     101       0       0       0     PRMSL    2025-01-12_00:00:00   00          
  46   0    2   2     103      30       0       0     UGRD     2025-01-12_00:00:00   00          
  47   0    2   2     103      50       0       0     UGRD     2025-01-12_00:00:00   00          
  48   0    2   2     103      70       0       0     UGRD     2025-01-12_00:00:00   00          
  49   0    2   2     103     100       0       0     UGRD     2025-01-12_00:00:00   00          
  50   0    2   2     103     120       0       0     UGRD     2025-01-12_00:00:00   00          
  51   0    2   2     103     140       0       0     UGRD     2025-01-12_00:00:00   00          
  52   0    2   2     103     160       0       0     UGRD     2025-01-12_00:00:00   00          
  53   0    2   2     103     180       0       0     UGRD     2025-01-12_00:00:00   00          
  54   0    2   2     103     200       0       0     UGRD     2025-01-12_00:00:00   00          
  55   0    2   3     103      30       0       0     VGRD     2025-01-12_00:00:00   00          
  56   0    2   3     103      50       0       0     VGRD     2025-01-12_00:00:00   00          
  57   0    2   3     103      70       0       0     VGRD     2025-01-12_00:00:00   00          
  58   0    2   3     103     100       0       0     VGRD     2025-01-12_00:00:00   00          
  59   0    2   3     103     120       0       0     VGRD     2025-01-12_00:00:00   00          
  60   0    2   3     103     140       0       0     VGRD     2025-01-12_00:00:00   00          
  61   0    2   3     103     160       0       0     VGRD     2025-01-12_00:00:00   00          
  62   0    2   3     103     180       0       0     VGRD     2025-01-12_00:00:00   00          
  63   0    2   3     103     200       0       0     VGRD     2025-01-12_00:00:00   00          
  64   0    3   5     100  100000       0       0     HGT      2025-01-12_00:00:00   00          
  65   0    3   5     100   97500       0       0     HGT      2025-01-12_00:00:00   00          
  66   0    3   5     100   95000       0       0     HGT      2025-01-12_00:00:00   00          
  67   0    3   5     100   92500       0       0     HGT      2025-01-12_00:00:00   00          
  68   0    3   5     100   90000       0       0     HGT      2025-01-12_00:00:00   00          
  69   0    3   5     100   85000       0       0     HGT      2025-01-12_00:00:00   00          
  70   0    3   5     100   80000       0       0     HGT      2025-01-12_00:00:00   00          
  71   0    3   5     100   75000       0       0     HGT      2025-01-12_00:00:00   00          
  72   0    3   5     100   70000       0       0     HGT      2025-01-12_00:00:00   00          
  73   0    3   5     100   65000       0       0     HGT      2025-01-12_00:00:00   00          
  74   0    3   5     100   60000       0       0     HGT      2025-01-12_00:00:00   00          
  75   0    3   5     100   55000       0       0     HGT      2025-01-12_00:00:00   00          
  76   0    3   5     100   50000       0       0     HGT      2025-01-12_00:00:00   00          
  77   0    3   5     100   45000       0       0     HGT      2025-01-12_00:00:00   00          
  78   0    3   5     100   40000       0       0     HGT      2025-01-12_00:00:00   00          
  79   0    3   5     100   35000       0       0     HGT      2025-01-12_00:00:00   00          
  80   0    3   5     100   30000       0       0     HGT      2025-01-12_00:00:00   00          
  81   0    3   5     100   27500       0       0     HGT      2025-01-12_00:00:00   00          
  82   0    3   5     100   25000       0       0     HGT      2025-01-12_00:00:00   00          
  83   0    3   5     100   22500       0       0     HGT      2025-01-12_00:00:00   00          
  84   0    3   5     100   20000       0       0     HGT      2025-01-12_00:00:00   00          
  85   0    3   5     100   17500       0       0     HGT      2025-01-12_00:00:00   00          
  86   0    3   5     100   15000       0       0     HGT      2025-01-12_00:00:00   00          
  87   0    3   5     100   12500       0       0     HGT      2025-01-12_00:00:00   00          
  88   0    3   5     100   10000       0       0     HGT      2025-01-12_00:00:00   00          
  89   0    3   5     100    7000       0       0     HGT      2025-01-12_00:00:00   00          
  90   0    3   5     100    5000       0       0     HGT      2025-01-12_00:00:00   00          
  91   0    3   5     100    3000       0       0     HGT      2025-01-12_00:00:00   00          
  92   0    3   5     100    2000       0       0     HGT      2025-01-12_00:00:00   00          
  93   0    3   5     100    1000       0       0     HGT      2025-01-12_00:00:00   00          
  94   0    3   5     100     700       0       0     HGT      2025-01-12_00:00:00   00          
  95   0    3   5     100     500       0       0     HGT      2025-01-12_00:00:00   00          
  96   0    3   5     100     400       0       0     HGT      2025-01-12_00:00:00   00          
  97   0    3   5     100     300       0       0     HGT      2025-01-12_00:00:00   00          
  98   0    3   5     100     200       0       0     HGT      2025-01-12_00:00:00   00          
  99   0    3   5     100     150       0       0     HGT      2025-01-12_00:00:00   00          
 100   0    3   5     100     100       0       0     HGT      2025-01-12_00:00:00   00          
 101   0    3   5     100      50       0       0     HGT      2025-01-12_00:00:00   00          
 102   0    3   5     100      20       0       0     HGT      2025-01-12_00:00:00   00          
 103   0    3   5     100      10       0       0     HGT      2025-01-12_00:00:00   00          
 104   0    0   0     100  100000       0       0     TMP      2025-01-12_00:00:00   00          
 105   0    0   0     100   97500       0       0     TMP      2025-01-12_00:00:00   00          
 106   0    0   0     100   95000       0       0     TMP      2025-01-12_00:00:00   00          
 107   0    0   0     100   92500       0       0     TMP      2025-01-12_00:00:00   00          
 108   0    0   0     100   90000       0       0     TMP      2025-01-12_00:00:00   00          
 109   0    0   0     100   85000       0       0     TMP      2025-01-12_00:00:00   00          
 110   0    0   0     100   80000       0       0     TMP      2025-01-12_00:00:00   00          
 111   0    0   0     100   75000       0       0     TMP      2025-01-12_00:00:00   00          
 112   0    0   0     100   70000       0       0     TMP      2025-01-12_00:00:00   00          
 113   0    0   0     100   65000       0       0     TMP      2025-01-12_00:00:00   00          
 114   0    0   0     100   60000       0       0     TMP      2025-01-12_00:00:00   00          
 115   0    0   0     100   55000       0       0     TMP      2025-01-12_00:00:00   00          
 116   0    0   0     100   50000       0       0     TMP      2025-01-12_00:00:00   00          
 117   0    0   0     100   45000       0       0     TMP      2025-01-12_00:00:00   00          
 118   0    0   0     100   40000       0       0     TMP      2025-01-12_00:00:00   00          
 119   0    0   0     100   35000       0       0     TMP      2025-01-12_00:00:00   00          
 120   0    0   0     100   30000       0       0     TMP      2025-01-12_00:00:00   00          
 121   0    0   0     100   27500       0       0     TMP      2025-01-12_00:00:00   00          
 122   0    0   0     100   25000       0       0     TMP      2025-01-12_00:00:00   00          
 123   0    0   0     100   22500       0       0     TMP      2025-01-12_00:00:00   00          
 124   0    0   0     100   20000       0       0     TMP      2025-01-12_00:00:00   00          
 125   0    0   0     100   17500       0       0     TMP      2025-01-12_00:00:00   00          
 126   0    0   0     100   15000       0       0     TMP      2025-01-12_00:00:00   00          
 127   0    0   0     100   12500       0       0     TMP      2025-01-12_00:00:00   00          
 128   0    0   0     100   10000       0       0     TMP      2025-01-12_00:00:00   00          
 129   0    0   0     100    7000       0       0     TMP      2025-01-12_00:00:00   00          
 130   0    0   0     100    5000       0       0     TMP      2025-01-12_00:00:00   00          
 131   0    0   0     100    3000       0       0     TMP      2025-01-12_00:00:00   00          
 132   0    0   0     100    2000       0       0     TMP      2025-01-12_00:00:00   00          
 133   0    0   0     100    1000       0       0     TMP      2025-01-12_00:00:00   00          
 134   0    0   0     100     700       0       0     TMP      2025-01-12_00:00:00   00          
 135   0    0   0     100     500       0       0     TMP      2025-01-12_00:00:00   00          
 136   0    0   0     100     400       0       0     TMP      2025-01-12_00:00:00   00          
 137   0    0   0     100     300       0       0     TMP      2025-01-12_00:00:00   00          
 138   0    0   0     100     200       0       0     TMP      2025-01-12_00:00:00   00          
 139   0    0   0     100     150       0       0     TMP      2025-01-12_00:00:00   00          
 140   0    0   0     100     100       0       0     TMP      2025-01-12_00:00:00   00          
 141   0    0   0     100      50       0       0     TMP      2025-01-12_00:00:00   00          
 142   0    0   0     100      20       0       0     TMP      2025-01-12_00:00:00   00          
 143   0    0   0     100      10       0       0     TMP      2025-01-12_00:00:00   00 
 ......
 852   0   19   0       1       0       0       0     VIS      2025-01-12_00:00:00   00          
 853   0   19 224       1       0       0       0     UNKNOWN  2025-01-12_00:00:00   00          
 854   0   19 225       1       0       0       0     UNKNOWN  2025-01-12_00:00:00   00          
 855   0    2  22     103      10       0       0     GUST     2025-01-12_00:00:00   00          
 856   0    1  19       1       0       0       0     UNKNOWN  2025-01-12_00:00:00   00          
 857   0    6  12       1       0       0       0     UNKNOWN  2025-01-12_00:00:00   00          
 858   0    6  11       1       0       0       0     UNKNOWN  2025-01-12_00:00:00   00          
 859   0    6  13       1       0       0       0     CEIL     2025-01-12_00:00:00   00          
 860   0    4 228       1       0       0       0     UNKNOWN  2025-01-12_00:00:00   00          
 861   0    4 227       1       0       0       0     UNKNOWN  2025-01-12_00:00:00   00          
   Successful completion of g2print  
 ```
{% endfold %}

## Pressure level

There are **40** pressure levels, **1000hPa up to 0.1hPa (~30km)**

## Land sea mask

- `./g2print.exe final_cma_lsm.grib2` <--- **Land sea mask**

{% fold info @Land sea mask %}
```log
 ungrib - grib edition num           2
 reading from grib file = final_cma_lsm.grib2                                                                                                    
      unknown model and orig center   
---------------------------------------------------------------------------------------
 rec Prod Cat Param  Lvl    Lvl      Lvl     Prod    Name            Time          Fcst
 num Disc     num    code   one      two     Templ                                 hour
---------------------------------------------------------------------------------------
   1   2    0   0       1       0       0       0     LAND     2025-01-12_00:00:00   00          
  
  
   Successful completion of g2print  
```
{% endfold %}

# ungrib

- ~~re-compile **WPS** (**WRFv450-CMA-GFS**)~~

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

*[2025-04-09]* After create a land-sea mask field for CMA_GFS (see below section), **GRIBFILE.AAB**,

- re-compile **WPS** (**WRFv450-CMA-GFS**)

```
-rw-rw-r-- 1 wpsze wpsze 3.2G Apr  2 16:56 CMA:2025-01-12_00
lrwxrwxrwx 1 wpsze wpsze   62 Apr  2 16:50 GRIBFILE.AAA -> Z_NAFP_C_BABJ_20250112000000_P_NWPC-GRAPES-GFS-GLB-00000.grib2
lrwxrwxrwx 1 wpsze wpsze   20 Apr  8 18:01 GRIBFILE.AAB -> final_cma_lsm.grib2
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
- ungrib.exe
  - We have to update the **LANDMASK** for CMA_GFS (see below section)
  - **final_cma_lsm.grib2**
    - ungrib this grib2 individually and generate a intermediate file **CMA_GFS_landmask**
    - CMA_GFS_landmask is **constant field**.
- metgrib.exe
  - namelist.wps
    ```namelist.wps
    &metgrid
    fg_name = 'CMA_GFS',
    io_form_metgrid = 2, 
    constants_name = 'CMA_GFS_landmask',
    opt_metgrid_tbl_path = './'
    /
    ```
  - **!  Successful completion of metgrid.  !**
    {% fold info @log %}
    ```log
    Processing domain 1 of 2
        CMA_GFS_landmask
    Processing 2025-01-12_00
        CMA_GFS
    Processing 2025-01-12_03
        CMA_GFS
    Processing 2025-01-12_06
        CMA_GFS
    Processing domain 2 of 2
        CMA_GFS_landmask
    Processing 2025-01-12_00
        CMA_GFS
    Processing 2025-01-12_03
        CMA_GFS
    Processing 2025-01-12_06
        CMA_GFS
    !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
    !  Successful completion of metgrid.  !
    !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
    ```
    {% endfold %}
- real.exe
  - **real_em: SUCCESS COMPLETE REAL_EM INIT**
- wrf.exe
  - **wrf: SUCCESS COMPLETE WRF**

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

##### Take IFS's land sea mask (Wrong!!)

- `./extract_landseamask.sh`

```sh
#!/bin/bash

cdo selname,lsm 20250401000000-0h-oper-fc.grib2 lsm_nan.nc

cdo setmissval,-999 lsm_nan.nc lsm.nc

cdo -f grb2 copy lsm.nc lsm.grib

cdo showname lsm.grib

grib_set -s centre=babj,dataDate=20250112,dataTime=0000,shortName=lsm,typeOfLevel=surface,level=0 lsm.grib lsm_final.grib

cdo showname lsm_final.grib

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

##### check grib2 file

In general, **GFS/ERA5/IFS 0.25 deg grib files** show

```log
# Earth assumed spherical with radius of 6 371 229.0 m  (grib2/tables/25/3.2.table)                 
shapeOfTheEarth = 6;                                                                                
Ni = 1440;                                                                                          
Nj = 721;                                                                                        
```

But, in CMA_GFS grib file, it has shown

```log
#==============   MESSAGE 1 ( length=205 )                 ==============  
GRIB {                                                                     
  # Meteorological products (grib2/tables/4/0.0.table)                     
  discipline = 0;                                                          
  editionNumber = 2;                                                       
  # Beijing  (RSMC)  (common/c-11.table)                                   
  centre = 38;                                                             
  subCentre = 0;                                                           
  # Analysis (grib2/tables/4/1.2.table)                                    
  significanceOfReferenceTime = 0;                                         
  dataDate = 20250112;                                                     
  dataTime = 0;    
......
# Earth assumed spherical with radius of 6,371,229.0 m (grib2/tables/4/3.2.table)                  
shapeOfTheEarth = 6;                                                                               
Ni = 2880;                                                                                         
Nj = 1440;
```

which **is not 0.25 deg but 0.125 deg**. Therefore, `LANDSEA` of IFS can not be used for CMA_GFS.

#### Take GFS's land sea mask (Wrong!!)

{% note primary %}
The GFS land mask is not suitable for use with CMA_GFS.
{% endnote %}

#### Solution (Successful)

Move to next section **"CMA_GFS Land Sea Mask"**

For MPASv7.3, it is **Successful**.

- **Successful completion of program ungrib.exe**
- **Finished running the init_atmosphere core**
- **Finished running the atmosphere core**

{% gi 4 3-1 %}
![](https://i.imgur.com/pdB6QnM.png)
![](https://i.imgur.com/f4xvs04.png)
![](https://i.imgur.com/lyWsoLX.png)
{% endgi %}

### atmosphere_model

- **Finished running the atmosphere core**

# CMA_GFS Land Sea Mask

{% note primary %}
If the GFS land mask is not suitable for use with CMA_GFS, the presence of 0K values could lead to problems. Is it possible for us to utilize this information to create a land-sea mask specifically for CMA_GFS from CMA_GFS?
{% endnote %}

## check grib2's soil temperature

{% gi 4 3-1 %}
![](https://i.imgur.com/So0DDy5.png)
![](https://i.imgur.com/JuV2vgI.png)
![](https://i.imgur.com/GbUafcp.png)
![](https://i.imgur.com/ZVtQcM4.png)
{% endgi %}

## Solution

- We are required to **create a land mask using the same source data as CMA_GFS to ensure consistency in our results**. In this case, I will utilize the soil temperature data at depths of 1-2 meters, as this data shows a value of 0 in ocean areas.

### wgrib2

```log
$ wgrib2 Z_NAFP_C_BABJ_20250112000000_P_NWPC-GRAPES-GFS-GLB-00000.grib2

464:416263005:d=2025011200:TMP:0-0.1 m below ground:anl:
465:418796241:d=2025011200:TMP:0.1-0.4 m below ground:anl:
466:421201726:d=2025011200:TMP:0.4-1 m below ground:anl:
467:423468299:d=2025011200:TMP:1-2 m below ground:anl:
468:424559640:d=2025011200:SPFH:0-0.1 m below ground:anl:
469:426189296:d=2025011200:SPFH:0.1-0.4 m below ground:anl:
470:427793124:d=2025011200:SPFH:0.4-1 m below ground:anl:
471:429405783:d=2025011200:SPFH:1-2 m below ground:anl:
```

- `467:423468299:d=2025011200:TMP:1-2 m below ground:anl:` is our target.

### extract and generate new grib2 for landmask

1. convert grib2 to nc file,

```sh
wgrib2 Z_NAFP_C_BABJ_20250112000000_P_NWPC-GRAPES-GFS-GLB-00000.grib2 -match ':TMP:1-2 m below ground:' -grib TMP_1_2m.grib2

grib_to_netcdf TMP_1_2m.grib2 -o TMP_1_2m.nc
```

2. modify/create landmask field for CMA_GFS,

```python
import os
import numpy as np
import xarray as xr
import matplotlib.pyplot as plt

TMP_1_2m_file = "/home/wpsze/mpas/CMA_GFS/test/ungrib/TMP_1_2m.nc"
TMP_1_2m_nc = xr.open_dataset(TMP_1_2m_file)

# plt.imshow(TMP_1_2m_nc['t'][0,:,:])

# Check for non-zero values and assign them to 1
TMP_1_2m_nc['t'] = TMP_1_2m_nc['t'].where(TMP_1_2m_nc['t'] == 0, 1)

# plt.imshow(TMP_1_2m_nc['t'][0,:,:])

TMP_1_2m_nc = TMP_1_2m_nc.rename({'t': 'lsm'})
TMP_1_2m_nc['lsm'] = TMP_1_2m_nc['lsm'].assign_attrs(
                standard_name = "land_binary_mask",
        		long_name = "Land-sea mask",
        		units = "(0 - 1)")

#================== Save =================================================
# Save the result to a new NetCDF file
TMP_1_2m_nc.to_netcdf(f'/home/wpsze/mpas/CMA_GFS/test/ungrib/new_TMP_1_2m.nc', format='NETCDF4')
```

- Left: original soil temperature; Right: our created Land sea mask field for CMA_GFS

{% gi 5 2-3 %}
![](https://i.imgur.com/5O95DVb.png)
![](https://i.imgur.com/r01Obgx.png)
{% endgi %}

3. convert landmask nc file to grib2
 
```sh
cdo -f grb2 copy new_TMP_1_2m.nc new_TMP_1_2m.grib2

# check
grib_ls new_TMP_1_2m.grib2
wgrib2 new_TMP_1_2m.grib2

grib_set -r -w packingType=grid_ieee -s centre=babj,shortName=lsm,typeOfLevel=surface,level=0,dataType=an,packingType=grid_jpeg new_TMP_1_2m.grib2 final_TMP_1_2m.grib2

mv final_TMP_1_2m.grib2 final_cma_lsm.grib2
```

- Make sure the **datetime is updated with your run**.

> **grib_set -r -w packingType=grid_ieee -s centre=babj,shortName=lsm,typeOfLevel=surface,level=0,dataType=an,packingType=grid_jpeg,dataDate=20250416,dataTime=0000 new_TMP_1_2m.grib2 final_TMP_1_2m.grib2**

- `./g2print.exe final_cma_lsm.grib2`
- `2 0 0 1` here correpsonds to `Vtable.CMA_GFS`
  - **LANDSEA**
  - `| LANDSEA  | proprtn | Land/Sea flag (1=land, 0 or 2=sea)      |  2  |  0  |  0  |   1 |`

```log
 ungrib - grib edition num           2
 reading from grib file = final_cma_lsm.grib2                                                                                                    
      unknown model and orig center   
---------------------------------------------------------------------------------------
 rec Prod Cat Param  Lvl    Lvl      Lvl     Prod    Name            Time          Fcst
 num Disc     num    code   one      two     Templ                                 hour
---------------------------------------------------------------------------------------
   1   2    0   0       1       0       0       0     LAND     2025-01-12_00:00:00   00          
  
  
   Successful completion of g2print  
```

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

    echo "Downloading ... ${tmp_cma_gfs_fname}"
    #wget -c --no-check-certificate http://data.wis.cma.cn/DCPC_WMC_BJ/open/nwp/gmf_gra/t0000/f0_f240_6h/Z_NAFP_C_BABJ_20250401000000_P_NWPC-GRAPES-GFS-GLB-00000.grib2
    wget -c --no-check-certificate http://data.wis.cma.cn/DCPC_WMC_BJ/open/nwp/gmf_gra/t0000/f0_f240_6h/${tmp_cma_gfs_fname}

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