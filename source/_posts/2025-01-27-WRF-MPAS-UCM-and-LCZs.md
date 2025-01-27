---
layout: post
title: WRF | MPAS | UCM and LCZs
categories: [WRF]
tags: [WRF, MPAS, HPC, NWP, UCM, LCZs]
author: wpsze
date: 2025-01-27 10:35:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/FqQXJNT.png
banner_img: https://i.imgur.com/FqQXJNT.png
---

# WRF-UCM

WRF-UCM refers to the Weather Research and Forecasting model (WRF) integrated with an Urban Canopy Model (UCM). This framework is designed to enhance the simulation of urban environments within meteorological models, allowing for more accurate predictions of weather patterns in urban areas.

## Urban Canopy Model (UCM):

**A single-layer model** designed to represent urban environments. It incorporates simplified urban geometries and features such as building shadowing and heat exchange processes, improving the representation of lower boundary conditions in urban areas

## BEP+BEM

The **Building Effect Parameterization (BEP) and Building Energy Model (BEM) scheme** is an advanced modeling framework integrated within the Weather Research and Forecasting (WRF) model, designed to improve the simulation of urban climates. 

- The BEM aspect incorporates building energy consumption factors, accounting for heating and cooling needs. This integration helps in assessing the urban heat budget more accurately by considering how energy use impacts local temperatures and climate conditions.

# WRF Bug-fix/updated UCM

## New features in WRFV4.6

