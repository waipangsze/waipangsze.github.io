---
layout: post
title: NWP | IFS | Open data
categories: [NWP]
tags: [WRF,WPS,MPAS,IC/BC,IFS,ECMWF]
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

# Convert to `grid_simple`

`$ grib_set -r -w packingType=grid_ccsds -s packingType=grid_simple ${1} ${2}`

# Convert lon to 0-360 

- In order to solving `u = 10^30` issue,

`$ cdo sellonlatbox,0,360,-90,90 input.grib2 shifted.grib2`

# Check more detail of grib2 file

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

{% fold info @title %}
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