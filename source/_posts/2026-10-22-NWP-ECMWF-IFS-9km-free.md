---
layout: post
title: NWP | ECMWF IFS 9km free
categories: [NWP]
tags: [MPAS, WRF, NWP, GFS, FNL, IFS, ERA5, CMA, BUFR]
author: wpsze
date: 2026-10-22 06:30:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/wzcbxiX.png
banner_img: https://i.imgur.com/wzcbxiX.png
---

- [**NWP | ECMWF | IFS | Open data**](https://waipangsze.github.io/2024/11/12/ECMWF-IFS/)
- [**NWP | IFS | Open data as IC/LBC**](https://waipangsze.github.io/2026/06/04/NWP-IFS-Open-data-as-IC-LBC/)
- [NWP | Check grib files](https://waipangsze.github.io/2025/02/05/NWP-Check-grib-files/)
- [NWP | plot GFS grib files](https://waipangsze.github.io/2025/09/11/NWP-plot-GFS-grib-files/)
- [NWP | ECMWF（歐洲中期天氣預報中心）的IFS（Integrated Forecasting System）購買數據實際成本](https://waipangsze.github.io/2026/04/09/NWP-ECMEF-IFS-purchasing-data-IC/)
- [NWP | CMA | CMA_GFS real time data](https://waipangsze.github.io/2025/04/02/NWP-CMA-CMA_GFS-real-time-data/)

---

- [**9 km free and open data offers a step change in localised forecasts | 20 October 2026**](https://www.ecmwf.int/en/about/media-centre/news/2026/9-km-free-and-open-data-offers-step-change-localised-forecasts)
  - ECMWF has just made a subset of its highest-resolution 9 km data free and open. The move will give more people access to accurate and localised forecasts.

---

# Main differences between the new and old versions 

Main differences between the new and old versions are as follows:

| Comparison Item | Original 0.25° | New 0.1° |
| :--- | :--- | :--- |
| **Latitude/Longitude Grid Spacing** | 0.25° | 0.1° |
| **Number of Grid Points in the Same Area** | Used as baseline | Approximately 6.25 times the original |
| **Underlying IFS Forecast** | High-resolution forecast | The same set of forecasts |
| **Spatial Information** | Coarser grid | Can retain more spatial details |
| **File Organization** | Surface and pressure-level fields are merged | Divided into `-sfc` and `-pl` files |
| **Data Arrival Time** | Provided according to the original open data schedule | About 2 hours later than the corresponding 0.25° data (9 km open data is released with a short delay of two hours (09:34UTC) rather than in real time (07:34UTC) |
| **Access Choice** | Continues to be provided | Provided in parallel with 0.25° |

{% note primary %}
The 0.1° (high-resolution) data is published about 2 hours later than the 0.25° data. The 0.25° fields arrive at the end of the normal dissemination schedule; the finer 0.1° surface and pressure-level files follow roughly 2 hours afterwards, each with their own WIS2 notification. If your workflow expects both resolutions together, plan for this gap — the 0.1° data is not late, it is scheduled later.
{% endnote %}

{% note primary %}
9 km open data is released with a short delay of two hours (09:34UTC) rather than in real time (07:34UTC). 
{% endnote %}

![IFS 0.25 deg](https://i.imgur.com/IouxtmO.png)
![IFS 0.1 deg (~9km)](https://i.imgur.com/wzcbxiX.png)

- [ECMWF open data: what's new at 0.1°](https://confluence.ecmwf.int/spaces/DAC/pages/738041087/ECMWF+open+data+what+s+new+at+0.1%C2%B0)
  - ![](https://confluence.ecmwf.int/download/attachments/738041087/resolution-0p1-vs-0p25.png?version=1&modificationDate=1792488372944&api=v2)

# ECMWF open data: bits per value

- <https://confluence.ecmwf.int/spaces/DAC/pages/738041093/ECMWF+open+data+bits+per+value>
  - What "bits per value" means
    - **Every value, at every grid point, is stored in a fixed number of bits**. More bits means finer steps between the values that can be represented — and larger files. Fewer bits means coarser steps and smaller files. The question is never "more or fewer" in the abstract; it is how fine each field actually needs to be.

{% note primary %}
ECMWF open data is packed to travel: the number of bits used to store each value is set per parameter, **so files are smaller** without losing the information people use. **From 20 October 2026**, every field is set by the precision it actually needs, rather than inherited from the production and archive settings. The same per-parameter packing applies to all open data — 0.25° and 0.1°, IFS and AIFS.
{% endnote %}

# ECMWF open data: real-time forecasts from IFS and AIFS

- <https://confluence.ecmwf.int/spaces/DAC/pages/272310539/ECMWF+open+data+real-time+forecasts+from+IFS+and+AIFS>

# Grib Files

{% fold info @20261021000000-0h-oper-fc-sfc.grib2 %}
```
20261021000000-0h-oper-fc-sfc.grib2
edition      centre       date         dataType     gridType     typeOfLevel  level        stepRange    shortName    packingType  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   soilLayer    0            0            vsw          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   heightAboveGround  2            0            2t           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   entireAtmosphere  0            0            tcolr        grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   meanSea      0            0            msl          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            sp           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   soilLayer    1            0            vsw          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   heightAboveGround  2            0            2d           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   mostUnstableParcel  0            0            mucape       grid_ccsds  
2            ecmf         20261021     fc           regular_ll   soilLayer    2            0            sot          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            ssrd         grid_ccsds  
2            ecmf         20261021     fc           regular_ll   entireAtmosphere  0            0            tcw          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   soilLayer    2            0            vsw          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   heightAboveGround  100          0            100u         grid_ccsds  
2            ecmf         20261021     fc           regular_ll   soilLayer    1            0            sot          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   entireAtmosphere  0            0            tcwv         grid_ccsds  
2            ecmf         20261021     fc           regular_ll   soilLayer    3            0            vsw          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            lsm          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   heightAboveGround  10           0            gust         grid_ccsds  
2            ecmf         20261021     fc           regular_ll   soilLayer    0            0            sot          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   heightAboveGround  100          0            100v         grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   entireAtmosphere  0            0            tclw         grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            ro           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            skt          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            tp           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   entireAtmosphere  0            0            tciw         grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            strd         grid_ccsds  
2            ecmf         20261021     fc           regular_ll   soilLayer    3            0            sot          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   heightAboveGround  2            0-3          mx2t3        grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            ssr          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            sdsgso       grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            ptype        grid_ccsds  
2            ecmf         20261021     fc           regular_ll   heightAboveGround  2            0-3          mn2t3        grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            str          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            tprate       grid_ccsds  
2            ecmf         20261021     fc           regular_ll   nominalTop   0            0            ttr          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   entireAtmosphere  0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            rsn          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   heightAboveGround  10           0            10u          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   heightAboveGround  10           0            10v          grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            sd           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   surface      0            0            unknown      grid_ccsds  
54 of 54 messages in 20261021000000-0h-oper-fc-sfc.grib2

54 of 54 total messages in 1 files
```
{% endfold %}

{% fold info @20261021000000-0h-oper-fc-pl.grib2 %}
```
20261021000000-0h-oper-fc-pl.grib2
edition      centre       date         dataType     gridType     stepRange    typeOfLevel  level        shortName    packingType  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  50           z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  850          z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  1000         z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  250          z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  600          z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  700          z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  200          z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  10           gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  150          gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  300          gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  500          gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  925          z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  100          gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  400          gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  10           z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  150          z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  850          gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  925          gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  50           gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  150          t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  100          z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  400          z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  500          z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  300          z            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  150          q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  600          gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  250          gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  200          gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  1000         gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  700          gh           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  925          t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  850          t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  10           t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  400          t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  10           q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  500          t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  600          t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  925          q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  850          q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  50           t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  400          q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  100          t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  500          q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  100          q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  600          q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  50           q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  300          t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  150          w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  10           w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  1000         t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  700          t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  250          t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  200          t            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  300          q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  200          q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  250          q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  700          q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  1000         q            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  925          w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  850          w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  400          w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  300          w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  600          w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  500          w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  50           w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  200          w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  100          w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  150          vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  850          vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  10           vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  400          vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  250          w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  700          w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  1000         w            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  925          vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  10           d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  600          vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  100          vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  300          vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  500          vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  50           vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  200          vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  150          d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  850          d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  400          d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  250          vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  700          vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  1000         vo           grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  100          d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  300          d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  500          d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  925          d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  850          r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  10           r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  600          d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  50           d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  200          d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  150          r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  925          r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  400          r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  250          d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  700          d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  1000         d            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  100          r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  300          r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  10           v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  500          r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  10           u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  600          r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  50           r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  200          r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  850          v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  100          u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  850          u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  100          v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  925          v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  150          u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  250          r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  925          u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  700          r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  1000         r            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  150          v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  400          v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  600          u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  200          u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  400          u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  600          v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  200          v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  300          v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  500          u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  300          u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  500          v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  700          u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  1000         v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  700          v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  50           v            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  1000         u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  50           u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  250          u            grid_ccsds  
2            ecmf         20261021     fc           regular_ll   0            isobaricInhPa  250          v            grid_ccsds  
140 of 140 messages in 20261021000000-0h-oper-fc-pl.grib2

140 of 140 total messages in 1 files
```
{% endfold %}