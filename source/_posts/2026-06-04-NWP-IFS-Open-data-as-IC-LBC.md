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

- [**Parameter database | ECMWF**](https://codes.ecmwf.int/grib/param-db/)

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
 141 |  1   |   0  |      | SNOW     | kg m-2   |Water Equivalent of Accumulated Snow Depth|  0  |  1  | 254 |   1 | 
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

- <https://forum.mmm.ucar.edu/threads/how-to-build-vtable.11753/>

```log
 ungrib - grib edition num           2
 reading from grib file = ifs-grid-shifted.grib2                                                                                                  
      ECMWF                           
---------------------------------------------------------------------------------------
 rec Prod Cat Param  Lvl    Lvl      Lvl     Prod    Name            Time          Fcst
 num Disc     num    code   one      two     Templ                                 hour
---------------------------------------------------------------------------------------

  77   2    0   0       1       0       0       0     LAND     2026-05-19_00:00:00   00

  22   0    3   5     100   30000       0       0     HGT      2026-05-19_00:00:00   00          
  23   0    3   5     100  100000       0       0     HGT      2026-05-19_00:00:00   00          
  24   0    3   5     100    5000       0       0     HGT      2026-05-19_00:00:00   00          
  25   0    3   5     100   60000       0       0     HGT      2026-05-19_00:00:00   00          
  31   0    3   5     100   85000       0       0     HGT      2026-05-19_00:00:00   00          
  33   0    3   5     100    1000       0       0     HGT      2026-05-19_00:00:00   00          
  35   0    3   5     100   20000       0       0     HGT      2026-05-19_00:00:00   00          
 119   0    3   5     100   92500       0       0     HGT      2026-05-19_00:00:00   00          
 121   0    3   5     100   10000       0       0     HGT      2026-05-19_00:00:00   00          
 125   0    3   5     100   40000       0       0     HGT      2026-05-19_00:00:00   00          
 126   0    3   5     100   25000       0       0     HGT      2026-05-19_00:00:00   00          
 127   0    3   5     100   15000       0       0     HGT      2026-05-19_00:00:00   00          
 129   0    3   5     100   50000       0       0     HGT      2026-05-19_00:00:00   00          
 130   0    3   5     100   70000       0       0     HGT      2026-05-19_00:00:00   00 

   2   0    3   4       1       0       0       0     GP       2026-05-19_00:00:00   00          
  18   0    3   4     100   85000       0       0     GP       2026-05-19_00:00:00   00          
  19   0    3   4     100    1000       0       0     GP       2026-05-19_00:00:00   00          
  20   0    3   4     100   20000       0       0     GP       2026-05-19_00:00:00   00          
  29   0    3   4     100   30000       0       0     GP       2026-05-19_00:00:00   00          
  30   0    3   4     100  100000       0       0     GP       2026-05-19_00:00:00   00          
  32   0    3   4     100    5000       0       0     GP       2026-05-19_00:00:00   00          
  34   0    3   4     100   60000       0       0     GP       2026-05-19_00:00:00   00          
 116   0    3   4     100   40000       0       0     GP       2026-05-19_00:00:00   00          
 117   0    3   4     100   25000       0       0     GP       2026-05-19_00:00:00   00          
 118   0    3   4     100   15000       0       0     GP       2026-05-19_00:00:00   00          
 120   0    3   4     100   50000       0       0     GP       2026-05-19_00:00:00   00          
 122   0    3   4     100   70000       0       0     GP       2026-05-19_00:00:00   00          
 123   0    3   4     100   10000       0       0     GP       2026-05-19_00:00:00   00          
 124   0    3   4     100   92500       0       0     GP       2026-05-19_00:00:00   00  

   1   0    2  63       1       0       0       8     UNKNOWN  2026-05-19_00:00:00   00  PDT4.8  
   4   2    3  18     151       2       3       0     UNKNOWN  2026-05-19_00:00:00   00          
   6  10    3  14       1       0       0       0     UNKNOWN  2026-05-19_00:00:00   00          
   8   0    1  51       1       0       0       0     UNKNOWN  2026-05-19_00:00:00   00          
  10  10    3  15       1       0       0       0     UNKNOWN  2026-05-19_00:00:00   00          
  11   0    1  64       1       0       0       0     UNKNOWN  2026-05-19_00:00:00   00          
  14   2    3  18     151       0       1       0     UNKNOWN  2026-05-19_00:00:00   00          
  16   0    3  20       1       0       0       0     UNKNOWN  2026-05-19_00:00:00   00          
  21   0    3  22       1       0       0       0     UNKNOWN  2026-05-19_00:00:00   00          
  37   0    0  17       1       0       0       0     UNKNOWN  2026-05-19_00:00:00   00          
  39   2    3  18     151       3       4       0     UNKNOWN  2026-05-19_00:00:00   00          
  47   0    1  19       1       0       0       0     UNKNOWN  2026-05-19_00:00:00   00          
  56   0    1  52       1       0       0       0     UNKNOWN  2026-05-19_00:00:00   00          
  67   0    1  61       1       0       0       0     UNKNOWN  2026-05-19_00:00:00   00          
  68   2    3  18     151       1       2       0     UNKNOWN  2026-05-19_00:00:00   00          
  71   2    0  25     151       0       1       0     UNKNOWN  2026-05-19_00:00:00   00          
  78   2    0  25     151       1       2       0     UNKNOWN  2026-05-19_00:00:00   00          
  87   2    0  25     151       2       3       0     UNKNOWN  2026-05-19_00:00:00   00          
  92   2    0  25     151       3       4       0     UNKNOWN  2026-05-19_00:00:00   00          
  97   0    4   9       1       0       0       8     UNKNOWN  2026-05-19_00:00:00   00  PDT4.8  
  99   0    5   5       1       0       0       8     UNKNOWN  2026-05-19_00:00:00   00  PDT4.8  
 100   0    5   5       8       0       0       8     UNKNOWN  2026-05-19_00:00:00   00  PDT4.8  
 110   0    2  62       1       0       0       8     UNKNOWN  2026-05-19_00:00:00   00  PDT4.8  
 128   0    1 254       1       0       0       0     UNKNOWN  2026-05-19_00:00:00   00 
```

# 0.5-degree (GRIB2) GFS data sets (For reference)

For example, the Vtable.GFS file contains GRIB2 Vtable fields, but is used for both 1-degree (GRIB1) GFS and **0.5-degree (GRIB2) GFS data sets**.

- <https://github.com/wrf-model/WPS/blob/master/ungrib/Variable_Tables/Vtable.GFS>

```log
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
 130 | 102  |   0  |      | PMSL     | Pa      | Sea-level Pressure                      |  0  |  3  | 192 | 101 |
 144 | 112  |   0  |  10  | SM000010 | fraction| Soil Moist 0-10 cm below grn layer (Up) |  2  |  0  | 192 | 106 |
 144 | 112  |  10  |  40  | SM010040 | fraction| Soil Moist 10-40 cm below grn layer     |  2  |  0  | 192 | 106 |
 144 | 112  |  40  | 100  | SM040100 | fraction| Soil Moist 40-100 cm below grn layer    |  2  |  0  | 192 | 106 |
 144 | 112  | 100  | 200  | SM100200 | fraction| Soil Moist 100-200 cm below gr layer    |  2  |  0  | 192 | 106 |
 144 | 112  |  10  | 200  | SM010200 | fraction| Soil Moist 10-200 cm below gr layer     |  2  |  0  | 192 | 106 |
  11 | 112  |   0  |  10  | ST000010 | K       | T 0-10 cm below ground layer (Upper)    |  0  |  0  |  0  | 106 |
  11 | 112  |  10  |  40  | ST010040 | K       | T 10-40 cm below ground layer (Upper)   |  0  |  0  |  0  | 106 |
  11 | 112  |  40  | 100  | ST040100 | K       | T 40-100 cm below ground layer (Upper)  |  0  |  0  |  0  | 106 |
  11 | 112  | 100  | 200  | ST100200 | K       | T 100-200 cm below ground layer (Bottom)|  0  |  0  |  0  | 106 |
  85 | 112  |   0  |  10  | ST000010 | K       | T 0-10 cm below ground layer (Upper)    |  2  |  0  |  2  | 106 |
  85 | 112  |  10  |  40  | ST010040 | K       | T 10-40 cm below ground layer (Upper)   |  2  |  0  |  2  | 106 |
  85 | 112  |  40  | 100  | ST040100 | K       | T 40-100 cm below ground layer (Upper)  |  2  |  0  |  2  | 106 |
  85 | 112  | 100  | 200  | ST100200 | K       | T 100-200 cm below ground layer (Bottom)|  2  |  0  |  2  | 106 |
  11 | 112  |  10  | 200  | ST010200 | K       | T 10-200 cm below ground layer (Bottom) |  0  |  0  |  0  | 106 |
  91 |   1  |   0  |      | SEAICE   | proprtn | Ice flag                                | 10  |  2  |  0  |   1 |
  81 |   1  |   0  |      | LANDSEA  | proprtn | Land/Sea flag (1=land, 0 or 2=sea)      |  2  |  0  |  0  |   1 |
  81 |   1  |   0  |      | LANDN    | proprtn |                                         |  2  |  0  | 218 |   1 |
   7 |   1  |   0  |      | SOILHGT  | m       | Terrain field of source analysis        |  0  |  3  |  5  |   1 |
  11 |   1  |   0  |      | SKINTEMP | K       | Skin temperature                        |  0  |  0  |  0  |   1 |
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
#
#  Vtable for GFS pressure-level data from the ncep server.
#  This version includes fields from the Tropopause and Max Wind levels used by WRF V3.6.1 and later.
#  NCEP has used multiple definitions of the soil temperature in their output and the Vtable attempts
#  to account for these possibilities. (Definition changed 14 Jan 2015).
#
# As of 22 March 2021:
#
#  https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/gfs.ccyymmdd/hh/atmos/
#  ftp://ftpprd.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/gfs.ccyymmdd/hh/atmos/
#
#                          approx.    grid    hours      domain  dx       notes
#                         file size   no.
#
#  gfs.t12z.pgrb2.0p25    510000 Kb   193  1-h to 120-h  global  0.25 deg  (41 p-levels plus sfc and trop, 1000 to .01 hPa).
#                                          3-h to 384-h
#  gfs.t12z.pgrb2.0p50    150000 Kb     4  3-h to 384-h  global  0.5  deg  (41 p-levels plus sfc and trop, 1000 to .01 hPa).
#
#
#  As of mid-2017 the GFS provides two land mask fields in the pressure-level output. WPS uses LANDN if available
#  and renames it LANDSEA.
#
#  As of WPS V4.1 (April 2019) the default PMSL is changed to MSLET. MSLET is an unsmoothed sea level pressure.
#  NCEP included MSLET in their GFS files beginning 12z on 10 May 2011 and in their GDAS files at 12z 14 January 2015.
#  The smoother PRMSL is in all GFS/GDAS pressure files.
#  For GFS files prior to those dates use PRMSL as shown in the following line:
#  2 | 102  |   0  |      | PMSL     | Pa      | Sea-level Pressure                      |  0  |  3  |  1  | 101 |
#
#
#   Abbreviated history of GFS changes (all starting at 12 UTC)
#
# 29 November 2022  - Updated GFS to V16.3.0. Improved snow depth prediction. 
#                   - Assimilate Leo-Geo winds, MetOp-C AVHRR and ASCAT winds. Upgrade CRTM to V2.4.0
# 22 March 2021     - Upgrade GFS to V16. Update FV3. Add a new gravity wave parameterization, scale-aware 
#                     TKE-EDMF scheme, update RRTMG. Use LETKF, 4D-IAU, new satellite obs.
#  7 November 2019  - Updated to V15.2.0. Assimilate more satellite data. Fix mask issues over water and 
#                     add LANDN field.
# 12 June 2019      - Updated to V15.1.0 with FV3 dynamical core. (requires WPS V4.0 or later)
# 19 July 2017      - Change to land mask and other terrestrial variables (requires WPS V3.9.1.1)
#                   - Change missing value, removal of grid 211 (80km conus). Flag value of .468 in soil moisture.
# 11 May 2016       - Assimilate AMSU-A radiances, AVHRR winds, CRTMv2.2.1, 4d hybrid das.
#                   - Add 5 stratospheric levels to ouput. UPP V7.0. Land surface and GWD upates.
# 14 January 2015   - Add MSLET to GDAS. Change from T574 (27km) to T1534 (13km). Fcst length to 240 h.
#                   - semi-lagrangian, uses rtgsst, Z0 dependent on vegetation type, CRTMv2.1.3, 0.25 deg output
# 10 May 2011       - Add MSLET to output. New thermal roughness length. Elevation of buoys to 10m.
#                   - Improved GSI, update CRTM. Recomputed BE.
# 27 July 2010      - T382 (35km) to T574 (27km). new pbl, gravity wave drag, updated deep convective scheme
# 13 May 2008       - RRTM radiation, aerosol, cloud overlap, windsat sfc winds, flow dependent bev.
# 25 September 2007 - Implement UPP.
# 31 May 2005       - T254L64 to T382L64. Change in soil output to 3 levels.
# 29 October 2002   - Change from T170L42  to T254L64. Model top changed from 2 hPA to .2 hPa
#
#  Prior to 12z 22 March 2021:
#
#  gfs.t12z.pgrb2.0p25    220000 Kb   193  3-h to 240-h  global  0.25 deg  (26 p-levels plus sfc and trop, 1000 to 10 mb).
#  gfs.t12z.pgrb2.0p50     68000 Kb     4  3-h to 240-h  global  0.5  deg  (26 p-levels plus sfc and trop, 1000 to 10 mb).
#                                          
#  Prior to 12z 14 January 2015:
#
#  gfs.t12z.pgrb2f00       56000 Kb     4  3-h to 192-h  global  0.5  deg  (26 p-levels plus sfc and trop, 1000 to 10 mb).
#  gfs.t12z.pgrbf00.grib2  18000 Kb     3  3-h to 384-h  global  1.0  deg  (26 p-levels plus sfc and trop, 1000 to 10 mb).
```

# Variables

## `HGT` and `GEOPT`

- <https://codes.ecmwf.int/grib/param-db/129>
- <https://codes.ecmwf.int/grib/param-db/156>

In meteorological `GRIB` files, `HGT` and `GEOPT` both represent the vertical height of a given atmospheric layer, but they are encoded in different mathematical units and formats.

| Parameter | Name | Variable Description | Unit | Common Application |
|---|---|---|---|---|
| HGT | Geopotential Height | The height of a pressure surface above mean sea level. | $\text{gpm}$ (geopotential meters) or $\text{m}$ | Synoptic chart plotting (e.g., finding $500\text{ hPa}$ troughs/ridges). |
| GEOPT | Geopotential | The gravitational potential energy per unit mass at a specific location. | $\text{m}^2\text{ s}^{-2}$ | Raw model output data; used as surface orography in dynamic modeling. |

You can easily convert between the two using the Earth's standard gravitational acceleration ($g = 9.80665 \text{ m s}^{-2}$): 

$$HGT = \frac{GEOPT}{g}$$

### Practical Use in GRIB Data

* If you are extracting GEOPT (e.g., from an `ECMWF invariant GRIB file`), you will need to divide the grid values by $9.80665$ to get the physical altitude in meters required by weather models like WRF. 
* If you are analyzing `HGT`, the values are already practically identical to geometric meters, making them immediately readable for overlaying on weather maps.

## `SOILGEO` and `SOILHGT`

`SOILGEO` and `SOILHGT` represent the surface orography (the elevation of the ground/terrain) at the bottom boundary of weather models, mirroring the exact relationship between `GEOPT` and `HGT`.

* **SOILGEO (Surface Geopotential)**: The gravitational potential energy per unit mass at surface terrain level. It is encoded in $\text{m}^2\text{ s}^{-2}$. This corresponds to the standard ECMWF variable parameter Z (Geopotential) extracted explicitly from an "invariant" surface file.
* **SOILHGT (Terrain / Surface Height)**: The physical altitude of the ground surface above mean sea level. It is encoded in geometric meters ($\text{m}$). 
* Just like free-atmosphere profiles, the two variables are linked strictly by the standard gravity of Earth ($g = 9.80665 \text{ m s}^{-2}$): $\text{SOILHGT} = \frac{\text{SOILGEO}}{9.80665}$

## Snow depth

- <https://codes.ecmwf.int/grib/param-db/141>

|       Name | Snow depth            |
|-----------:|-----------------------|
| Short name | sd                    |
|       Unit | m of water equivalent |

- grib 2 format
  
| Key	Value          |     | 
|--------------------|-----|
| localTablesVersion | 1   | 
| discipline         | 0   |  
| parameterCategory  | 1   | 
| parameterNumber    | 254 |  

## Seaice

- <https://codes.ecmwf.int/grib/param-db/31>

|       Name | Sea ice area fraction |
|-----------:|-----------------------|
| Short name | ci                    |
|       Unit | (0 - 1)               |

- grib 2 format

| Key	Value               |    | 
|-------------------------|----|
| discipline              | 10 |
| parameterCategory       | 2  |
| parameterNumber         | 0  |
| typeOfFirstFixedSurface | 1  |