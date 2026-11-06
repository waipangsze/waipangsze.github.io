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

- <https://data.ecmwf.int/forecasts/>

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


{% fold info @ecmwf_realtime_download.sh %}
```
#!/bin/bash

#set -e # stop the shell on first error
#set -u # fail when using an undefined variable
#set -x # echo script lines as they are executed
#set -euo pipefail
set -o pipefail

source /EM/icare/opt/micromamba/bin/activate venv

#################################################################
# https://data.ecmwf.int/forecasts/20241117/00z/ifs/0p1/oper/
# https://data.ecmwf.int/forecasts/20241117/00z/ifs/0p1/oper/20241117000000-114h-oper-fc-sfc/pl.grib2
#################################################################
# ./ecmwf_realtime_download.sh [days_ago] [cycle_hour] [max_forecast_hour]
# ./ecmwf_realtime_download.sh 0 00 0 # single file
# ./ecmwf_realtime_download.sh 0 12 0 # single file
# ./ecmwf_realtime_download.sh 0 00 3
# ./ecmwf_realtime_download.sh 1 00 0 # single file
# ./ecmwf_realtime_download.sh 1 12 3
#################################################################

# ------------------------------------------------------------------------------
# Configuration & Arguments
# ------------------------------------------------------------------------------
DAYS_AGO="${1:-0}"
HH="${2:-00}"             # 00, 06, 12 , 18
MAX_FCST="${3:-0}"

# BASE_URL="https://data.ecmwf.int/forecasts"
BASE_URL="https://storage.googleapis.com/ecmwf-open-data"

#yyyy='2026'
#mm='08'
#dd='09'

# Calculate date variables based on days ago
YYYY=$(date -d "${DAYS_AGO} day ago" +%Y)
MM=$(date -d "${DAYS_AGO} day ago" +%m)
DD=$(date -d "${DAYS_AGO} day ago" +%d)
YYYYMMDD="${YYYY}${MM}${DD}"
YYYYMMDDHH="${YYYYMMDD}${HH}"

# Create and enter output directory safely
WORK_DIR="${YYYY}${MM}/${YYYYMMDD}"
mkdir -p "${WORK_DIR}"
cd "${WORK_DIR}"

# ------------------------------------------------------------------------------
# Logging Header
# ------------------------------------------------------------------------------
echo "=================================================="
echo " ECMWF Open Data Realtime Downloader"
echo "=================================================="
echo " Target Model Run : ${YYYY}-${MM}-${DD} ${HH}:00 UTC"
echo " Max Forecast Hour: ${MAX_FCST}h (Step: 3h)"
echo " Output Directory : ${PWD}"
echo " Download Time    : $(date '+%Y-%m-%d %H:%M:%S %Z')"
echo "=================================================="
echo ""

# ------------------------------------------------------------------------------
# Helper Functions
# ------------------------------------------------------------------------------

# Formats an integer to 3 digits (e.g., 3 -> 003)
pad_hour() {
    printf "%03d" "$1"
}

# Downloads a single ECMWF file directly into WORK_DIR
# Usage: download_ecmwf_file <remote_filename> <target_filename>
download_ecmwf_file() {
    local remote_filename="$1"
    local target_filename="$2"
    local download_url="${BASE_URL}/${YYYYMMDD}/${HH}z/ifs/0p1/oper/${remote_filename}"

    if [[ -f "./${target_filename}" ]]; then
        echo "  [SKIP] ${target_filename} already exists."
        return 0
    fi

    echo "  [DOWNLOADING] ${download_url} -> ${target_filename}"
    
    if wget --no-check-certificate -q --show-progress -c "${download_url}" -O "./${target_filename}"; then
        echo "  [SUCCESS] Saved ${target_filename}"
    else
        echo "  [ERROR] Failed to download ${remote_filename}" >&2
        rm -f "./${target_filename}" # Remove partial file on failed download
    fi
}

# ------------------------------------------------------------------------------
# Main Download Loop
# ------------------------------------------------------------------------------
# Generate forecast step sequence: if MAX_FCST is 0, only run for 0
if [[ "${MAX_FCST}" -eq 0 ]]; then
    STEPS=(0)
else
    STEPS=($(seq 0 3 "${MAX_FCST}"))
fi

for fcst_hour in "${STEPS[@]}"; do
    padded_fcst=$(pad_hour "${fcst_hour}")
    echo "--- Processing Forecast Step: +${padded_fcst}h ---"

    for type in sfc pl; do
        remote_file="${YYYYMMDDHH}0000-${fcst_hour}h-oper-fc-${type}.grib2"
        target_file="${YYYYMMDDHH}0000-${padded_fcst}h-oper-fc-${type}.grib2"
        download_ecmwf_file "${remote_file}" "${target_file}"
    done

    echo ""
done

echo "Done!"
```
{% endfold %}

