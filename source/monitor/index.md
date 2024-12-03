---
layout: page
title: Real-Time Monitoring
mathjax: true
author: wpsze
date: 2024-10-06 12:55:11
---

# 北極濤動/北極震盪/Arctic Oscillation（AO)

[北極濤動（AO）](https://www.weather.gov.hk/tc/climate_change/ao.htm) (又稱北極震盪) 是指北半球中緯度地區（約北緯45度）與北極地區氣壓形勢差別的變化。它是一個代表北極地區大氣環流的重要氣候指數，可分為正位相和負位相。

北極通常受低氣壓系統支配，而高氣壓系統則位於中緯度地區。

- 當AO處於正位相時，**這些系統的氣壓差較正常強**，限制了極區冷空氣向南擴展；
- 當AO處於負位相時，**這些系統的氣壓差較正常弱**，冷空氣較易向南侵襲。

有關北極濤動的最新情況可參考：
- <https://www.cpc.ncep.noaa.gov/products/precip/CWlink/daily_ao_index/ao.shtml>

![The daily AO index and its forecasts using GFS and Ensemble mean forecast data are shown for the previous 120 days as indicated. Each daily value has been standardized by the standard deviation of the monthly AO index from 1979-2000.](https://www.cpc.ncep.noaa.gov/products/precip/CWlink/daily_ao_index/ao.gefs.fcst.png)

# 南方濤動振盪指標/Southern Oscillation Index

- <https://www.longpaddock.qld.gov.au/soi/>
- The Southern Oscillation Index or SOI is a standardised index of the barometric pressures over Darwin, Australia and Tahiti. Climate scientists use the SOI to assess the **strength of the El Niño Southern Oscillation phenomenon (or ENSO)**, which in Queensland accounts for nearly 25 per cent of our year-to-year rainfall variability.
- Note: The SOI values are expressed as an index (i.e. no units) and are derived from the 1887–1989 base period. This information is updated daily. The new data are typically available by 7:00pm (AEST).
- SOI指標是**南方濤動振盪指標(Southern Oscillation Index)**的縮寫，用於衡量太平洋區域大氣和海洋之間的交互作用。該指標主要是通過比較遠東太平洋地區的氣壓和澳大利亞/印尼地區的氣壓來計算。
- SOI指標的變化可以提供關於聖嬰和反聖嬰現象的信息，這些現象對全球氣候和天氣模式產生重大影響。當SOI指數持續高於 7 時，通常表示反聖嬰現象。
- [厄爾尼諾與香港的降水 | HKO ](https://www.hko.gov.hk/hko/publica/reprint/r284.pdf)
- [聖嬰現象(El Niño)來了沒？動手算算看！](http://www.geostory.tw/enso-elnino-math-soi/)

![](https://data.longpaddock.qld.gov.au/SeasonalClimateOutlook/SouthernOscillationIndex/SOIGraph/2024.gif){width=600}


# AI for NWP

- ECMWF | [Scores of forecasts of upper-air parameters by experimental machine learning models](https://charts.ecmwf.int/products/plwww_3m_fc_aimodels_wp_mean?area=Northern%20Extra-tropics&parameter=Geopotential%20500hPa&score=Root%20mean%20square%20error)

<!-- {% gi 4 2-2 %}
![1](https://charts.ecmwf.int/streaming/20241009-0750/81/ps2png-worker-commands-76f744c54b-4xnd6-6fe5cac1a363ec1525f54343b6cc9fd8-zisedq81.png)
![2](https://charts.ecmwf.int/streaming/20241009-0710/e3/ps2png-worker-commands-76f744c54b-rk8tn-6fe5cac1a363ec1525f54343b6cc9fd8-ue79gh2y.png)
![3](https://charts.ecmwf.int/streaming/20241009-0820/27/ps2png-worker-commands-76f744c54b-sgsp8-6fe5cac1a363ec1525f54343b6cc9fd8-ccvm8iij.png)
![4](https://charts.ecmwf.int/streaming/20241009-0130/fb/ps2png-worker-commands-76f744c54b-r2bdp-6fe5cac1a363ec1525f54343b6cc9fd8-o3sdzb7o.png)
{% endgi %} -->