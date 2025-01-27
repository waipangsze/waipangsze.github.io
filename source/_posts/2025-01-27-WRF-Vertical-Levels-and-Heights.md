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

```python
from netCDF4 import Dataset
from wrf import getvar
import numpy as np
np.set_printoptions(suppress=True)

def main(file_nc):
    nc = Dataset(file_nc)
    hgt = getvar(nc, 'HGT')
    z = getvar(nc, 'z')

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

Outputs,

```console
離地高度(m)為：
[    4.6424522    30.92521      68.38203      99.607834    141.92523
   199.51625     257.37473     313.9709      663.8559     1213.3403
  1480.827      1592.7434     1732.3972     1928.7981     2228.9375
  2558.0645     2887.5166     3199.1963     3503.0833     3894.8406
  4270.9155     4674.8535     5122.3745     5496.455      5847.2725
  6315.642      6890.278      7477.73       7919.236      8284.508
  9007.295      9896.075     10943.57      12093.356     13241.985
 14453.84      15761.201     17026.54      17861.408     18336.861    ]

eta值為：
[0.9994     0.996      0.99115    0.9871     0.9816     0.9741
 0.96655    0.95914996 0.91314995 0.84099996 0.806      0.7915
 0.77349997 0.7485     0.71099997 0.671      0.6325     0.59749997
 0.565      0.5255     0.48950002 0.454      0.417      0.3885
 0.3635     0.333      0.2985     0.267      0.24499999 0.2285
 0.199      0.16600001 0.133      0.1015     0.0755     0.052
 0.0315     0.015      0.006      0.0015    ]
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

1. [Mapping vertical levels to physical altitude](https://forum.mmm.ucar.edu/threads/mapping-vertical-levels-to-physical-altitude.9906/)
2. [Vertical Levels Adjustment](https://forum.mmm.ucar.edu/threads/vertical-levels-adjustment.5638/)
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
 3. [Stretched vertical levels information?](https://forum.mmm.ucar.edu/threads/stretched-vertical-levels-information.14975/)
 4. [Does the REAL program generate the Full level height based on the International Standard Atmosphere?](https://forum.mmm.ucar.edu/threads/does-the-real-program-generate-the-full-level-height-based-on-the-international-standard-atmosphere.16833/)
 5. [Vertical profile of Wind](https://forum.mmm.ucar.edu/threads/vertical-profile-of-wind.16348/)
 6. [计算wrfout垂直层离地高度](https://mp.weixin.qq.com/s/4txXV2kuYktlvasxkiKdeg)