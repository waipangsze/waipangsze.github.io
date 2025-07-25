---
layout: post
title: NWP | Check grib files
categories: [NWP]
tags: [MPAS, NWP, WRF, PGW, ERA5, GFS, grib, GRIB1, GRIB2, NetCDF, CMIP6]
author: wpsze
date: 2025-02-05 16:35:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/dPlkBuU.png
banner_img: https://i.imgur.com/dPlkBuU.png
---

{% note primary %}
- [**WRF | GRIB1, GRIB2, NetCDF**](https://waipangsze.github.io/2023/05/06/GRIB1_GRIB2_NetCDF/)
- [**ERA5 CDS upgrade (2024 Sep)**](https://waipangsze.github.io/2024/08/16/ERA5-2024-upgrade/)
- [**NWP | ERA5 | GRIB1 and GRIB2**](https://waipangsze.github.io/2025/02/05/NWP-ERA5-GRIB1-GRIB2/)
- [**MPAS | WRF | Extract subset of GFS GRIB files**](https://waipangsze.github.io/2025/02/19/MPAS-WRF-Extract-GFS-GRIB-files/)
- [**NWP | CMA | CMA_GFS real time data**](https://waipangsze.github.io/2025/04/02/NWP-CMA-CMA_GFS-real-time-data/)
- [**NWP | Herbie | Download NWP model output (grib2) | MPAS | WRF**](https://waipangsze.github.io/2025/04/10/NWP-Herbie-Download-NWP-model-output-MPAS-WRF/)
{% endnote %}

---

# ECMWF Parameter database

- <https://codes.ecmwf.int/grib/param-db/>
  - ![](https://i.imgur.com/W0pbXLZ.png){width=400}

# cdo

- `cdo showname`
  - {% fold info @cdo showname ERA5-0p25-SL-2023052100.grib %}
    ```log
        $ cdo showname ERA5-0p25-SL-2023052100.grib
    var165 var166 var168 var167 var172 var151 var31 var34 var235 var33 var141 var139 var170 var183 var236 var134 var39 var40 var41 var42 var129
    cdo    showname: Processed 21 variables [0.07s 33MB].
    ```
    {% endfold %}
  - {% fold info @cdo showname ERA5-0p25-PL-2023052100.grib %}
    ```log
    $ cdo showname ERA5-0p25-PL-2023052100.grib
    var129 var157 var133 var130 var131 var132
    cdo    showname: Processed 6 variables [0.35s 31MB].
    ```
    {% endfold %}
- `cdo info`
  - {% fold info @cdo info ERA5-0p25-SL-2023052100.grib %}
    ```log
    $ cdo info ERA5-0p25-SL-2023052100.grib
        -1 :       Date     Time   Level Gridsize    Miss :     Minimum        Mean     Maximum : Parameter ID
        1 : 2023-05-21 00:00:00       0  1038240       0 :     -21.115     0.39898      22.422 : 165.128       
        2 : 2023-05-21 00:00:00       0  1038240       0 :     -21.346     0.35972      21.077 : 166.128       
        3 : 2023-05-21 00:00:00       0  1038240       0 :      197.14      274.89      302.18 : 168.128       
        4 : 2023-05-21 00:00:00       0  1038240       0 :      201.04      279.49      311.52 : 167.128       
        5 : 2023-05-21 00:00:00       0  1038240       0 :      0.0000     0.33590      1.0000 : 172.128       
        6 : 2023-05-21 00:00:00       0  1038240       0 :      94799.  1.0077e+05  1.0389e+05 : 151.128       
        7 : 2023-05-21 00:00:00       0  1038240  351876 :      0.0000     0.17234      1.0000 : 31.128        
        8 : 2023-05-21 00:00:00       0  1038240  351876 :      269.65      287.09      307.00 : 34.128        
        9 : 2023-05-21 00:00:00       0  1038240       0 :      200.20      279.62      322.07 : 235.128       
        10 : 2023-05-21 00:00:00       0  1038240       0 :      100.00      130.06      450.00 : 33.128        
        11 : 2023-05-21 00:00:00       0  1038240       0 :      0.0000      1.1549      10.000 : 141.128       
        12 : 2023-05-21 00:00:00     3.5  1038240       0 :      215.26      280.31      323.69 : 139.128       
        13 : 2023-05-21 00:00:00    17.5  1038240       0 :      215.73      280.54      318.04 : 170.128       
        14 : 2023-05-21 00:00:00      64  1038240       0 :      216.82      280.37      314.96 : 183.128       
        15 : 2023-05-21 00:00:00   177.5  1038240       0 :      219.36      279.96      312.29 : 236.128       
        16 : 2023-05-21 00:00:00       0  1038240       0 :      49688.      96483.  1.0365e+05 : 134.128       
        17 : 2023-05-21 00:00:00     3.5  1038240       0 :   -0.014522    0.087194     0.76871 : 39.128        
        18 : 2023-05-21 00:00:00    17.5  1038240       0 : -0.00035179    0.089905     0.76121 : 40.128        
        19 : 2023-05-21 00:00:00      64  1038240       0 : -0.00012223    0.090184     0.76250 : 41.128        
        20 : 2023-05-21 00:00:00   177.5  1038240       0 :      0.0000    0.085637     0.75583 : 42.128        
        21 : 2023-05-21 00:00:00       0  1038240       0 :     -1386.5      3724.3      57458. : 129.128       
    cdo    info: Processed 21 variables over 1 timestep [0.33s 42MB].
    ```
    {% endfold %}
  - {% fold info @cdo info ERA5-0p25-PL-2023052100.grib %}
    ```log
    $ cdo info ERA5-0p25-PL-2023052100.grib
        -1 :       Date     Time   Level Gridsize    Miss :     Minimum        Mean     Maximum : Parameter ID
        1 : 2023-05-21 00:00:00     100  1038240       0 :  4.1082e+05  4.6062e+05  4.8298e+05 : 129.128       
        2 : 2023-05-21 00:00:00     100  1038240       0 :  5.7310e-05  0.00092131    0.025504 : 157.128       
        3 : 2023-05-21 00:00:00     100  1038240       0 :  3.2372e-06  3.9020e-06  4.1034e-06 : 133.128       
        4 : 2023-05-21 00:00:00     100  1038240       0 :      221.09      258.37      281.97 : 130.128       
        5 : 2023-05-21 00:00:00     100  1038240       0 :     -34.221      23.626      115.38 : 131.128       
    
    212 : 2023-05-21 00:00:00   97500  1038240       0 :     0.96080      78.736      157.37 : 157.128       
    213 : 2023-05-21 00:00:00   97500  1038240       0 :  3.1545e-06   0.0070588    0.022532 : 133.128       
    214 : 2023-05-21 00:00:00   97500  1038240       0 :      221.14      280.76      314.10 : 130.128       
    215 : 2023-05-21 00:00:00   97500  1038240       0 :     -31.454     0.78810      25.805 : 131.128       
    216 : 2023-05-21 00:00:00   97500  1038240       0 :     -29.676     0.38837      25.550 : 132.128       
    217 : 2023-05-21 00:00:00  100000  1038240       0 :     -4127.8      603.85      3193.9 : 129.128       
    218 : 2023-05-21 00:00:00  100000  1038240       0 :     0.87153      76.483      157.51 : 157.128       
    219 : 2023-05-21 00:00:00  100000  1038240       0 :  3.1545e-06   0.0072340    0.023612 : 133.128       
    220 : 2023-05-21 00:00:00  100000  1038240       0 :      234.08      282.30      315.62 : 130.128       
    221 : 2023-05-21 00:00:00  100000  1038240       0 :     -25.980     0.47058      22.410 : 131.128       
    222 : 2023-05-21 00:00:00  100000  1038240       0 :     -23.276     0.37781      22.248 : 132.128       
        :       Date     Time   Level Gridsize    Miss :     Minimum        Mean     Maximum : Parameter ID
    cdo    info: Processed 6 variables over 1 timestep [1.88s 39MB].
    ```
    {% endfold %}
- `cdo sinfo`
  - {% fold info @cdo sinfo ERA5-0p25-SL-2023052100.grib %}
    ```log
    $ cdo sinfo ERA5-0p25-SL-2023052100.grib
    File format : GRIB
        -1 : Institut Source   T Steptype Levels Num    Points Num Dtype : Parameter ID
        1 : ECMWF    unknown  v instant       1   1   1038240   1  P16  : 165.128       
        2 : ECMWF    unknown  v instant       1   1   1038240   1  P16  : 166.128       
        3 : ECMWF    unknown  v instant       1   1   1038240   1  P16  : 168.128       
        4 : ECMWF    unknown  v instant       1   1   1038240   1  P16  : 167.128       
        5 : ECMWF    unknown  v instant       1   1   1038240   1  P24  : 172.128       
        6 : ECMWF    unknown  v instant       1   1   1038240   1  P16  : 151.128       
        7 : ECMWF    unknown  v instant       1   1   1038240   1  P16  : 31.128        
        8 : ECMWF    unknown  v instant       1   1   1038240   1  P16  : 34.128        
        9 : ECMWF    unknown  v instant       1   1   1038240   1  P16  : 235.128       
        10 : ECMWF    unknown  v instant       1   1   1038240   1  P16  : 33.128        
        11 : ECMWF    unknown  v instant       1   1   1038240   1  P24  : 141.128       
        12 : ECMWF    unknown  v instant       1   2   1038240   1  P16  : 139.128       
        13 : ECMWF    unknown  v instant       1   3   1038240   1  P16  : 170.128       
        14 : ECMWF    unknown  v instant       1   4   1038240   1  P16  : 183.128       
        15 : ECMWF    unknown  v instant       1   5   1038240   1  P16  : 236.128       
        16 : ECMWF    unknown  v instant       1   1   1038240   1  P16  : 134.128       
        17 : ECMWF    unknown  v instant       1   2   1038240   1  P16  : 39.128        
        18 : ECMWF    unknown  v instant       1   3   1038240   1  P16  : 40.128        
        19 : ECMWF    unknown  v instant       1   4   1038240   1  P16  : 41.128        
        20 : ECMWF    unknown  v instant       1   5   1038240   1  P16  : 42.128        
        21 : ECMWF    unknown  v instant       1   1   1038240   1  P24  : 129.128       
    Grid coordinates :
        1 : lonlat                   : points=1038240 (1440x721)
                                lon : 0 to 359.75 by 0.25 degrees_east  circular
                                lat : 90 to -90 by -0.25 degrees_north
    Vertical coordinates :
        1 : surface                  : levels=1
        2 : depth_below_land         : levels=1
                                depth : 3.5 cm
                            bounds : 0-7 cm
        3 : depth_below_land         : levels=1
                                depth : 17.5 cm
                            bounds : 7-28 cm
        4 : depth_below_land         : levels=1
                                depth : 64 cm
                            bounds : 28-100 cm
        5 : depth_below_land         : levels=1
                                depth : 177.5 cm
                            bounds : 100-255 cm
    Time coordinate :  1 step
        RefTime =  2023-05-21 00:00:00  Units = hours  Calendar = proleptic_gregorian
    YYYY-MM-DD hh:mm:ss  YYYY-MM-DD hh:mm:ss  YYYY-MM-DD hh:mm:ss  YYYY-MM-DD hh:mm:ss
    2023-05-21 00:00:00
    cdo    sinfo: Processed 21 variables over 1 timestep [0.08s 34MB].
    ```
    {% endfold %}
  - {% fold info @cdo sinfo ERA5-0p25-PL-2023052100.grib %}
    ```log
        $ cdo sinfo ERA5-0p25-PL-2023052100.grib
    File format : GRIB
        -1 : Institut Source   T Steptype Levels Num    Points Num Dtype : Parameter ID
        1 : ECMWF    unknown  v instant      37   1   1038240   1  P16  : 129.128       
        2 : ECMWF    unknown  v instant      37   1   1038240   1  P16  : 157.128       
        3 : ECMWF    unknown  v instant      37   1   1038240   1  P16  : 133.128       
        4 : ECMWF    unknown  v instant      37   1   1038240   1  P16  : 130.128       
        5 : ECMWF    unknown  v instant      37   1   1038240   1  P16  : 131.128       
        6 : ECMWF    unknown  v instant      37   1   1038240   1  P16  : 132.128       
    Grid coordinates :
        1 : lonlat                   : points=1038240 (1440x721)
                                lon : 0 to 359.75 by 0.25 degrees_east  circular
                                lat : 90 to -90 by -0.25 degrees_north
    Vertical coordinates :
        1 : pressure                 : levels=37
                                plev : 100 to 100000 Pa
    Time coordinate :  1 step
        RefTime =  2023-05-21 00:00:00  Units = hours  Calendar = proleptic_gregorian
    YYYY-MM-DD hh:mm:ss  YYYY-MM-DD hh:mm:ss  YYYY-MM-DD hh:mm:ss  YYYY-MM-DD hh:mm:ss
    2023-05-21 00:00:00
    cdo    sinfo: Processed 6 variables over 1 timestep [0.35s 31MB].
    ```
    {% endfold %}

# grib_ls

- `grib ls`
  - {% fold info @grib_ls ERA5-0p25-SL-2023052100.grib %}
    ```log
    $ grib_ls ERA5-0p25-SL-2023052100.grib
    ERA5-0p25-SL-2023052100.grib
    edition      centre       typeOfLevel  level        dataDate     stepRange    dataType     shortName    packingType  gridType     
    1            ecmf         surface      0            20230521     0            an           10u          grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           10v          grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           2d           grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           2t           grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           lsm          grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           msl          grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           ci           grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           sst          grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           skt          grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           rsn          grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           sd           grid_simple  regular_ll  
    1            ecmf         depthBelowLandLayer  0            20230521     0            an           stl1         grid_simple  regular_ll  
    1            ecmf         depthBelowLandLayer  7            20230521     0            an           stl2         grid_simple  regular_ll  
    1            ecmf         depthBelowLandLayer  28           20230521     0            an           stl3         grid_simple  regular_ll  
    1            ecmf         depthBelowLandLayer  100          20230521     0            an           stl4         grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           sp           grid_simple  regular_ll  
    1            ecmf         depthBelowLandLayer  0            20230521     0            an           swvl1        grid_simple  regular_ll  
    1            ecmf         depthBelowLandLayer  7            20230521     0            an           swvl2        grid_simple  regular_ll  
    1            ecmf         depthBelowLandLayer  28           20230521     0            an           swvl3        grid_simple  regular_ll  
    1            ecmf         depthBelowLandLayer  100          20230521     0            an           swvl4        grid_simple  regular_ll  
    1            ecmf         surface      0            20230521     0            an           z            grid_simple  regular_ll  
    21 of 21 messages in ERA5-0p25-SL-2023052100.grib

    21 of 21 total messages in 1 files
    ```
    {% endfold %}

# wgrib2

- `wgrib2`
  - {% fold info @title %}
    ```log
    ```
    {% endfold %}