{% fold info @historical_ifs.sh %}
```
#!/bin/bash

#################################################################
# https://data.ecmwf.int/forecasts/20241117/00z/ifs/0p25/oper/
# https://data.ecmwf.int/forecasts/20241117/00z/ifs/0p25/oper/20241117000000-114h-oper-fc-{sfc/pl}.grib2
#################################################################
#
# Usage:
#   ./historical_ifs.sh <yyyymmdd> [cycle_hour] [max_forecast_hour]
#
# Examples:
#   ./historical_ifs.sh 20260801 00 0     # Single 000h forecast step
#   ./historical_ifs.sh 20260801 12 72    # 000h to 072h steps (3h interval)
#################################################################

# ------------------------------------------------------------------------------
# Input Validation & Arguments
# ------------------------------------------------------------------------------
DATE_IN="${1:-}"
HH="${2:-00}"
MAX_FCST="${3:-0}"

if [[ ${#DATE_IN} -ne 8 ]]; then
    echo "Error: Please provide a valid date in YYYYMMDD format (e.g., 20260801)." >&2
    echo "Usage: $0 <yyyymmdd> [cycle_hour] [max_forecast_hour]" >&2
    exit 1
fi

YYYY="${DATE_IN:0:4}"
MM="${DATE_IN:4:2}"
DD="${DATE_IN:6:2}"
YYYYMMDD="${YYYY}${MM}${DD}"
YYYYMMDDHH="${YYYYMMDD}${HH}"

# Verify valid calendar date
if ! date -d "${YYYYMMDD}" >/dev/null 2>&1; then
    echo "Error: Invalid date '${YYYYMMDD}'." >&2
    exit 1
fi

# BASE_URL="https://data.ecmwf.int/forecasts"
BASE_URL="https://storage.googleapis.com/ecmwf-open-data"

# Create and enter output directory safely
WORK_DIR="${YYYY}${MM}/${YYYYMMDD}"
mkdir -p "${WORK_DIR}"
cd "${WORK_DIR}"

# ------------------------------------------------------------------------------
# Logging Header
# ------------------------------------------------------------------------------
echo "=================================================="
echo " ECMWF Open Data Historical Downloader"
echo "=================================================="
echo " Target Model Run : ${YYYY}-${MM}-${DD} ${HH}:00 UTC"
echo " Max Forecast Hour: ${MAX_FCST}h (Step: 3h)"
echo " Output Directory : ${PWD}"
echo " Execution Time   : $(date '+%Y-%m-%d %H:%M:%S %Z')"
echo "=================================================="
echo ""

# ------------------------------------------------------------------------------
# Helper Functions
# ------------------------------------------------------------------------------

# Formats an integer to 3 digits (e.g., 3 -> 003)
pad_hour() {
    printf "%03d" "$1"
}

# Downloads a single ECMWF file directly into WORK_DIR
# Usage: download_ecmwf_file <remote_filename> <target_filename>
download_ecmwf_file() {
    local remote_filename="$1"
    local target_filename="$2"
    local download_url="${BASE_URL}/${YYYYMMDD}/${HH}z/ifs/0p1/oper/${remote_filename}"

    if [[ -f "./${target_filename}" ]]; then
        echo "  [SKIP] ${target_filename} already exists."
        return 0
    fi

    echo "  [DOWNLOADING] ${download_url} -> ${target_filename}"
    
    if wget --no-check-certificate -q --show-progress -c "${download_url}" -O "./${target_filename}"; then
        echo "  [SUCCESS] Saved ${target_filename}"
    else
        echo "  [ERROR] Failed to download ${remote_filename}" >&2
        rm -f "./${target_filename}" # Remove partial file on failure
    fi
}

# ------------------------------------------------------------------------------
# Main Download Loop
# ------------------------------------------------------------------------------
if [[ "${MAX_FCST}" -eq 0 ]]; then
    STEPS=(0)
else
    STEPS=($(seq 0 3 "${MAX_FCST}"))
fi

for fcst_hour in "${STEPS[@]}"; do
    padded_fcst=$(pad_hour "${fcst_hour}")
    echo "--- Processing Forecast Step: +${padded_fcst}h ---"

    for type in sfc pl; do
        remote_file="${YYYYMMDDHH}0000-${fcst_hour}h-oper-fc-${type}.grib2"
        target_file="${YYYYMMDDHH}0000-${padded_fcst}h-oper-fc-${type}.grib2"
        download_ecmwf_file "${remote_file}" "${target_file}"
    done

    echo ""
done

echo "Done!"
```
{% endfold %}
