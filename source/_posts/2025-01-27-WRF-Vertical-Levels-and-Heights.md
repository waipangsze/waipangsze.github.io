---
layout: post
title: WRF | Vertical Levels and Heights
categories: [WRF]
tags: [WRF, MPAS, HPC, NWP, UCM]
author: wpsze
date: 2025-01-27 14:19:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/oyjw9E5.png
banner_img: https://i.imgur.com/oyjw9E5.png
---

{% note primary %}
- [**WRF Users Guide documentation !!**](https://www2.mmm.ucar.edu/wrf/users/wrf_users_guide/build/html/index.html)
- [Development Team Announcements > **Frequently Asked Questions**](https://forum.mmm.ucar.edu/forums/frequently-asked-questions.115/)
{% endnote %}

# Hybrid Vertical Coordinate

WRF is a hybrid vertical coordinate (HVC) and at the surface, it is terrain-following - meaning the heights come from the static geographic data used in the WPS process. Additional details about the HVC can be found in the [Dynamics chapter](https://www2.mmm.ucar.edu/wrf/users/wrf_users_guide/build/html/dynamics.html) of the WRF Users Guide.

- Prior to WRFV4.0, the vertical coordinate defaulted to terrain-following. To convert back to that option, set `“hybrid_opt=0”` in the `&dynamics` section of the namelist. real.exe and wrf.exe must both be run with the same `“hybrid_opt”` value.
- When using HVC output with post-processors, use either hydrostatic pressure (`P_HYD`) or total pressure (`PB + P`) for diagnostics and vertical interpolation.
- In wrfout, you can find variables `PH` and `PHB`, `(PH + PHB)/9.8` will **give you the real altitude of each model level**. You can do vertical interpolation to put the variables in any levels you want for calculation.
- `PH` and `PHB` are geopotential, which is a physical variable defined in geoscience

{% gi 3 3 %}
![](https://i.imgur.com/3ECQSPE.png)
![](https://i.imgur.com/v4MPzsr.png)
![](https://i.imgur.com/oyjw9E5.png)
{% endgi %}

# Determine pressure and height at each WRF vertical level

- [Determine pressure and height at each WRF vertical level](https://forum.mmm.ucar.edu/threads/determine-pressure-and-height-at-each-wrf-vertical-level.114/)

```text
They can be computed from:

(P + PB)*0.01 for pressure at half-sigma level, and

(PH + PHB)/9.81 for geopotential height at full level.

The model levels are not at constant pressure or height surfaces because they follow terrain.
```

{% note primary %}
the result of (PH+PHB)/9.8 is the height above sea level that is not what you need to check.
{% endnote %}

Above surface, (`HGT` is terrain height.)

{% note primary %}
(PH+PHB)/9.81-HGT
{% endnote %}

The center height above surface, absolute altitude should be calculated by running average from the top and bottom:  

{% note primary %}
(((PH(k)+PH(k+1)) / 2) + ((PHB(k)+(PHB(k+1)) / 2) / 9.81 – HGT,  
{% endnote %}

where PH and PHB are staggered in the vertical direction, meaning that they are at the tops and bottoms of grid cells. HGT is terrain height. This center height of grid cell is changing from model time to time based on definition of eta vertical coordinate system. You never get it fixed. So you cannot get the answer to your question by re-setting eta levels.  

# WRF離地⾼度設定與計算

WRF離地⾼度設定與計算, 簡單分享⼀個使⽤python計算wrfout中的離地⾼度程序，如下：

- `"znu"`  "eta values on half (mass) levels"
- `"znw"`  "eta values on full (w) levels"  ""

```python
from netCDF4 import Dataset
from wrf import getvar
import numpy as np
np.set_printoptions(suppress=True)

def main(file_nc):
    nc = Dataset(file_nc)
    hgt = getvar(nc, 'HGT') # "HGT" "Terrain Height"   "m" ij
    z = getvar(nc, 'z')     # z (geopotential /g)

    hei = z - hgt
    eta = getvar(nc, 'ZNU')
    print('離地高度(m)為：')
    print(hei.mean(dim=['south_north', 'west_east']).values)
    print('\neta值為：')
    print(eta.values)

if __name__ == '__main__':
    file_in = r'F:\WRF\wrfout\DaiBu1\test\wrfout_d02_2016-01-01_00_00_00'
    main(file_in)
```

其中 `HGT` 變數代表了地面海拔高度，在 geo_em 檔案中就存在了，而 `z` 代表了海平面高度，是一個診斷量，根據氣壓等值進行計算出來的。而ZNU代表了垂直方向上質量（Mass）格點的 eta 值，也就是可以在 namelist.input 中手動設定 eta_levels 後得到的，其中注意 **eta_levels 為非交錯（Unstaggered）格點**，數量比質量格點多1。

```text
eta=(P-Ptop)/(Pbot-Ptop)
```

`P` 代表某一層的氣壓，也就是​​你需要研究的那一層， `Ptop` 代表模式頂層氣壓， `Pbot` 代表地面氣壓。 （WRF中好像是10hpa，不太確定）。從表達式可以看出，**地面的eta值就是1，頂層就是0**。這樣一來，無論某個地區的下墊面的海拔高度是多少，它的eta座標值都統一成1。

Outputs, **e_vert = 54**

```console
離地高度(m)為：
[   25.79018    84.52496   159.36661   254.26878   373.91403   523.6823
   709.4084    936.94196  1211.5869   1537.3103   1915.9166   2346.727
  2826.5383   3351.045    3919.6106   4535.6753   5203.207    5926.446
  6709.601    7526.155    8336.745    9129.773    9904.727   10662.59
 11405.757   12136.74    12855.216   13558.873   14246.067   14916.217
 15570.322   16211.534   16845.14    17475.96    18106.285   18739.186
 19379.889   20031.79    20695.127   21368.908   22051.965   22742.492
 23437.81    24135.63    24835.205   25537.508   26244.611   26958.385
 27679.389   28406.797   29139.291   29875.842   30616.08   ]

eta值為：
[0.9970323  0.9902915  0.98175085 0.9710067  0.9576102  0.9410889
 0.92098445 0.8969075  0.8686075  0.83604616 0.79945725 0.759369
 0.7165741  0.6719424  0.6260139  0.57906395 0.53141975 0.4834582
 0.43560207 0.38982862 0.3482536  0.31100053 0.27762014 0.24770983
 0.22090887 0.19689405 0.1753757  0.15609428 0.13881733 0.1233364
 0.10946479 0.09703521 0.08589777 0.07591815 0.06697594 0.05896334
 0.0517837  0.04535042 0.03958592 0.03442067 0.02979238 0.02564523
 0.0219292  0.01859947 0.01561588 0.01294246 0.01054696 0.00840049
 0.00647715 0.00475375 0.00320951 0.0018258  0.00058594]
```

加密低層層數，總共設定了40層質量格點，對應的 `&domains` 組中的 `eta_levels` 設定如下：

```namelist
 eta_levels  = 1.0000, 0.9988, 0.9932, 0.9891, 0.9851,
0.9781, 0.9701, 0.9630, 0.9553, 0.8710,
0.811, 0.801, 0.782, 0.765, 0.732,
0.690, 0.652, 0.613, 0.582, 0.548,
0.503, 0.476, 0.432, 0.402, 0.375,
0.352, 0.314, 0.283, 0.251, 0.239,
0.218, 0.180, 0.152, 0.114, 0.089,
0.062, 0
```

# Choosing an appropriate number of vertical levels (e_vert)

- [Choosing an appropriate number of vertical levels (e_vert)](https://forum.mmm.ucar.edu/threads/choosing-an-appropriate-number-of-vertical-levels-e_vert.10682/)
  

A better question for this is "what is the stretching rate I need to use?"

See [Setting Model Vertical Levels](https://www2.mmm.ucar.edu/wrf/users/wrf_users_guide/build/html/initialization.html#setting-model-vertical-levels) in the WRF Users' Guide.

A good procedure to follow may be:

1. Determine the model top pressure.
2. Determine how thick your lowest layer is (50 m by default)
3. Determine maximum thickness (1 km by default).

Then based on your needs, the stretching rates can be chosen using the table in chapter 4 of UG (link above) as a guideline.

1. Choose the stretching rate(s) - larger if you want fewer levels but it’s not recommended that the rate be > 1.3.
2) Find the minimum number of levels that works for that stretching factor by iterating running real and changing e_vert. The minimum is the one that gives the smoothest variation in DNW, which can also be viewed in wrfinput as part of the process

It is worth mentioning that when one chooses this method, the real program prints out the thickness of the layer, and it can be very good information for making adjustments for the stretching parameters.

*Additional Notes
Smoothly varying vertical levels can be very important for model stability. Tests have shown that two different sets of vertical levels gives different numbers of CFL prints.
For cases with dz > dx (as can occur for dx < 1 km at the top), tests have also shown that continuous stretching works through to the model top better than having many equal dz levels.


# References

1. [Setting first layer thickness](https://forum.mmm.ucar.edu/threads/setting-first-layer-thickness.11680/)
2. [height of first layer](https://forum.mmm.ucar.edu/threads/height-of-first-layer.9128/)
3. [calculate height of first model layer](https://forum.mmm.ucar.edu/threads/calculate-height-of-first-model-layer.9831/)
4. [Mapping vertical levels to physical altitude](https://forum.mmm.ucar.edu/threads/mapping-vertical-levels-to-physical-altitude.9906/)
5. [Vertical Levels Adjustment](https://forum.mmm.ucar.edu/threads/vertical-levels-adjustment.5638/)
   1. (1) To increase vertical levels below a specific height, I would suggest you run REAL program first to create eta levels. Then you can manually increase the number of levels based on the automatically generated eta levels by REAL. For example , suppose you run REAL and get the following eta levels:
    ```text
    eta_levels =
    1, 0.993, 0.983, 0.97, 0.954, 0.934, 0.909, 0.88,
    ....
    ....
    /
    You can then replace the first several eta levels by:
    1, 0.999, 0.998, 0.996, 0.993, 0.990, 0.980. 0.970, 0.960, 0.950,
    0.940, 0.930, 0.920, 0.910, 0.900, 0.890, 0.880, 0.870,
    ```
    2. This is just an example to show how to add more levels within certain height
    3. (2) Once wrfinput is produced, you will find `PH` and `PHB` in wrfinput, and **(PH=PHB) is the geopotential height** corresponding to model levels.
 6. [Stretched vertical levels information?](https://forum.mmm.ucar.edu/threads/stretched-vertical-levels-information.14975/)
 7. [Does the REAL program generate the Full level height based on the International Standard Atmosphere?](https://forum.mmm.ucar.edu/threads/does-the-real-program-generate-the-full-level-height-based-on-the-international-standard-atmosphere.16833/)
 8. [Vertical profile of Wind](https://forum.mmm.ucar.edu/threads/vertical-profile-of-wind.16348/)
 9. [计算wrfout垂直层离地高度](https://mp.weixin.qq.com/s/4txXV2kuYktlvasxkiKdeg)