- Add two options (slucm_distributed_drag and distributed_ahe_opt) to WRF SLUCM (sf_urban_physics = 1) so that **spatially varying urban morphological parameters (building height, plan area index, frontal area index, roughness length for momentum, and displacement height) can be considered**. [Detail](https://github.com/wrf-model/WRF/commit/3cadf04277ac3a050e65461efb6aa939349c60a8)
  - SOURCE: Do Ngoc Khanh (Tokyo Institute of Technology)
  - This PR adds a new feature to WRF SLUCM by allowing consideration of **spatially varying global distributed urban parameters and spatially hourly monthly varying anthropogenic heat**.

### Bug fixes and enhancement in WRFV4.6

- Correct net long wave fluxes for application in modeling urban climate using Single Layer Urban Canopy Model (SLUCM). It slightly improved 2-m temperature in urban area. [Details](https://github.com/wrf-model/WRF/commit/01228e7dfc0c4c257a7511e028088eddf3dfb388)

# WRF Bug-fix/updated LCZs

## Bug fixes and enhancement in WRFV4.6

- Fix a bug in Noah-MP for uninitialized leaf mass when LCZ is used. [Detail](https://github.com/wrf-model/WRF/commit/554b12c81b081e068e24a0111ee360b528bb97b0)
- **Update urban LCZ parameter table (URBPARM_LCZ.TBL) with more reasonable values**. [Detail](https://github.com/wrf-model/WRF/commit/6156b78dfd350e0514d2455badcd7eab9b7d2d31)
- Make the Pleim-Xiu LSM compatible with MODIS LCZ. Also, effects of evaporation from transpiration, soil in both vegetated and non-veg parts and wet leaves on ground temperature all are considered. Previously only the effect from transpiration and evaporation from non-veg soil are considered. [Detail](https://github.com/wrf-model/WRF/commit/b7f31dcde42beb4be468edb0541ec02f23b20455)

## WRF Version 4.5.1 (Bug-fix Release)

- **Fix a bug in building height specification in the LCZ urban parameter table**. [Details](https://github.com/wrf-model/WRF/commit/fb4be8f7b937283ee986db73690a9654bae70b4c)

## WRF Version v4.4.2 (Bug-fix Release)

- Update urban local climate zone (LCZ) numbers in parameter tables to avoid overlap with existing NLCD land types. This update affects VEGPARM.TBL, LANDUSE.TBL, and MPTABLE.TBL. **The LCZ numbers in WRF-urban are changed from 31-41 to 51-61**. [Details](https://github.com/wrf-model/WRF/commit/61a1bde309838e2581a40e228b209ef418cd9cc2)
- Fix the units and unit conversions of several variables in subroutines CARBON and CARBON_CROP (NoahMP scheme); update urban pixel identification by including LCZ urban types in module_sf_noahmpdrv.F for surface variable initialization. [Details](https://github.com/wrf-model/WRF/commit/75c5ddc0094a4630290bec58da8ff2609da1f2c0)

## WRF Version v4.3.1 Release

- **Some of the urban parameter values for LCZ are changed to make them more generic**.

## WRF Version 4.3

- **Implementation of Local Climate Zone (LCZ using WUDAPT (31-41) landuse classes, along with standard urban classes (31-33)**. (Ref: Stewart, I.D. and Oke, T.R. (2012) Local Climate Zones for Urban Temperature Studies. Bulletin of the American Meteorological Society, 93, 1879-1900. <http://dx.doi.org/10.1175/BAMS-D-11-00019.1>). More information can be found at <https://ral.ucar.edu/sites/default/files/public/product-tool/urban-canopy-model/WRF_urban_update_Readme_file_WRF4.3.pdf>. Data from <https://www.wudapt.org/> is required. Note that the parameters in the new urban table, URBPARM_LCZ.TBL, may vary greatly from city to city. The default values are probably not appropriate for any given city. Users should adapt these values based on the city they are working with.

{% note primary %}
[Global-LCZ-for-Urban](https://waipangsze.github.io/2024/10/07/Global-LCZ-for-Urban/)
{% endnote %}

# W2W

- [Demuzere, M., Argüeso, D., Zonato, A., & Kittner, J. (2022). W2W: A Python package that injects WUDAPT's Local Climate Zone information in WRF. Journal of Open Source Software, 7(76), 4432.](https://joss.theoj.org/papers/10.21105/joss.04432.pdf)
  - The Python-based WUDAPT-to-WRF (W2W) package is developed to translate Local Climate Zone (LCZ) maps into urban canopy parameters readable by WRF

```console
# w2w.py
# A WUDAPT-to-WRF python tool that injects WUDAPT's Local Climate Zone information into WRF.

# Install
pip install w2w
# Install from GitHub:
# pip install git+https://github.com/matthiasdemuzere/w2w

# Run the tool
# Check out its help:
w2w --help

# Try with the provided sample:
# Sample data used here can be downloaded from the repository in the sample_data folder. After clicking on the file you can download it.
w2w ./sample_data lcz_zaragoza.tif geo_em.d04.nc

# Deploy using your own data:
# w2w INPUT_DIRECTORY YOUR_LCZ.TIF YOUR_GEO_EM.d0X.NC
```

![](https://i.imgur.com/pUxBAbG.png){width=500}

# Steps to Run UCM on WRF

## 1. Prepare the Environment
- Ensure you have the WRF model installed and configured properly on your system.
- Download or prepare an **urban land-use dataset** if you want to use a specific urban area map. If not, you can use the default land-use map.

## 2. Modify the Configuration Files
- **Namelist Configuration**:
  - Open the `namelist.input` file in your WRF directory.
  - Set the `sf_urban_physics` option to `1` to enable UCM:
    ```text
    sf_urban_physics = 1
    ```
    - For LCZs, `“& physics”` section of WRF namelist.input: `use_wudapt_lcz =1` (set to 0 by default).
  - Ensure that you set this option for every domain you plan to run.

- **Registry Modifications**:
  - Edit the `Registry.EM` file located in the `Registry/` directory. Add an 'h' in the appropriate column to enable output of urban canopy variables in your output files.

## 3. Prepare Urban Parameters
- Modify the `URBPARM.TBL` file located in the `run/` directory to adjust urban parameters according to your study area. This includes parameters like building height, surface albedo, and anthropogenic heat emissions.

## ERROR: Warning: too many input landuse types

```text
ERROR: ------------------------------ FATAL CALLED ------------------------------
ERROR: Warning: too many input landuse types
CRITICAL ERROR: MPAS core_physics abort

./MPAS/src/core_atmosphere/physics/physics_wrf/module_sf_noahlsm.F:2437:                     FATAL_ERROR( 'Warning: too many input landuse types' )

open(land_unit,file='LANDUSE.TBL',action='READ',status='OLD',iostat=istat)

./MPAS/src/core_atmosphere/physics/mpas_atmphys_landuse.F:180:       read(unit=land_unit,fmt=*) lucats,luseas
./MPAS/src/core_atmosphere/physics/mpas_atmphys_landuse.F:183:          write(mess,*) '   landuse type = ' // trim (lutype) // ' found', lucats, &
```

- Renew your `*.TBL` for different version of WRF.

# URBPARM.TBL

- The appropriate urban parameters are highly variable from city to city (e.g., some cities may have wide roads and short buildings, other cities may have narrow roads and tall buildings). For best use of the urban canopy models, these parameters should be tuned specifically for the city of interest.
- <https://ral.ucar.edu/sites/default/files/public/images/product_tool/urbparm.html>
- <https://github.com/wrf-model/WRF/blob/master/run/URBPARM.TBL>
- <https://github.com/wrf-model/WRF/blob/master/run/URBPARM_LCZ.TBL>

{% fold info @URBPARM.TBL %}
| URBPARM.TBL                   |                                                                                                                         |
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------|
| ZR                            | Roof level (building height) [m]                                                                                        |
| ROOF_WIDTH                    | Roof (i.e., building) width [m]                                                                                         |
| ROAD_WIDTH                    | Road width [m]                                                                                                          |
| AH                            | Anthropogenic heat [W m-2]                                                                                              |
| FRC_URB                       | Fraction of the urban landscape which does not have natural vegetation. [Fraction]                                      |
| CAPR                          | Heat capacity of roof [J m-3 K-1]                                                                                       |
| CAPB                          | Heat capacity of building wall [ J m-3 K-1]                                                                             |
| CAPG                          | Heat capacity of ground (road) [J m-3 K-1]                                                                              |
| AKSR                          | Thermal conductivity of roof [J m-1 s-1 K-1]                                                                            |
| AKSB                          | Thermal conductivity of building wall [J m-1 s-1 K-1]                                                                   |
| AKSG                          | Thermal conductivity of ground (road) [J m-1 s-1 K-1]                                                                   |
| ALBR                          | Surface albedo of roof [fraction]                                                                                       |
| ALBB                          | Surface albedo of building wall [fraction]                                                                              |
| ALBG                          | Surface albedo of ground (road) [fraction]                                                                              |
| EPSR                          | Surface emissivity of roof [-]                                                                                          |
| EPSB                          | Surface emissivity of building wall [-]                                                                                 |
| EPSG                          | Surface emissivity of ground (road) [-]                                                                                 |
| Z0R                           | Roughness length for momentum, over roof [m]                                                                            |
| Z0B                           | Roughness length for momentum, over building wall [m] Only active for CH_SCHEME == 1                                    |
| Z0G                           | Roughness length for momentum, over ground (road) [m] Only active for CH_SCHEME == 1                                    |
| CH_SCHEME                     | Ch of Wall and Road [1: M-O Similarity Theory, 2: Empirical Form of Narita et al., 1997 (recommended)]                  |
| TS_SCHEME	                    | Surface and Layer Temperatures [1: 4-layer model, 2: Force-Restore method]                                              |
| AHOPTION                      | [0: No anthropogenic heating, 1: Anthropogenic heating will be added to sensible heat flux term]                        |
| AHDIUPRF                      | Anthropogenic Heating diurnal profile. Multiplication factor applied to AH (as defined in the table above) Hourly values ( 24 of them ), starting at 01 hours Local Time. For sub-hourly model time steps, value changes on the hour and is held constant until the next hour.|
{% endfold %}

# Anthropogenic Heating (AH)

The parameters for urban land surface are highly case-dependent. The default values given in WRF are based on a few observations and I don't think they are representative. One must adjust these values based on one's specific case. However, we don't have more information about how to specify the values. there is little publications discussing this issue. So they are more empirical. It is needed to `set AHOPTION and ALHOPTION to 1` if you want to activate anthropogenic heating. Again, note that these values are also case-dependent. They may not well apply to your case. You probably need to do lots of tuning.

- [Holst, Christopher Claus, Chi‐Yung Tam, and Johnny CL Chan. "Sensitivity of urban rainfall to anthropogenic heat flux: A numerical experiment." Geophysical Research Letters 43.5 (2016): 2240-2248.](https://agupubs.onlinelibrary.wiley.com/doi/pdfdirect/10.1002/2015GL067628)

# Roughness length

The roughness length is a crucial parameter that influences the simulation of wind profiles and heat exchange in urban areas. 

```text
#
# Ch of Wall and Road [ 1: M-O Similarity Theory, 2: Empirical Form of Narita et al., 1997 (recommended) ]
#      (sf_urban_physics=1)
#

CH_SCHEME: 2

#
# Z0B:  Roughness length for momentum, over building wall [ m ]
#       Only active for CH_SCHEME == 1
#      (sf_urban_physics=1)
#

Z0B: 0.0001, 0.0001, 0.0001

#
# Z0G:  Roughness length for momentum, over ground (road) [ m ]
#       Only active for CH_SCHEME == 1
#      (sf_urban_physics=1,2,3)
#

Z0G: 0.01, 0.01, 0.01
```

Set `Roughness length` Only `active for CH_SCHEME == 1`, 

and, <https://github.com/wrf-model/WRF/blob/d66e442fccc04111067e29274c9f9eaccc3cef28/phys/module_sf_urban.F#L83>

```text
!  CH_SCHEME [integer 1 or 2] : Sfc exchange scheme used for building wall and road
!                         [1: M-O Similarity Theory,  2: Empirical Form (recommend)]
```

- [The surface roughness issue in UCM](https://forum.mmm.ucar.edu/threads/the-surface-roughness-issue-in-ucm.11533/)
- [Why roughness(ZNT) of Urban is lower than Croplands in UCM?](https://forum.mmm.ucar.edu/threads/why-roughness-znt-of-urban-is-lower-than-croplands-in-ucm.11006/)
  - I have tried to modify `module_sf_urban.F` to change the calculation of Z0C to find whether the calculation of surface roughness in the Urban Canopy Model would influence the output of ZNT from wrfout files. But I find that the change in Z0C will not impact the output of ZNT that ZNT in wrfout files kept about 0.12 no matter how Z0C change.

{% gi 4 2-2 %}
![](https://i.imgur.com/gQRdbHS.png)
![](https://i.imgur.com/mkjsJYt.png)
![](https://i.imgur.com/SBmM5A8.png)
![](https://i.imgur.com/XZWd0rk.png)
{% endgi %}

In above Figure 2, 

- left is theta level is 83m, and 1st level is 166m
- right is theta level is ~23m, and 1st level is ~46m

## Papars

- [Yeung, Pak Shing. Refinement of surface roughness length for the weather research and forecasting model. Diss. 2018.| HKUST](https://lbezone.hkust.edu.hk/pdfviewer/web/viewer.php?file=aHR0cHM6Ly9sYmV6b25lLmhrdXN0LmVkdS5oay9vYmovMS9vLzk5MTAxMjYzNDk2NzMwMzQxMi85OTEwMTI2MzQ5NjczMDM0MTIucGRm#page=1)

# ZDC + Z0C + 2m is larger than the 1st WRF level Stop in subroutine urban - change ZDC and Z0C

```fortran
!    ZDC [m]             : Zero plane displacement height (1/5 of ZR)
!    Z0C [m]             : Roughness length above canyon for momentum (1/10 of ZR)
!     ZA     [m]     : First atmospheric level
!                      as a lowest boundary condition
```

- Calculate the following urban canyon geometry parameters following [Macdonald's (1998) formulations, Macdonald, R. W., Griffiths, R. F., & Hall, D. J. (1998). An improved method for the estimation of surface roughness of obstacle arrays. Atmospheric environment, 32(11), 1857-1864](https://www.sciencedirect.com/science/article/abs/pii/S1352231097004032).

{% fold info @module_sf_urban.F %}
```fortran
! Parameters derived from the morphological terms above.
! These parameters are computed in the code.
!
!    HGT [-]             : normalized building height
!    SVF [-]             : sky view factor
!    R   [-]             : Normalized roof width (a.k.a. "building coverage ratio")
!    RW  [-]             : = 1 - R
!    Z0C [m]             : Roughness length above canyon for momentum (1/10 of ZR)
!    Z0HC [m]            : Roughness length above canyon for heat (1/10 of Z0C)
!    ZDC [m]             : Zero plane displacement height (1/5 of ZR)

......

Cd         = 1.2
alpha_macd = 4.43
beta_macd  = 1.0

......

      ZDC = ZR * ( 1.0 + ( alpha_macd ** ( -R ) )  * ( R - 1.0 ) )

      Z0C = ZR * ( 1.0 - ZDC/ZR ) * &
           exp (-(0.5 * beta_macd * Cd / (VonK**2) * ( 1.0-ZDC/ZR) * lambda_f )**(-0.5))

   IF( ZDC+Z0C+2. >= ZA) THEN
    CALL wrf_error_fatal ("ZDC + Z0C + 2m is larger than the 1st WRF level "// &
                           "Stop in subroutine urban - change ZDC and Z0C" ) 
   END IF

......

      ! The following urban canyon geometry parameters are following Macdonald's (1998) formulations
      
      ! Lambda_P :: Plan areal fraction, which corresponds to R for a 2-d canyon.
      ! Lambda_F :: Frontal area index, which corresponds to HGT for a 2-d canyon
      ! Cd       :: Drag coefficient ( 1.2 from Grimmond and Oke, 1998 )
      ! Alpha_macd :: Emperical coefficient ( 4.43 from Macdonald et al., 1998 )
      ! Beta_macd  :: Correction factor for the drag coefficient ( 1.0 from Macdonald et al., 1998 )

      Lambda_P = R_TBL(LC)
      Lambda_F = HGT_TBL(LC)
      Cd         = 1.2
      alpha_macd = 4.43 
      beta_macd  = 1.0

......
```
{% endfold %}

# How is 10m wind speed computed?

Generally 10m wind is computed based on the `Monin-Obukhov similarity theory`; however, please see `phys/module_sf_myjsfc.F` regarding the MYJ PBL. For the MM5 surface layer option, see `module_sf_sfclay.F`. The ARW Technical Note lists published papers that discuss this, if you are interested in reading more. 

- <https://forum.mmm.ucar.edu/threads/how-is-10m-wind-speed-computed.75/> 

# Applications (papers)

- [Chen, Fei, et al. "**Utilizing the coupled WRF/LSM/Urban modeling system with detailed urban classification** to simulate the urban heat island phenomena over the Greater Houston area." Fifth Symposium on the Urban Environment. Vol. 25. American Meteorological Society Vancouver, BC, Canada, 2004.](https://ams.confex.com/ams/pdfpapers/79765.pdf)
- [Kusaka, Hiroyuki, and Fujio Kimura. "**Coupling a single-layer urban canopy model with a simple atmospheric model**: Impact on urban heat island simulation for an idealized case." Journal of the Meteorological Society of Japan. Ser. II 82.1 (2004): 67-80.](https://www.jstage.jst.go.jp/article/jmsj/82/1/82_1_67/_pdf)
- [Chen, Fei, et al. "**The integrated WRF/urban modeling system**: development, evaluation, and applications to urban environmental problems." (2011).](https://pdxscholar.library.pdx.edu/cgi/viewcontent.cgi?article=1045&context=mengin_fac)
- [Kusaka, Hiroyuki, et al. "**Numerical simulation of urban heat island effect by the WRF model with 4-km grid increment**: An inter-comparison study between the urban canopy model and slab model." 気象集誌. 第 2 輯 90.0 (2012): 33-45.](https://www.jstage.jst.go.jp/article/jmsj/90B/0/90B_2012-B03/_pdf)
- [**利用 WRF/Urban Canopy Model 模擬探討台灣北部都市地區之熱島效應**. 2016. PhD Thesis. National Central University.](http://ir.lib.ncu.edu.tw:88/thesis/view_etd.asp?URN=103621004)
- [Li, Huidong, et al. "**Quantifying urban heat island intensity** and its physical mechanism using WRF/UCM." Science of the total environment 650 (2019): 3110-3119.](https://www.researchgate.net/profile/Huidong-Li-3/publication/328053113_Quantifying_urban_heat_island_intensity_and_its_physical_mechanism_using_WRFUCM/links/616a2df6039ba26844489530/Quantifying-urban-heat-island-intensity-and-its-physical-mechanism-using-WRF-UCM.pdf)
- [Wang, Yi, et al. "Impact of **land surface heterogeneity on urban heat island** circulation and sea‐land breeze circulation in **Hong Kong**." Journal of Geophysical Research: Atmospheres 122.8 (2017): 4332-4352.](https://agupubs.onlinelibrary.wiley.com/doi/pdf/10.1002/2017JD026702)
- [Kim, G., Cha, D. H., Song, C. K., & Kim, H. (2021). **Impacts of anthropogenic heat and building height** on urban precipitation over the Seoul metropolitan area in regional climate modeling. Journal of Geophysical Research: Atmospheres, 126(23), e2021JD035348.](https://agupubs.onlinelibrary.wiley.com/doi/pdfdirect/10.1029/2021JD035348)

## HKCU

- [Hu, C., C.-Y. Tam, Z. Li, J. Chen, Y. Li, K.K.W. Cheung, Z.-L. Yang, J.-E. Chu and T. N. Chow, 2024: **Impacts of Urban Heat and Surface Roughness** on Landfalling Tropical Cyclone Intensity: A Case Study based on TC Victor (1997) in Coastal South China, J. Geophys. Res., 129, e2024JD040915.](https://doi.org/10.1029/2024JD040915)
  - urban surface roughness contributes to a decrease in postlandfall storm intensity through frictional energy dissipation
  - the roughness of the city's surface also helped to weaken the storm because of the friction.
- [Hu, C., C.-Y. Tam and Z. Wang, 2024: **Urbanization Impacts on South China Greater Bay Area Extreme Rainfall** - Sensitivity to Synoptic Systems during Pre-monsoon Period Climate Dynamics, Adv. Atmos. Sci., https://doi.org/10.1007/s00376-024-4114-x](https://doi.org/10.1007/s00376-024-4114-x)
- [Hu, C., C.-Y. Tam, Z.-L. Yang and Z. Wang, 2024: **Analyzing Urban Influence on Extreme Winter Precipitation** through Observations and Numerical Simulation of Two South China Case Studies, Scientific Reports, https://www.nature.com/articles/s41598-024-52193-2q](https://www.nature.com/articles/s41598-024-52193-2q)

## HKO

- [Mok, Hing Yim, Man Chi Wu, and Cheuk Yin Cheng. "Spatial variation of the characteristics of urban heat island effect in Hong Kong." Journal of Civil Engineering and Architecture 5.9 (2011): 779-786.](https://www.hko.gov.hk/hko/publica/reprint/r923.pdf)
- [城市發展如何影響香港的氣候 | 城市熱島效應](https://www.climatechange.edu.hk/cause-of-climate-change/7-3-zh-%E9%A6%99%E6%B8%AF%E7%9A%84%E6%B0%A3%E5%80%99%E8%AE%8A%E5%8C%96%E5%8F%88%E6%98%AF%E6%80%8E%E6%A8%A3%E5%91%A2%EF%B9%96/7-3-2-zh-%E5%9F%8E%E5%B8%82%E7%99%BC%E5%B1%95%E5%A6%82%E4%BD%95%E5%BD%B1%E9%9F%BF%E9%A6%99%E6%B8%AF%E7%9A%84%E6%B0%A3%E5%80%99/?lang=zh-hant)
  - 全球暖化及本地城市發展皆會影響香港的氣候，根據天文台的研究，城市發展對香港長期暖化趨勢的貢獻估計可達百分之五十。
  - 在香港，熱島效應基本上是夜間現象，它在冬季時較為顯著，尤其是在大氣穩定、微風及天朗氣清的日子最為明顯。
  - **香港的高樓建築及高密度城市發展增加了地面的磨擦力及阻擋空氣流動，降低市區內部風速**。圖3.7顯示1968至2015年間横瀾島的年均風速無明顯的趨勢，而位於市區的京士柏年均風速則明顯下降。
- [微氣候](https://www.climatechange.edu.hk/cause-of-climate-change/7-3-zh-%e9%a6%99%e6%b8%af%e7%9a%84%e6%b0%a3%e5%80%99%e8%ae%8a%e5%8c%96%e5%8f%88%e6%98%af%e6%80%8e%e6%a8%a3%e5%91%a2%ef%b9%96/7-3-3-zh-%e5%be%ae%e6%b0%a3%e5%80%99/?lang=zh-hant#source)

## HK Planning Department

- [Urban Climatic Map and Standards for Wind Environment | 2011](https://www.tpb.gov.hk/en/papers/TPB/1001-tpb_8972.pdf)
  - <https://www.eeb.gov.hk/sites/default/files/susdev/html/en/council/SSCPaper02-12Att2e.pdf>
  - 

# References

1. [Tewari, M., Chen, F., Kusaka, H., & Miao, S. (2007). Coupled WRF/Unified Noah/urban-canopy modeling system. Ncar WRF Documentation, NCAR, Boulder, 122, 1-22.](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=89e976035880446f1e33a9f8492af4da05a2b011)
2. [Barlage, Michael, Shiguang Miao, and Fei Chen. "Impact of physics parameterizations on high‐resolution weather prediction over two Chinese megacities." Journal of Geophysical Research: Atmospheres 121.9 (2016): 4487-4498.](https://agupubs.onlinelibrary.wiley.com/doi/pdf/10.1002/2015JD024450)
3. [Smithson, C. L., White, N. J., Klomp, H. R., Monson, E. C., & Adams, B. R. (2023). User Guide for Urban Canopy Modeling in WRF. Version 1.0.](https://scholarsarchive.byu.edu/cgi/viewcontent.cgi?article=7694&context=facpub)
4. [Updates of WRF-urban in WRF 4.3: Local Climate Zones, Mitigation Strategies, building materials permeability and new buildings drag coefficient | March 24, 2021](https://ral.ucar.edu/sites/default/files/public/product-tool/urban-canopy-model/WRF_urban_update_Readme_file_WRF4.3.pdf)
   1. implemented in WRF, allows now to incorporate 11 urban classes corresponding to the WUDAPT(<http://www.wudapt.org/>) Local Climate Zones (LCZ)
   2. `“& physics”` section of WRF namelist.input: `use_wudapt_lcz =1` (set to 0 by default).
5. [The new version of the BEP+BEM scheme in WRF 4.3: Local Climate Zones, Mitigation Strategies, building materials permeability and new buildings drag coefficient | February 10, 2021](https://ral.ucar.edu/sites/default/files/public/product-tool/urban-canopy-model/README_BEP_BEM_MODIFICATIONS.pdf)
   1. The new version of the **BEP+BEM urban multilayer scheme**, implemented in WRF, allows now to incorporate 10 urban classes corresponding to the WUDAPT (<http://www.wudapt.org/>) Local Climate Zones (LCZ).
6. [城市气象模拟的1、2、3（上）| 朱浩楠 | 2016年09月30日](https://mp.weixin.qq.com/s/BIXCzU-QTBGkOVyBRJ8aEQ)