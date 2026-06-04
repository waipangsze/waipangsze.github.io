---
layout: post
title: NWP | IFS | Open data as IC/LBC
categories: [NWP]
tags: [WRF,WPS,MPAS,IC/LBC,IFS,ECMWF]
author: wpsze
date: 2026-06-04 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://www.ecmwf.int/sites/default/files/flags-council-chamber-650px.jpg
banner_img: https://www.ecmwf.int/sites/default/files/flags-council-chamber-650px.jpg
---

- [NWP | ECMWF | IFS | Open data](https://waipangsze.github.io/2026/06/04/ECMWF-IFS/)
- [NWP | Check grib files](https://waipangsze.github.io/2025/02/05/NWP-Check-grib-files/)
- [NWP | plot GFS grib files](https://waipangsze.github.io/2025/09/11/NWP-plot-GFS-grib-files/)
- [NWP | ECMWF（歐洲中期天氣預報中心）的IFS（Integrated Forecasting System）購買數據實際成本](https://waipangsze.github.io/2026/04/09/NWP-ECMEF-IFS-purchasing-data-IC/)
- [NWP | CMA | CMA_GFS real time data](https://waipangsze.github.io/2025/04/02/NWP-CMA-CMA_GFS-real-time-data/)

---

# Vtable ***

```
GRIB1| Level| From |  To  | metgrid  | metgrid  | metgrid                                  |GRIB2|GRIB2|GRIB2|GRIB2|
Param| Type |Level1|Level2| Name     | Units    | Description                              |Discp|Catgy|Param|Level|
-----+------+------+------+----------+----------+------------------------------------------+-----------------------+
 129 | 100  |   *  |      | HGT      | m        | Height                                   |  0  |  3  |  5  | 100 |
 130 | 100  |   *  |      | TT       | K        | Temperature                              |  0  |  0  |  0  | 100 |
 131 | 100  |   *  |      | UU       | m s-1    | U                                        |  0  |  2  |  2  | 100 |
 132 | 100  |   *  |      | VV       | m s-1    | V                                        |  0  |  2  |  3  | 100 |
 157 | 100  |   *  |      | RH       | %        | Relative Humidity                        |  0  |  1  |  1  | 100 |
 165 |  1   |   0  |      | UU       | m s-1    | U                 at 10 m                |  0  |  2  |  2  | 103 |
 166 |  1   |   0  |      | VV       | m s-1    | V                 at 10 m                |  0  |  2  |  3  | 103 |
 167 |  1   |   0  |      | TT       | K        | Temperature       at  2 m                |  0  |  0  |  0  | 103 |
 168 |  1   |   0  |      | DEWPT    | K        |                                          |  0  |  0  |  6  | 103 |
     |  1   |   0  |      | RH       | %        | Relative Humidity at 2 m                 |  0  |  1  |  1  | 103 |
 172 |  1   |   0  |      | LANDSEA  | 0/1 Flag | Land/Sea flag (1=land, 0 or 2=sea)       |  2  |  0  |  0  |   1 |
 134 |  1   |   0  |      | PSFC     | Pa       | Surface Pressure                         |  0  |  3  |  0  |   1 |
 151 |  1   |   0  |      | PMSL     | Pa       | Sea-level Pressure                       |  0  |  3  |  0  | 101 |
 235 |  1   |   0  |      | SKINTEMP | K        | Skin temperature                         |  0  |  0  | 17  |   1 |
  0  |  1   |   0  |   7  | ST000007 | K        | T of 0-7 cm ground layer                 |  2  |  3  | 18  | 151 | 
  1  |  2   |   7  |  28  | ST007028 | K        | T of 7-28 cm ground layer                |  2  |  3  | 18  | 151 | 
  2  |  3   |  28  | 100  | ST028100 | K        | T of 28-100 cm ground layer              |  2  |  3  | 18  | 151 | 
  3  |  4   | 100  | 289  | ST100289 | K        | T of 100-289 cm ground layer             |  2  |  3  | 18  | 151 | 
  0  |  1   |   0  |   7  | SM000007 | fraction | Soil moisture of 0-7 cm ground layer     |  2  |  0  | 25  | 151 | 
  1  |  2   |   7  |  28  | SM007028 | fraction | Soil moisture of 7-28 cm ground layer    |  2  |  0  | 25  | 151 | 
  2  |  3   |  28  | 100  | SM028100 | fraction | Soil moisture of 28-100 cm ground layer  |  2  |  0  | 25  | 151 | 
  3  |  4   | 100  | 289  | SM100289 | fraction | Soil moisture of 100-289 cm ground layer |  2  |  0  | 25  | 151 | 
-----+------+------+------+----------+---------+-----------------------------------------+-------------------------+
#
```

# Convert to `grid_simple`

`$ grib_set -r -w packingType=grid_ccsds -s packingType=grid_simple ${1} ${2}`

# Convert lon to 0-360 

- In order to solving `u = 10^30` issue. It depends on `code of MPAS/WRF/ICON...`.

`$ cdo sellonlatbox,0,360,-90,90 input.grib2 shifted.grib2`

# Check more detail of grib2 file

## grib_ls

```sh
grib_ls ${1}

#-- Print all keys in namespace, Common namespaces: parameter, geography, statistics
grib_ls -n parameter ${1}

#-- check the GRIB2 discipline, parameterCategory, and parameterNumber (related to Vtable!!!)
#grib_ls -p discipline,parameterCategory,parameterNumber,level,shortName ${1}
grib_ls -p shortName,discipline,parameterCategory,typeOfLevel,level,typeOfLevel ${1}
#grib_ls -p typeOfLevel,level,topLevel,bottomLevel,unitsOfFirstFixedSurface ${1} # typeOfLevel = grib2 Level
#grib_ls -w shortName=t -p discipline,parameterCategory,parameterNumber,level,shortName ${1}
grib_ls -w level=0 -p shortName,discipline,parameterCategory,parameterNumber,level,typeOfLevel ${1} # check surface

```

<!-- {% fold info @title %}
{% endfold %} -->

{% fold info @grib_ls -n parameter ${1} %}
```log
ifs-grid-shifted.grib2
centre      paramId     shortName   units       name        
ecmf        136         tcw         kg m**-2    Total column water 
ecmf        260360      sot         K           Soil temperature 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        168         2d          K           2 metre dewpoint temperature 
ecmf        169         ssrd        J m**-2     Surface short-wave (solar) radiation downwards 
ecmf        137         tcwv        kg m**-2    Total column vertically-integrated water vapour 
ecmf        260015      ptype       (Code table 4.201)  Precipitation type 
ecmf        260360      sot         K           Soil temperature 
ecmf        260360      sot         K           Soil temperature 
ecmf        260048      tprate      kg m**-2 s**-1  Total precipitation rate 
ecmf        172         lsm         (0 - 1)     Land-sea mask 
ecmf        32          asn         (0 - 1)     Snow albedo 
ecmf        33          rsn         kg m**-3    Snow density 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        262000      sithick     m           Sea ice thickness 
ecmf        260199      vsw         m**3 m**-3  Volumetric soil moisture 
ecmf        175         strd        J m**-2     Surface long-wave (thermal) radiation downwards 
ecmf        132         v           m s**-1     V component of wind 
ecmf        176         ssr         J m**-2     Surface net short-wave (solar) radiation 
ecmf        131         u           m s**-1     U component of wind 
ecmf        157         r           %           Relative humidity 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        260199      vsw         m**3 m**-3  Volumetric soil moisture 
ecmf        156         gh          gpm         Geopotential height 
ecmf        156         gh          gpm         Geopotential height 
ecmf        177         str         J m**-2     Surface net long-wave (thermal) radiation 
ecmf        260199      vsw         m**3 m**-3  Volumetric soil moisture 
ecmf        179         ttr         J m**-2     Top net long-wave (thermal) radiation 
ecmf        155         d           s**-1       Divergence 
ecmf        155         d           s**-1       Divergence 
ecmf        260199      vsw         m**3 m**-3  Volumetric soil moisture 
ecmf        156         gh          gpm         Geopotential height 
ecmf        130         t           K           Temperature 
ecmf        130         t           K           Temperature 
ecmf        180         ewss        N m**-2 s   Time-integrated eastward turbulent surface stress 
ecmf        132         v           m s**-1     V component of wind 
ecmf        49          10fg        m s**-1     Maximum 10 metre wind gust since previous post-processing 
ecmf        181         nsss        N m**-2 s   Time-integrated northward turbulent surface stress 
ecmf        157         r           %           Relative humidity 
ecmf        131         u           m s**-1     U component of wind 
ecmf        157         r           %           Relative humidity 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        130         t           K           Temperature 
ecmf        260360      sot         K           Soil temperature 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        205         ro          m           Runoff     
ecmf        132         v           m s**-1     V component of wind 
ecmf        156         gh          gpm         Geopotential height 
ecmf        131         u           m s**-1     U component of wind 
ecmf        141         sd          m of water equivalent  Snow depth 
ecmf        131         u           m s**-1     U component of wind 
ecmf        228         tp          m           Total precipitation 
ecmf        132         v           m s**-1     V component of wind 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        144         sf          m of water equivalent  Snowfall   
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        201         mx2t        K           Maximum temperature at 2 metres since previous post-processing 
ecmf        130         t           K           Temperature 
ecmf        151         msl         Pa          Mean sea level pressure 
ecmf        202         mn2t        K           Minimum temperature at 2 metres since previous post-processing 
ecmf        156         gh          gpm         Geopotential height 
ecmf        155         d           s**-1       Divergence 
ecmf        156         gh          gpm         Geopotential height 
ecmf        155         d           s**-1       Divergence 
ecmf        262140      sve         m s**-1     Eastward surface sea water velocity 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        262139      svn         m s**-1     Northward surface sea water velocity 
ecmf        0           unknown     unknown     unknown    
ecmf        157         r           %           Relative humidity 
ecmf        157         r           %           Relative humidity 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        155         d           s**-1       Divergence 
ecmf        130         t           K           Temperature 
ecmf        130         t           K           Temperature 
ecmf        228246      100u        m s**-1     100 metre U wind component 
ecmf        262124      zos         m           Sea surface height 
ecmf        228247      100v        m s**-1     100 metre V wind component 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        157         r           %           Relative humidity 
ecmf        160         sdor        m           Standard deviation of sub-gridscale orography 
ecmf        132         v           m s**-1     V component of wind 
ecmf        131         u           m s**-1     U component of wind 
ecmf        235         skt         K           Skin temperature 
ecmf        131         u           m s**-1     U component of wind 
ecmf        132         v           m s**-1     V component of wind 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        163         slor        Numeric     Slope of sub-gridscale orography 
ecmf        155         d           s**-1       Divergence 
ecmf        164         tcc         (0 - 1)     Total cloud cover 
ecmf        131         u           m s**-1     U component of wind 
ecmf        132         v           m s**-1     V component of wind 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        156         gh          gpm         Geopotential height 
ecmf        156         gh          gpm         Geopotential height 
ecmf        157         r           %           Relative humidity 
ecmf        165         10u         m s**-1     10 metre U wind component 
ecmf        155         d           s**-1       Divergence 
ecmf        156         gh          gpm         Geopotential height 
ecmf        166         10v         m s**-1     10 metre V wind component 
ecmf        130         t           K           Temperature 
ecmf        155         d           s**-1       Divergence 
ecmf        130         t           K           Temperature 
ecmf        132         v           m s**-1     V component of wind 
ecmf        167         2t          K           2 metre temperature 
ecmf        131         u           m s**-1     U component of wind 
ecmf        157         r           %           Relative humidity 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        157         r           %           Relative humidity 
ecmf        130         t           K           Temperature 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        156         gh          gpm         Geopotential height 
ecmf        131         u           m s**-1     U component of wind 
ecmf        132         v           m s**-1     V component of wind 
ecmf        131         u           m s**-1     U component of wind 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        132         v           m s**-1     V component of wind 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        130         t           K           Temperature 
ecmf        155         d           s**-1       Divergence 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        156         gh          gpm         Geopotential height 
ecmf        155         d           s**-1       Divergence 
ecmf        156         gh          gpm         Geopotential height 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        157         r           %           Relative humidity 
ecmf        155         d           s**-1       Divergence 
ecmf        130         t           K           Temperature 
ecmf        157         r           %           Relative humidity 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        130         t           K           Temperature 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        157         r           %           Relative humidity 
ecmf        133         q           kg kg**-1   Specific humidity 
ecmf        131         u           m s**-1     U component of wind 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        132         v           m s**-1     V component of wind 
ecmf        131         u           m s**-1     U component of wind 
ecmf        132         v           m s**-1     V component of wind 
ecmf        135         w           Pa s**-1    Vertical velocity 
ecmf        155         d           s**-1       Divergence 
ecmf        129         z           m**2 s**-2  Geopotential 
ecmf        132         v           m s**-1     V component of wind 
ecmf        156         gh          gpm         Geopotential height 
ecmf        131         u           m s**-1     U component of wind 
ecmf        138         vo          s**-1       Vorticity (relative) 
ecmf        134         sp          Pa          Surface pressure 
ecmf        157         r           %           Relative humidity 
ecmf        155         d           s**-1       Divergence 
ecmf        130         t           K           Temperature 
164 of 164 messages in ifs-grid-shifted.grib2
```
{% endfold %}

{% fold info @grib_ls -w level=0 -p shortName,discipline,parameterCategory,parameterNumber,level,typeOfLevel ${1} %}
```log
ifs-grid-shifted.grib2
shortName          discipline         parameterCategory  parameterNumber    level              typeOfLevel        
tcw                0                  1                  51                 0                  entireAtmosphere  
ssrd               0                  4                  7                  0                  surface           
tcwv               0                  1                  64                 0                  entireAtmosphere  
ptype              0                  1                  19                 0                  surface           
tprate             0                  1                  52                 0                  surface           
lsm                2                  0                  0                  0                  surface           
asn                0                  19                 192                0                  surface           
rsn                0                  1                  61                 0                  surface           
sithick            10                 2                  1                  0                  surface           
strd               0                  5                  3                  0                  surface           
ssr                0                  4                  9                  0                  surface           
str                0                  5                  5                  0                  surface           
ttr                0                  5                  5                  0                  nominalTop        
ewss               0                  2                  62                 0                  surface           
nsss               0                  2                  63                 0                  surface           
ro                 2                  0                  201                0                  surface           
sd                 0                  1                  254                0                  surface           
tp                 0                  1                  193                0                  surface           
sf                 0                  1                  198                0                  surface           
msl                0                  3                  0                  0                  meanSea           
sve                10                 3                  14                 0                  surface           
svn                10                 3                  15                 0                  surface           
unknown            0                  7                  6                  0                  mostUnstableParcel 
zos                10                 3                  1                  0                  surface           
sdor               0                  3                  20                 0                  surface           
skt                0                  0                  17                 0                  surface           
slor               0                  3                  22                 0                  surface           
tcc                0                  6                  192                0                  entireAtmosphere  
z                  0                  3                  4                  0                  surface           
sp                 0                  3                  0                  0                  surface           
30 of 164 messages in ifs-grid-shifted.grib2
```
{% endfold %}

## ./g2print.exe

- `./g2print.exe ${1}`

{% fold info @./g2print.exe ${1} %}
```log
 ungrib - grib edition num           2
 reading from grib file = ifs-grid-shifted.grib2                                                                                                  
      ECMWF                           
---------------------------------------------------------------------------------------
 rec Prod Cat Param  Lvl    Lvl      Lvl     Prod    Name            Time          Fcst
 num Disc     num    code   one      two     Templ                                 hour
---------------------------------------------------------------------------------------
   1   0    1  51       1       0       0       0     UNKNOWN  2026-03-22_00:00:00   00          
   2   2    3  18     151       3       4       0     UNKNOWN  2026-03-22_00:00:00   00          
   3   0    1   0     100   15000       0       0     SPFH     2026-03-22_00:00:00   00          
   4   0    0   6     103       2       0       0     DPT      2026-03-22_00:00:00   00          
   5   0    4   7       1       0       0       8     DSWRF    2026-03-22_00:00:00   00  PDT4.8  
   6   0    1  64       1       0       0       0     UNKNOWN  2026-03-22_00:00:00   00          
   7   0    1  19       1       0       0       0     UNKNOWN  2026-03-22_00:00:00   00          
   8   2    3  18     151       1       2       0     UNKNOWN  2026-03-22_00:00:00   00          
   9   2    3  18     151       0       1       0     UNKNOWN  2026-03-22_00:00:00   00          
  10   0    1  52       1       0       0       0     UNKNOWN  2026-03-22_00:00:00   00          
  11   2    0   0       1       0       0       0     LAND     2026-03-22_00:00:00   00          
  12   0   19 192       1       0       0       0     MXSALB   2026-03-22_00:00:00   00          
  13   0    1  61       1       0       0       0     UNKNOWN  2026-03-22_00:00:00   00          
  14   0    2   8     100   15000       0       0     VVEL     2026-03-22_00:00:00   00          
  15  10    2   1       1       0       0       0     ICETK    2026-03-22_00:00:00   00          
  16   2    0  25     151       0       1       0     UNKNOWN  2026-03-22_00:00:00   00          
  17   0    5   3       1       0       0       8     DLWRF    2026-03-22_00:00:00   00  PDT4.8  
  18   0    2   3     100   50000       0       0     VGRD     2026-03-22_00:00:00   00          
  19   0    4   9       1       0       0       8     UNKNOWN  2026-03-22_00:00:00   00  PDT4.8  
  20   0    2   2     100   50000       0       0     UGRD     2026-03-22_00:00:00   00          
  21   0    1   1     100   40000       0       0     RH       2026-03-22_00:00:00   00          
  22   0    2  12     100   15000       0       0     RELV     2026-03-22_00:00:00   00          
  23   0    2  12     100   25000       0       0     RELV     2026-03-22_00:00:00   00          
  24   2    0  25     151       1       2       0     UNKNOWN  2026-03-22_00:00:00   00          
  25   0    3   5     100   10000       0       0     HGT      2026-03-22_00:00:00   00          
  26   0    3   5     100   85000       0       0     HGT      2026-03-22_00:00:00   00          
  27   0    5   5       1       0       0       8     UNKNOWN  2026-03-22_00:00:00   00  PDT4.8  
  28   2    0  25     151       2       3       0     UNKNOWN  2026-03-22_00:00:00   00          
  29   0    5   5       8       0       0       8     UNKNOWN  2026-03-22_00:00:00   00  PDT4.8  
  30   0    2  13     100   15000       0       0     RELD     2026-03-22_00:00:00   00          
  31   0    2  13     100   25000       0       0     RELD     2026-03-22_00:00:00   00          
  32   2    0  25     151       3       4       0     UNKNOWN  2026-03-22_00:00:00   00          
  33   0    3   5     100   60000       0       0     HGT      2026-03-22_00:00:00   00          
  34   0    0   0     100   85000       0       0     TMP      2026-03-22_00:00:00   00          
  35   0    0   0     100   10000       0       0     TMP      2026-03-22_00:00:00   00          
  36   0    2  62       1       0       0       8     UNKNOWN  2026-03-22_00:00:00   00  PDT4.8  
  37   0    2   3     100   40000       0       0     VGRD     2026-03-22_00:00:00   00          
  38   0    2  22     103      10       0       8     GUST     2026-03-22_00:00:00   00  PDT4.8  
  39   0    2  63       1       0       0       8     UNKNOWN  2026-03-22_00:00:00   00  PDT4.8  
  40   0    1   1     100   25000       0       0     RH       2026-03-22_00:00:00   00          
  41   0    2   2     100   40000       0       0     UGRD     2026-03-22_00:00:00   00          
  42   0    1   1     100   15000       0       0     RH       2026-03-22_00:00:00   00          
  43   0    1   0     100   10000       0       0     SPFH     2026-03-22_00:00:00   00          
  44   0    1   0     100   85000       0       0     SPFH     2026-03-22_00:00:00   00          
  45   0    0   0     100   60000       0       0     TMP      2026-03-22_00:00:00   00          
  46   2    3  18     151       2       3       0     UNKNOWN  2026-03-22_00:00:00   00          
  47   0    2   8     100   85000       0       0     VVEL     2026-03-22_00:00:00   00          
  48   0    2   8     100   10000       0       0     VVEL     2026-03-22_00:00:00   00          
  49   0    1   0     100   60000       0       0     SPFH     2026-03-22_00:00:00   00          
  50   2    0 201       1       0       0       8     WILT     2026-03-22_00:00:00   00  PDT4.8  
  51   0    2   3     100   25000       0       0     VGRD     2026-03-22_00:00:00   00          
  52   0    3   5     100    5000       0       0     HGT      2026-03-22_00:00:00   00          
  53   0    2   2     100   15000       0       0     UGRD     2026-03-22_00:00:00   00          
  54   0    1 254       1       0       0       0     UNKNOWN  2026-03-22_00:00:00   00          
  55   0    2   2     100   25000       0       0     UGRD     2026-03-22_00:00:00   00          
  56   0    1 193       1       0       0       8     CFRZR    2026-03-22_00:00:00   00  PDT4.8  
  57   0    2   3     100   15000       0       0     VGRD     2026-03-22_00:00:00   00          
  58   0    2  12     100   10000       0       0     RELV     2026-03-22_00:00:00   00          
  59   0    1 198       1       0       0       8     MINRH    2026-03-22_00:00:00   00  PDT4.8  
  60   0    2  12     100   85000       0       0     RELV     2026-03-22_00:00:00   00          
  61   0    2   8     100   60000       0       0     VVEL     2026-03-22_00:00:00   00          
  62   0    0   0     103       2       0       8     TMP      2026-03-22_00:00:00   00  PDT4.8  
  63   0    0   0     100    5000       0       0     TMP      2026-03-22_00:00:00   00          
  64   0    3   0     101       0       0       0     PRES     2026-03-22_00:00:00   00          
  65   0    0   0     103       2       0       8     TMP      2026-03-22_00:00:00   00  PDT4.8  
  66   0    3   5     100   20000       0       0     HGT      2026-03-22_00:00:00   00          
  67   0    2  13     100   85000       0       0     RELD     2026-03-22_00:00:00   00          
  68   0    3   5     100   30000       0       0     HGT      2026-03-22_00:00:00   00          
  69   0    2  13     100   10000       0       0     RELD     2026-03-22_00:00:00   00          
  70  10    3  14       1       0       0       0     UNKNOWN  2026-03-22_00:00:00   00          
  71   0    1   0     100    5000       0       0     SPFH     2026-03-22_00:00:00   00          
  72   0    2  12     100   60000       0       0     RELV     2026-03-22_00:00:00   00          
  73  10    3  15       1       0       0       0     UNKNOWN  2026-03-22_00:00:00   00          
  74   0    7   6      17       0       0       0     CAPE     2026-03-22_00:00:00   00          
  75   0    1   1     100   85000       0       0     RH       2026-03-22_00:00:00   00          
  76   0    1   1     100   10000       0       0     RH       2026-03-22_00:00:00   00          
  77   0    2   8     100    5000       0       0     VVEL     2026-03-22_00:00:00   00          
  78   0    2  13     100   60000       0       0     RELD     2026-03-22_00:00:00   00          
  79   0    0   0     100   20000       0       0     TMP      2026-03-22_00:00:00   00          
  80   0    0   0     100   30000       0       0     TMP      2026-03-22_00:00:00   00          
  81   0    2   2     103     100       0       0     UGRD     2026-03-22_00:00:00   00          
  82  10    3   1       1       0       0       0     DSLM     2026-03-22_00:00:00   00          
  83   0    2   3     103     100       0       0     VGRD     2026-03-22_00:00:00   00          
  84   0    1   0     100   20000       0       0     SPFH     2026-03-22_00:00:00   00          
  85   0    1   0     100   30000       0       0     SPFH     2026-03-22_00:00:00   00          
  86   0    2  12     100    5000       0       0     RELV     2026-03-22_00:00:00   00          
  87   0    1   1     100   60000       0       0     RH       2026-03-22_00:00:00   00          
  88   0    3  20       1       0       0       0     UNKNOWN  2026-03-22_00:00:00   00          
  89   0    2   3     100   85000       0       0     VGRD     2026-03-22_00:00:00   00          
  90   0    2   2     100   10000       0       0     UGRD     2026-03-22_00:00:00   00          
  91   0    0  17       1       0       0       0     UNKNOWN  2026-03-22_00:00:00   00          
  92   0    2   2     100   85000       0       0     UGRD     2026-03-22_00:00:00   00          
  93   0    2   3     100   10000       0       0     VGRD     2026-03-22_00:00:00   00          
  94   0    2   8     100   20000       0       0     VVEL     2026-03-22_00:00:00   00          
  95   0    2   8     100   30000       0       0     VVEL     2026-03-22_00:00:00   00          
  96   0    3  22       1       0       0       0     UNKNOWN  2026-03-22_00:00:00   00          
  97   0    2  13     100    5000       0       0     RELD     2026-03-22_00:00:00   00          
  98   0    6 192       1       0       0       0     CDLYR    2026-03-22_00:00:00   00          
  99   0    2   2     100   60000       0       0     UGRD     2026-03-22_00:00:00   00          
 100   0    2   3     100   60000       0       0     VGRD     2026-03-22_00:00:00   00          
 101   0    2  12     100   20000       0       0     RELV     2026-03-22_00:00:00   00          
 102   0    2  12     100   30000       0       0     RELV     2026-03-22_00:00:00   00          
 103   0    3   5     100   92500       0       0     HGT      2026-03-22_00:00:00   00          
 104   0    3   5     100  100000       0       0     HGT      2026-03-22_00:00:00   00          
 105   0    1   1     100    5000       0       0     RH       2026-03-22_00:00:00   00          
 106   0    2   2     103      10       0       0     UGRD     2026-03-22_00:00:00   00          
 107   0    2  13     100   20000       0       0     RELD     2026-03-22_00:00:00   00          
 108   0    3   5     100   70000       0       0     HGT      2026-03-22_00:00:00   00          
 109   0    2   3     103      10       0       0     VGRD     2026-03-22_00:00:00   00          
 110   0    0   0     100  100000       0       0     TMP      2026-03-22_00:00:00   00          
 111   0    2  13     100   30000       0       0     RELD     2026-03-22_00:00:00   00          
 112   0    0   0     100   92500       0       0     TMP      2026-03-22_00:00:00   00          
 113   0    2   3     100    5000       0       0     VGRD     2026-03-22_00:00:00   00          
 114   0    0   0     103       2       0       0     TMP      2026-03-22_00:00:00   00          
 115   0    2   2     100    5000       0       0     UGRD     2026-03-22_00:00:00   00          
 116   0    1   1     100   20000       0       0     RH       2026-03-22_00:00:00   00          
 117   0    1   0     100  100000       0       0     SPFH     2026-03-22_00:00:00   00          
 118   0    1   0     100   92500       0       0     SPFH     2026-03-22_00:00:00   00          
 119   0    1   1     100   30000       0       0     RH       2026-03-22_00:00:00   00          
 120   0    0   0     100   70000       0       0     TMP      2026-03-22_00:00:00   00          
 121   0    1   0     100   70000       0       0     SPFH     2026-03-22_00:00:00   00          
 122   0    2   8     100  100000       0       0     VVEL     2026-03-22_00:00:00   00          
 123   0    2   8     100   92500       0       0     VVEL     2026-03-22_00:00:00   00          
 124   0    3   5     100   50000       0       0     HGT      2026-03-22_00:00:00   00          
 125   0    2   2     100   20000       0       0     UGRD     2026-03-22_00:00:00   00          
 126   0    2   3     100   20000       0       0     VGRD     2026-03-22_00:00:00   00          
 127   0    2   2     100   30000       0       0     UGRD     2026-03-22_00:00:00   00          
 128   0    2   8     100   70000       0       0     VVEL     2026-03-22_00:00:00   00          
 129   0    2  12     100  100000       0       0     RELV     2026-03-22_00:00:00   00          
 130   0    2   3     100   30000       0       0     VGRD     2026-03-22_00:00:00   00          
 131   0    2  12     100   92500       0       0     RELV     2026-03-22_00:00:00   00          
 132   0    0   0     100   50000       0       0     TMP      2026-03-22_00:00:00   00          
 133   0    2  13     100  100000       0       0     RELD     2026-03-22_00:00:00   00          
 134   0    2  12     100   70000       0       0     RELV     2026-03-22_00:00:00   00          
 135   0    3   5     100   25000       0       0     HGT      2026-03-22_00:00:00   00          
 136   0    2  13     100   92500       0       0     RELD     2026-03-22_00:00:00   00          
 137   0    3   5     100   40000       0       0     HGT      2026-03-22_00:00:00   00          
 138   0    1   0     100   50000       0       0     SPFH     2026-03-22_00:00:00   00          
 139   0    1   1     100  100000       0       0     RH       2026-03-22_00:00:00   00          
 140   0    2  13     100   70000       0       0     RELD     2026-03-22_00:00:00   00          
 141   0    0   0     100   25000       0       0     TMP      2026-03-22_00:00:00   00          
 142   0    1   1     100   92500       0       0     RH       2026-03-22_00:00:00   00          
 143   0    2   8     100   50000       0       0     VVEL     2026-03-22_00:00:00   00          
 144   0    0   0     100   40000       0       0     TMP      2026-03-22_00:00:00   00          
 145   0    1   0     100   25000       0       0     SPFH     2026-03-22_00:00:00   00          
 146   0    1   1     100   70000       0       0     RH       2026-03-22_00:00:00   00          
 147   0    1   0     100   40000       0       0     SPFH     2026-03-22_00:00:00   00          
 148   0    2   2     100  100000       0       0     UGRD     2026-03-22_00:00:00   00          
 149   0    2  12     100   50000       0       0     RELV     2026-03-22_00:00:00   00          
 150   0    2   8     100   25000       0       0     VVEL     2026-03-22_00:00:00   00          
 151   0    2   3     100  100000       0       0     VGRD     2026-03-22_00:00:00   00          
 152   0    2   2     100   92500       0       0     UGRD     2026-03-22_00:00:00   00          
 153   0    2   3     100   92500       0       0     VGRD     2026-03-22_00:00:00   00          
 154   0    2   8     100   40000       0       0     VVEL     2026-03-22_00:00:00   00          
 155   0    2  13     100   50000       0       0     RELD     2026-03-22_00:00:00   00          
 156   0    3   4       1       0       0       0     GP       2026-03-22_00:00:00   00          
 157   0    2   3     100   70000       0       0     VGRD     2026-03-22_00:00:00   00          
 158   0    3   5     100   15000       0       0     HGT      2026-03-22_00:00:00   00          
 159   0    2   2     100   70000       0       0     UGRD     2026-03-22_00:00:00   00          
 160   0    2  12     100   40000       0       0     RELV     2026-03-22_00:00:00   00          
 161   0    3   0       1       0       0       0     PRES     2026-03-22_00:00:00   00          
 162   0    1   1     100   50000       0       0     RH       2026-03-22_00:00:00   00          
 163   0    2  13     100   40000       0       0     RELD     2026-03-22_00:00:00   00          
 164   0    0   0     100   15000       0       0     TMP      2026-03-22_00:00:00   00          
  
  
   Successful completion of g2print   
```
{% endfold %}