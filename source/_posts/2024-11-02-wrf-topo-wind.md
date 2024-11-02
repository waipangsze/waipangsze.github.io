---
layout: post
title: WRF | topo-wind
categories: [WRF]
tags: [WRF,MPAS,NWP]
author: wpsze
date: 2024-11-02 13:34:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/cOWLDX0.png
banner_img: https://i.imgur.com/cOWLDX0.png
---

# Introduction 

- The Developmental Testbed Center WRFv3.4.1+ Surface Drag Parameterization Sensitivity Test Plan Points of Contact: Hongli Jiang, Michelle Harrold and Jamie Wolff November 28, 2012 

It is widely acknowledged that the Weather Research and Forecasting (WRF) model has a high surface wind speed bias, especially over plains and valleys. In recent versions of WRF, two new surface drag parameterization schemes, **both associated with the Yonsei University (YSU) planetary boundary layer (PBL) scheme**, have been developed. The Developmental Testbed Center (DTC) will be performing testing and evaluation of three WRF model configurations with the Advanced Research WRF (ARW) core (Skamarock et al. 2008). The baseline configuration will be run with the physics suite being used in the ARW High-Resolution Window (HIRESW) forecast system being run operationally at the National Centers for Environmental Prediction (NCEP). The two comparative configurations will be testing the effects of the surface drag parameterization scheme namelist option, topo_wind, which aims to correct the high wind bias seen in WRF. One configuration will be run with **topo_wind=1 (TWIND1; Jimenez and Dudhia 2011)**, which is based on the concept of a momentum sink term and makes use of the standard deviation of the subgrid-scale orography as well as the Laplacian of the topographic field. The second configuration will be run with **topo_wind=2 (TWIND2; Mass and Ovens 2012)**, which determines the subgrid terrain variance and makes the surface drag or roughness used in the model dependent on it; it also includes additional consideration for stability and wind speed. The baseline configuration will have **topo_wind=0 (turned off)**. These runs will be cold start cases initialized every 36 hours and run out to 48 hours for one full year. 

The Weather Research and Forecasting (WRF)model (Skamarock et al. 2008) has presented a **high surface wind speed bias over land** since the early versions of the model (Cheng and Steenburgh 2005). The bias still persists in more recent versions (e.g., Bernardet et al. 2005; Roux et al. 2009; Mass and Ovens 2010, 2011) and represents a limitation for the high demand of accurate surface wind estimations by different sectors such as wind-energy applications or air-quality studies.

- The wind speed is higher in plains and valleys,
- The wind speed is lower in mountains and hills.

WRF模式對風速變化趨勢模擬較好，但是風速值偏差較大，主要是因為 WRF 沒有對次網格地形阻力進行參數化，致使

{% note primary %}
- 平原和山谷風速偏大，
- 高山和丘陵風速偏小。
{% endnote %}

從WRFv3.4開始，WRF提供了與YSU PBL方案相關聯的TopoWind模式，以改善地形對近地面風的影響。 YSU PBL方案共有三種方案：

- topo_wind = 0，在近地面風計算中不包括TopoWind模式的附加地形效應；
- topo_wind = 1，將亞柵格尺度地形的標準差納入近地面風計算；
- topo_wind = 2，採用與topowind = 1 相同的方法計算近地面風，但同時透過加入亞格點地形變化加強了摩擦速度的計算。

# WRF 

- topo-wind=1 → varsso (Jimenez 2011)
- topo-wind=2 → var/var2d (Mass 2012)
  
and geog data,

- varsso=0.924km (0.5m)
- varsso_2m=3.7km
- varsso_5m=9.25km
- varsso_10m=18.5km


# Jiménez 2011

- Jiménez, P. A., & Dudhia, J. (2012). Improving the representation of resolved and unresolved topographic effects on surface wind in the WRF model. Journal of Applied Meteorology and Climatology, 51(2), 300-316.

- u = stands for the zonal wind component at the first model level

![The differentiation has been accomplished by applying the following operator to the topographic field $h$](https://i.imgur.com/JrR5M9h.png){width=600}
![](https://i.imgur.com/3r4P6Uh.png){width=600}
![](https://i.imgur.com/1lSVGvw.png){width=600}
![](https://i.imgur.com/4F3m0BK.png){width=600}