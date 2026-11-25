---
layout: post
title: NWP | HKO | OCF Objective Consensus Forecast | 客觀整合預報
categories: NWP
tags: [NWP, HKO, WRF, MPAS, CMA, CMA_GFS, CMA_MESO]
author: wpsze
date: 2026-11-20 06:15:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/g1Xo2sI.jpeg
banner_img: https://i.imgur.com/g1Xo2sI.jpeg
---

# 香港分區天氣資訊平台

{% gi 4 2-2 %}
![](https://i.imgur.com/rVkGWcK.png)
![](https://i.imgur.com/z0n41nt.png)
![](https://i.imgur.com/qyIKPrf.png)
![](https://i.imgur.com/XqlT6o9.png)
{% endgi %}

1. 產品約於**每天中午和午夜前更新**；前者顯示由當天中午至第九天午夜的逐小時預測；後者顯示由當天午夜至第九天午夜的逐小時預測。
2. 產品乃基於一個多模式集成預報系統製作而成。該預報系統綜合多個全球數值天氣模式 的輸出，先利用香港及珠江三角洲區域收集的實況觀測數據對模式預測作出修正，再按照各模式的過去表現以訂定其比重，再集合成集成預報。有關該預報系統的詳細技術資料可參閱香港天文台 [**短文第1064號**](http://www.weather.gov.hk/publica/reprint/r1064.pdf) （只提供英文版）。

Currently, outputs from five different NWP models are used in OCF, including: (**2013**)

1. Meso-NHM, (Meso-NHM is the operational limited area model of HKO)
2. ECMWF deterministic model,
3. JMA GSM,
4. NCEP GFS,
5. ECMWF Ensemble Prediction System (EPS).

- [開發公眾綜合天氣預報網站 | 2016](https://www.weather.gov.hk/en/publica/reprint/files/r1228.pdf)
  - 香港天文台於 2013年 12 月開始試行向公眾發報「自動分區天氣預報」（ARWF）網站，隨著其後不斷改進，現時網站為香港和珠三角地區超過 250 個地點提供一站式未來九天天氣預測服務。自動天氣預報結合了多個數值天氣預報（NWP）模式，以及歐洲中期天氣預報中心集合預報系統的後處理輸出。**本文將討論當中的運算方法和驗證結果**，包括每小時溫度、相對濕度、風向和風速預測，以及每天降雨概率（PoP）預測的表現。文章亦會討論為製作預測天氣圖示而研發的天色及降雨預報之近期進展；從驗證結果和比較個案實況，來展示預測天氣圖示的表現。此外，本文會介紹 ARWF 網站加入天氣圖示的改進工作。

## 主觀預報 (根據經驗及最新實況觀測數據)

在天文預報語境中，「主觀預報」通常涉及兩個層面：

1. 預報員的專業研判與修訂：自動化模式（如 OCF）提供客觀參考，但最終對外發布的官方預報仍仰賴預報員結合經驗、最新觀測、複雜天氣演變與即時天氣變化（如雷雨調整）進行綜合判斷與修訂。
2. 公眾主觀感受與「準確／可接受」門檻：天文台在評估預報表現時，除客觀計分外，也會考量市民的主觀觀感與滿意度，並曾設定「準確／可接受」的評分門檻（例如歷史檢討中曾以 75 分作為衡量門檻之一）來反映大眾對預報是否實用的感受。

- [Objective Verification of Weather Forecast in Hong Kong | 2009](https://www.hko.gov.hk/tc/publica/reprint/files/r829.pdf)
  - In order to assess the accuracy of weather forecasts provided by HKO, an objective weather forecast verification scheme for Hong Kong was developed and computerized. This paper outlines the essential features of the scheme and the methodology employed such as categorical, spatial and user-oriented verification. It also compares the accuracy of weather forecasts issued by weather forecasters with that of persistence forecasts.
- [Cheung, P., Y. Y. Leung, and S. Y. Tang. "An algorithm for generating location-specific NWP total cloud cover forecast with potential application to sea breeze forecast at the Hong Kong International Airport." (2015): 20-22.](https://www.hko.gov.hk/hko/publica/reprint/r1162.pdf)
  - he Hong Kong Observatory (HKO) has developed a site-specific multimodel consensus forecast system called the objective consensus forecast (OCF) in 2011. In 2014, the OCF is extended to include cloud cover forecast to assist forecaster’s assessment on the state of sky. A potential use of OCF cloud cover forecasts is its ingestion into the sea breeze prediction model that the HKO hasbeen using for predicting sea breeze occurrence at the Hong Kong International Airport (HKIA).
- [按天氣情境修正數值預報模式的低溫預報 | 2016 | HKO](https://www.hko.gov.hk/en/publica/reprint/files/r1226.pdf#2#2#2)
  - **秋冬兩季，華南沿岸地區不時受到東北季風潮影響。季風潮可根據它們在抵達時是否帶來降雨，以及抵達後雨勢是否持續，分為「乾潮」、「濕潮」、「濕轉乾潮」**
  - 該系統先利用實況觀測數據，以**卡爾曼濾波**後處理技巧對多個全球預報模式及天文台中尺度模式的直接輸出作出修正，再按照各模式在**過去三十天的表現訂定其比重**，製成集成預報。目前，OCF 的結果已成為預報員制定每日氣溫預測的主要客觀參考數據之一。
  - 雖然卡爾曼濾波能夠因應模式誤差在過去一段時間的變化而調節其修正幅度，**但在天氣情境急速變化之際，或未能及時掌握模式誤差可能出現的轉變**。
  - 因此，在參考「客觀集成預報」的同時，**預報員往往根據經驗及最新實況觀測數據，評估在預計會出現的天氣情境下，經後處理的數值模式預報是否需要再作調整**，從而作出氣溫預測，並向公眾發佈。
- [修订热带气旋出现时的自动网格风场预报 | 2018 | HKO](https://www.hko.gov.hk/en/publica/reprint/files/r1355.pdf)
  - 现时香港天文台应用多个数值天气预报模式的预测，透过加权的客观共识方法制作出未来九天的数码天气预报
  - 但当有热带气旋出现在西北太平洋或南海时，各预报模式对热带气旋的预报位置和强度时有差异，**往往导致风场预报和天文台的主观预报出现不一致**的情况
  - 这两种方法分别为「制造气旋风场」和「移动模式风场」。
    - 前者利用天文台对热带气旋强度和风力分布的预测构建一个新的风场，
    - 后者则在模式的集成预报群组中选取一个预报位置及强度与主观预报路径最接近的成员并采用其风场。
- [Development of a platform for the correction of automatic gridded weather forecasts | 2018](https://www.cuhk.edu.hk/sci/essc/docs/HKO_2018.pdf)
  - The Hong Kong Observatory delivers worded weather forecasts and automatic forecasts through a variety of dissemination channels to meet the needs of the public. For local weather, the Observatory's forecaster compiles worded 9-day weather forecasts while automatic gridded forecasts are generated from computer models directly without manual adjustment. Correction to the automatic forecasts is needed from time to time when the automatic forecasts do not agree with **the subjective forecasts issued by the forecasters**. In this project, reference will be made to the results from preceding studies on the methods and algorithms for correcting automatic gridded forecasts. The suite of algorithms and associated tools will be enhanced further taking into account the local conditions and environment. An integrated platform will also be developed for more efficient operations and access to the tools.

## 開發新一代「香港及珠江三角洲區域自動分區天氣預報」

- [人工智能天氣模型的定點自動天氣預報 | 2025 | HKO](https://www.hko.gov.hk/tc/education/weather/data-and-technology/00762-Location-specific-Automatic-Weather-Forecast-using-AI-Weather-Model.html)
  - 為探討這個問題，我們近期對AI模式，包括「歐洲中心人工智能電腦模式」（AIFS），以及「風烏」、「伏羲」和「盤古」等應用了上述模式後處理方法進行預報修正。綜合以上四個模式的後處理預報表現，初步結果顯示AI模式對未來九天香港天文台的每日最低氣溫預報的誤差，較現行OCF降低了約百分之三至十六（圖一）；而在每日最高氣溫預報方面，AI模式於未來第二至五天的預測誤差平均減少約百分之十（圖二），但之後AI模式的預測誤差在比現行OCF稍為遜色，在第八和第九天的預測誤差增加百分之十左右。
  - 基於這些發現，我們將上述AI模式的後處理預報融合至現有的OCF系統中。如圖一和圖二所示，這套 「加強版OCF」 成功綜合了NWP和AI模式預報的優勢，提供更準確的每日最高及最低氣溫預測參考依據。在集合NWP和AI多個模式預報的過程中，模式後處理會通過按照各個NWP和AI模型預測的誤差，從新調整各個模式預報在計算「加強版OCF」結果的比重，令到「加強版OCF」有效地將表現較好的NWP或 AI 模式在集合過程中突出其貢獻。從圖一和圖二可見，「加強版OCF」對未來九天的每日最低氣溫預報誤差平均減少約百分之十，同時每日最高氣溫的預報準確度也較現行OCF有所提升。

# To retrieve the hourly regional forecast data

## HKO observation data

- Temp and RH
- `wget -c https://www.hko.gov.hk/wxinfo/awsgis/hkoc.csv`

```txt
Date,Temp,RH 2026/11/19 10:20,20.6,67 2026/11/19 10:21,20.5,67 2026/11/19 10:22,20.5,68 2026/11/19 10:23,20.6,67 2026/11/19 10:24,20.6,67 2026/11/19 10:25,20.5,68 2026/11/19 10:26,20.5,67 2026/11/19 10:27,20.5,67 2026/11/19 10:28,20.5,67 2026/11/19 10:29,20.6,67 2026/11/19 10:30,20.5,69 2026/11/19 10:31,20.5,69 2026/11/19 10:32,20.5,69 2026/11/19 10:33,20.5,69 2026/11/19 10:34,20.5,69 2026/11/19
……
10:08,14,80 2026/11/20 10:09,14,80 2026/11/20 10:10,13.9,82 2026/11/20 10:11,13.9,82 2026/11/20 10:12,13.8,82 2026/11/20 10:13,13.7,82 2026/11/20 10:14,13.7,82 2026/11/20 10:15,13.7,83 2026/11/20 10:16,13.7,84 2026/11/20 10:17,13.7,84 2026/11/20 10:18,13.7,84 2026/11/20 10:19,13.6,85 2026/11/20 10:20,13.6,85
```

- Heat Index
- `wget -c https://www.hko.gov.hk/wxinfo/awsgis/hq4_hi2.csv`
  
```txt
Date,Data 2026/11/19 10:20,16.3 2026/11/19 10:21,16.3 2026/11/19 10:22,16.3 2026/11/19 10:23,16.3 2026/11/19 10:24,16.3 2026/11/19 10:25,16.3 2026/11/19 10:26,16.3 2026/11/19 10:27,16.3 2026/11/19 10:28,16.3 2026/11/19 10:29,16.3 2026/11/19 10:30,16.3 2026/11/19 10:31,16.3 2026/11/19 10:32,16.3 2026/11/19 10:33,16.3 2026/11/19 10:34,16.3 2026/11/19 10:35,16.3 2026/11/19 10:36,16.3 2026/11/19 10:37,16.3 2026/11/19 10:38,16.3 2026/11/19 10:39,16.3 2026/11/19 10:40,16.4 2026/11/19 10:41,16.4 2026/11/19 10:42,16.4 2026/11/19 10:43,16.5 2026/11/19 10:44,16.5 2026/11/19 10:45,16.6 2026/11/19 10:46,16.6 2026/11/19 10:47,16.6 2026/11/19 10:48,16.6 2026/11/19 10:49,16.6 2026/11/19 10:50,16.6 2026/11/19 10:51,16.6 2026/11/19 10:52,16.5 2026/11/19 10:53,16.5 2026/11/19 10:54,16.4 2026/11/19 10:55,16.4 2026/11/19 10:56,16.4 2026/11/19 10:57,16.3 2026/11/19 10:58,16.3 2026/11/19 10:59,16.3 2026/11/19 11:00,16.3 2026/11/19 11:01,16.2 2026/11/19 11:02,16.2 2026/11/19 11:03,16.2 2026/11/19 11:04,16.2 2026/11/19 11:05,16.3 2026/11/19 11:06,16.3 2026/11/19 11:07,16.3 2026/11/19 11:08,16.3 2026/11/19 11:09,16.3 2026/11/19 11:10,16.3 2026/11/19 11:11,16.3 2026/11/19 11:12,16.2 2026/11/19 11:13,16.2 2026/11/19 11:14,16.2 2026/11/19 11:15,16.1 2026/11/19 11:16,16.1 2026/11/19 11:17,16.1 2026/11/19 11:18,16.1 2026/11/19 11:19,16.1 2026/11/19 11:20,16.1 2026/11/19 11:21,16.2 2026/11/19 11:22,16.2
```

- Pressure
- `wget -c https://www.hko.gov.hk/wxinfo/awsgis/hkoc_pre.csv`

```txt
Date,Pressure 2026/11/19 10:20,1023.3 2026/11/19 10:21,1023.3 2026/11/19 10:22,1023.3 2026/11/19 10:23,1023.3 2026/11/19 10:24,1023.3 2026/11/19 10:25,1023.3 2026/11/19 10:26,1023.3 2026/11/19 10:27,1023.3 2026/11/19 10:28,1023.3 2026/11/19 10:29,1023.3 2026/11/19 10:30,1023.3 2026/11/19 10:31,1023.3 2026/11/19 10:32,1023.3 2026/11/19 10:33,1023.3 2026/11/19 10:34,1023.3 2026/11/19 10:35,1023.3 2026/11/19 10:36,1023.3 2026/11/19 10:37,1023.3 2026/11/19 10:38,1023.3 2026/11/19 10:39,1023.3 2026/11/19 10:40,1023.2 2026/11/19 10:41,1023.2 2026/11/19 10:42,1023.2 2026/11/19 10:43,1023.2 2026/11/19 10:44,1023.2 2026/11/19 10:45,1023.2 2026/11/19 10:46,1023.2 2026/11/19 10:47,1023.2 2026/11/19 10:48,1023.2 2026/11/19 10:49,1023.2 2026/11/19 10:50,1023.2 2026/11/19 10:51,1023.2 2026/11/19 10:52,1023.2 2026/11/19 10:53,1023.2 2026/11/19 10:54,1023.2 2026/11/19 10:55,1023.2 2026/11/19 10:56,1023.2 2026/11/19 10:57,1023.2 2026/11/19 10:58,1023.2 2026/11/19 10:59,1023.2 2026/11/19 11:00,1023.2 2026/11/19 11:01,1023.2 2026/11/19 11:02,1023.2 2026/11/19 11:03,1023.1 2026/11/19 11:04,1023.1 2026/11/19 11:05,1023 2026/11/19 11:06,1023 2026/11/19 11:07,1023 2026/11/19 11:08,1023 2026/11/19 11:09,1023 2026/11/19 11:10,1023
```

## Direct Portal Endpoints (Hourly Regional Data, Daily)

The Regional Weather Information Portal (`regional_portal.html`) loads its station-specific hourly forecasts from static files hosted under the `/wxinfo/awsgis/forecast/` directory. Despite having a `.xml` file extension, these files actually return JSON data:

- URL Pattern:<https://www.hko.gov.hk/wxinfo/awsgis/forecast/> and https://www.hko.gov.hk/wxinfo/awsgis/forecast/{STATION_CODE}.xml
- Station Codes: Replace `HKO` with the station's 3-letter AWS code (e.g., `HKA` for Chek Lap Kok / Airport, `SHA` for Sha Tin, TKL for Ta Kwu Ling, `WGL` for Waglan Island). You can find the full list of station codes by inspecting the Network tab in your browser's Developer Tools (`F12` -> `Network` -> `Fetch/XHR`) when clicking different locations on the map.
- Same as `https://maps.weather.gov.hk/ocf/index_uc.html?station=HKO`, `https://maps.weather.gov.hk/ocf/dat/HKO.xml`

### Location

#### ARWF 測站代碼摘要

以下為香港天文台**自動分區天氣預報（Automatic Regional Weather Forecast，ARWF）**系統的測站代碼，根據所提供的檔案標頭及隨附說明整理。

| 代碼 | 測站名稱 | 緯度 | 經度 | 備註 |
|------|---------|------|------|------|
| **ARWF_CCC** | 香港中文大學（大學站） | 22.4147 | 114.210 | 城市氣象監測站 |
| **ARWF_CYS** | 香港中文大學（伍宜孫書院） | 22.4224 | 114.203 | 城市氣象監測站 |
| **ARWF_HMM** | 香港海事博物館 | 22.2863 | 114.162 | 城市氣象監測站 |
| **ARWF_MKA** | 馬鞍山 | 22.3225 | 114.168 | 城市氣象監測站 |
| **ARWF_SFY** | 天星碼頭（尖沙咀） | 22.2935 | 114.169 | 城市氣象監測站 |
| **ARWF_TIT** | 大環山公園 | 22.3319 | 114.197 | 城市氣象監測站 |
| **ARWF_TWS** | 荃灣 | 22.3057 | 114.193 | 城市氣象監測站 |
| **ARWF_VIP** | 維多利亞公園 | 22.2826 | 114.188 | 城市氣象監測站 |
| **ARWF_WCC** | 保良局胡忠中學 | 22.4161 | 114.227 | 城市氣象監測站 |

##### 重點

- **系統**：所有代碼均屬香港天文台**自動分區天氣預報（ARWF）**系統，為香港各區微氣候測站提供定點逐小時天氣預報。
- **時間戳**：所示檔案均為 `LastModified: 2026112015301x`（2026年11月20日約15:30 香港時間），表示同一預報時次同步產生。
- **測站類型**：CCC、CYS、HMM、SFY、TIT、VIP、WCC 均為**城市氣象監測站**；MKA（馬鞍山）及 TWS（荃灣）為提示中已有說明的測站。
- **地理分佈**：測站由約北緯22.28°（VIP、HMM）至約北緯22.42°（CYS、WCC），涵蓋港島、九龍、新界及中大校園一帶。

### Data

- It will update hourly, replacing past hours with observed data.
  - Remark: Please Avoding for Overwrite !!!
- crontab with `10,40 * * * * cd /home/wpsze/hko_ocf && ./run.sh` or `20,50` or `25,55`

{% fold info @python %} 
```python
#!/bin/python
# -*- coding: utf-8 -*-
#------------------------------------------------#
# Author:         wpsze
# Email:          waipangsze@gmail.com
# File:           extract-hko-ocf.py
# Date:           2026-11-20 11:10:40
# Version:        0.0 
# Description:    The purpose of the script
# Copyright (C):  2026 All rights reserved
#------------------------------------------------#

import csv
import os
import time
import requests

stations = [
    "HKO", "HKA", "BR1", "BHD", "CP1", "CCH", "CPH", "CWB", "GI", "HPV", "HKP",
    "SE", "SE1", "KFB", "KAT", "KSC", "KP", "KLT", "KTG", "LAM", "LFS", "NLS",
    "TY1", "NGP", "TYW", "PEN", "EPC", "SKG", "SLW", "SHA", "SSP", "SKW", "SEK",
    "SSH", "STY", "TKL", "TLS", "PLC", "TMS", "YCT", "TAP", "TC", "VP1", "JKB",
    "TWN", "TW", "TU1", "WGL", "WLP", "HKS", "WTS", "YLP", "CCB", "NP", "SC",
    "SHL", "SF", "TMT", "TO", "TPK", "TUN", "YTS", "TME", "DF3647", "GF3640", "G137", 
    "ARWF_MKA", "ARWF_SFY", "ARWF_TWS", "ARWF_TIT", "ARWF_CCC", "ARWF_CYS", 
    "ARWF_WCC", "ARWF_HMM", "ARWF_VIP",
]

headers = {"User-Agent": "Mozilla/5.0"}

hourly_fields = [
    "ForecastHour",
    "ForecastRelativeHumidity",
    "ForecastTemperature",
    "ForecastWindDirection",
    "ForecastWindSpeed",
]

daily_fields = [
    "ForecastDate",
    "ForecastChanceOfRain",
    "ForecastDailyWeather",
    "ForecastMaximumTemperature",
    "ForecastMinimumTemperature",
]


def write_forecast_csv(filepath, metadata_lines, fields, rows):
  with open(filepath, mode="w", newline="", encoding="utf-8") as f:
    for line in metadata_lines:
      f.write(f"{line}\n")
    writer = csv.DictWriter(f, fieldnames=fields, extrasaction="ignore")
    writer.writeheader()
    writer.writerows(rows)


for station in stations:
  url = f"https://www.hko.gov.hk/wxinfo/awsgis/forecast/{station}.xml?_t={int(time.time() * 1000)}"

  try:
    response = requests.get(url, headers=headers, timeout=10)
    if response.status_code != 200:
      print(f"[{station}] HTTP {response.status_code} - Skipped")
      continue

    data = response.json()

    last_modified = str(data.get("LastModified", ""))

    # Extract top-level metadata fields
    metadata_lines = [
        f"# LastModified: {last_modified}",
        f"# StationCode: {data.get('StationCode', station)}",
        f"# Latitude: {data.get('Latitude', '')}",
        f"# Longitude: {data.get('Longitude', '')}",
        f"# ModelTime: {data.get('ModelTime', '')}",
    ]

    hourly_forecasts = data.get("HourlyWeatherForecast", [])
    daily_forecasts = data.get("DailyForecast", [])

    # Derive ref_time from LastModified (yyyymmddhhmmss)
    ref_time = "".join(filter(str.isdigit, last_modified))

    if len(ref_time) >= 10:
      yyyy = ref_time[:4]
      yyyymm = ref_time[:6]
      yyyymmdd = ref_time[:8]
      yyyymmddhh = ref_time[:10]
      date_subpath = os.path.join(yyyy, yyyymm, yyyymmdd, yyyymmddhh)
      file_timestamp = last_modified
    elif len(ref_time) >= 8:
      yyyy = ref_time[:4]
      yyyymm = ref_time[:6]
      yyyymmdd = ref_time[:8]
      date_subpath = os.path.join(yyyy, yyyymm, yyyymmdd)
      file_timestamp = last_modified
    else:
      date_subpath = "unknown_date"
      file_timestamp = last_modified or "output"

    # 1. Save HourlyWeatherForecast
    if hourly_forecasts:
      hourly_dir = os.path.join("HourlyWeatherForecast", date_subpath)
      os.makedirs(hourly_dir, exist_ok=True)
      hourly_path = os.path.join(hourly_dir, f"{station}_{file_timestamp}.csv")

      write_forecast_csv(
          hourly_path, metadata_lines, hourly_fields, hourly_forecasts
      )
      print(
          f"[{station}] Saved {len(hourly_forecasts)} hourly rows ->"
          f" {hourly_path}"
      )
    else:
      print(f"[{station}] No HourlyWeatherForecast data found.")

    # 2. Save DailyForecast
    if daily_forecasts:
      daily_dir = os.path.join("DailyForecast", date_subpath)
      os.makedirs(daily_dir, exist_ok=True)
      daily_path = os.path.join(daily_dir, f"{station}_{file_timestamp}.csv")

      write_forecast_csv(
          daily_path, metadata_lines, daily_fields, daily_forecasts
      )
      print(
          f"[{station}] Saved {len(daily_forecasts)} daily rows -> {daily_path}"
      )
    else:
      print(f"[{station}] No DailyForecast data found.")

  except requests.exceptions.JSONDecodeError:
    print(f"[{station}] Invalid JSON response (station may not have forecast).")
  except Exception as e:
    print(f"[{station}] Error: {e}")

  time.sleep(0.2)
```
{% endfold %}

save as

```
DailyForecast/
└── 2026
    └── 202611
        └── 20261125
            └── 2026112509
HourlyWeatherForecast/
└── 2026
    └── 202611
        └── 20261125
            └── 2026112509
```

![Some time slots may occur twice an hour.](https://i.imgur.com/yH6ttfS.png)

# Analysis and Plot

![](https://i.imgur.com/shBeAKf.png)
![](https://i.imgur.com/M6vHtSN.png)
![](https://i.imgur.com/j8bJQAq.png)

{% fold info @analyze_24h_runs.py %}
```python
#!/bin/python
# -*- coding: utf-8 -*-
#------------------------------------------------#
# Author:         wpsze
# Email:          waipangsze@gmail.com
# File:           analyze_24h_runs.py
# Date:           2026-11-23 16:35:33
# Version:        0.0 
# Description:    The purpose of the script
# Copyright (C):  2026 All rights reserved
#------------------------------------------------#

import glob
import os
import matplotlib.dates as mdates
import matplotlib.pyplot as plt
import pandas as pd


def load_single_forecast(filepath):
  """Reads one HKO forecast CSV, skipping '#' comments and dropping empty trailing rows."""
  meta = {}
  with open(filepath, "r", encoding="utf-8") as f:
    for line in f:
      if line.startswith("#"):
        parts = line.lstrip("#").strip().split(":", 1)
        if len(parts) == 2:
          meta[parts[0].strip()] = parts[1].strip()
      else:
        break

  df = pd.read_csv(filepath, comment="#")
  df["ForecastHour"] = pd.to_datetime(
      df["ForecastHour"].astype(str), format="%Y%m%d%H", errors="coerce"
  )

  value_cols = [
      "ForecastRelativeHumidity",
      "ForecastTemperature",
      "ForecastWindDirection",
      "ForecastWindSpeed",
  ]
  for col in value_cols:
    df[col] = pd.to_numeric(df[col], errors="coerce")

  df = df.dropna(subset=["ForecastHour"]).dropna(how="all", subset=value_cols)

  raw_ts = meta.get(
      "LastModified", os.path.basename(filepath).split("_")[-1].split(".")[0]
  )
  digits = "".join(filter(str.isdigit, str(raw_ts)))
  hh_str = digits[8:10] if len(digits) >= 10 else "00"

  group_label, _ = get_time_group(hh_str)
  df["RunTime"] = raw_ts
  df["RunHH"] = hh_str
  df["TimeGroup"] = group_label
  return df


def get_time_group(hh_str):
  """Categorizes run hour (00-23) into the 3 requested time types."""
  hh = int(hh_str)
  if hh < 11:
    return "time<11am", "royalblue"
  elif hh < 23:
    return "11am<time<11pm", "darkorange"
  else:
    return "11pm<time", "crimson"


def compute_grouped_stats(df):
  """Computes multi-run aggregation metrics for a given DataFrame."""
  return df.groupby("ForecastHour").agg(
      Run_Count=("ForecastTemperature", "count"),
      Temp_Mean=("ForecastTemperature", "mean"),
      Temp_Std=("ForecastTemperature", "std"),
      Temp_Range=("ForecastTemperature", lambda x: x.max() - x.min()),
      RH_Mean=("ForecastRelativeHumidity", "mean"),
      RH_Std=("ForecastRelativeHumidity", "std"),
      RH_Range=("ForecastRelativeHumidity", lambda x: x.max() - x.min()),
      WindSpd_Mean=("ForecastWindSpeed", "mean"),
      WindSpd_Max=("ForecastWindSpeed", "max"),
      WindSpd_Range=("ForecastWindSpeed", lambda x: x.max() - x.min()),
  )


def plot_analysis_summary(summary_csv, station, yyyymmdd):
  """Reads overall summary CSV and plots mean, spread, and run coverage."""
  df_stats = pd.read_csv(summary_csv, parse_dates=["ForecastHour"])

  fig, axes = plt.subplots(4, 1, figsize=(16, 13), sharex=True)
  fig.suptitle(
      f"{station} — Overall 24-Run Forecast Spread & Uncertainty Analysis ({yyyymmdd})",
      fontsize=14,
      fontweight="bold",
      y=0.995,
  )

  # 1. Temperature Mean ± 1 Std Dev & Min-Max Envelope
  ax0 = axes[0]
  ax0.plot(
      df_stats["ForecastHour"],
      df_stats["Temp_Mean"],
      color="firebrick",
      linewidth=2,
      label="Mean Temp (°C)",
  )
  ax0.fill_between(
      df_stats["ForecastHour"],
      df_stats["Temp_Mean"] - df_stats["Temp_Std"].fillna(0),
      df_stats["Temp_Mean"] + df_stats["Temp_Std"].fillna(0),
      color="firebrick",
      alpha=0.25,
      label="±1 Std Dev",
  )
  ax0_twin = ax0.twinx()
  ax0_twin.bar(
      df_stats["ForecastHour"],
      df_stats["Temp_Range"],
      width=0.03,
      color="gray",
      alpha=0.35,
      label="Max-Min Spread (°C)",
  )
  ax0.set_ylabel("Temp Mean (°C)", fontsize=11, fontweight="bold", color="firebrick")
  ax0_twin.set_ylabel("Temp Spread (°C)", fontsize=10, color="dimgray")
  ax0.grid(True, linestyle="--", alpha=0.4)
  lines1, labels1 = ax0.get_legend_handles_labels()
  lines2, labels2 = ax0_twin.get_legend_handles_labels()
  ax0.legend(lines1 + lines2, labels1 + labels2, loc="upper left", fontsize=9)

  # 2. Relative Humidity Mean & Max-Min Spread
  ax1 = axes[1]
  ax1.plot(
      df_stats["ForecastHour"],
      df_stats["RH_Mean"],
      color="teal",
      linewidth=2,
      label="Mean RH (%)",
  )
  ax1_twin = ax1.twinx()
  ax1_twin.bar(
      df_stats["ForecastHour"],
      df_stats["RH_Range"],
      width=0.03,
      color="gray",
      alpha=0.35,
      label="Max-Min Spread (%)",
  )
  ax1.set_ylabel("RH Mean (%)", fontsize=11, fontweight="bold", color="teal")
  ax1_twin.set_ylabel("RH Spread (%)", fontsize=10, color="dimgray")
  ax1.grid(True, linestyle="--", alpha=0.4)
  lines1, labels1 = ax1.get_legend_handles_labels()
  lines2, labels2 = ax1_twin.get_legend_handles_labels()
  ax1.legend(lines1 + lines2, labels1 + labels2, loc="upper left", fontsize=9)

  # 3. Wind Speed Mean vs Peak Across All Runs
  ax2 = axes[2]
  ax2.plot(
      df_stats["ForecastHour"],
      df_stats["WindSpd_Mean"],
      color="navy",
      linewidth=2,
      label="Mean Wind Speed (km/h)",
  )
  ax2.plot(
      df_stats["ForecastHour"],
      df_stats["WindSpd_Max"],
      color="dodgerblue",
      linestyle="--",
      linewidth=1.5,
      label="Max Wind Speed Across Runs (km/h)",
  )
  ax2.fill_between(
      df_stats["ForecastHour"],
      df_stats["WindSpd_Mean"],
      df_stats["WindSpd_Max"],
      color="dodgerblue",
      alpha=0.15,
  )
  ax2.set_ylabel("Wind Speed (km/h)", fontsize=11, fontweight="bold", color="navy")
  ax2.grid(True, linestyle="--", alpha=0.4)
  ax2.legend(loc="upper left", fontsize=9)

  # 4. Run Coverage Count
  ax3 = axes[3]
  ax3.fill_between(
      df_stats["ForecastHour"],
      0,
      df_stats["Run_Count"],
      color="purple",
      alpha=0.3,
      step="mid",
  )
  ax3.plot(
      df_stats["ForecastHour"],
      df_stats["Run_Count"],
      color="purple",
      linewidth=1.8,
      drawstyle="steps-mid",
      label="Active Model Runs Covering Hour",
  )
  ax3.set_ylabel("Run Count", fontsize=11, fontweight="bold", color="purple")
  ax3.set_ylim(0, df_stats["Run_Count"].max() + 2)
  ax3.grid(True, linestyle="--", alpha=0.4)
  ax3.legend(loc="upper right", fontsize=9)

  ax3.xaxis.set_major_locator(mdates.DayLocator(interval=1))
  ax3.xaxis.set_major_formatter(mdates.DateFormatter("%Y-%m-%d\n%H:%M"))
  ax3.xaxis.set_minor_locator(mdates.HourLocator(byhour=[6, 12, 18]))
  ax3.set_xlabel("Forecast Target Time (HKT)", fontsize=11, fontweight="bold")

  plt.tight_layout()
  analysis_fig = f"{station}_{yyyymmdd}_24h_run_analysis.png"
  plt.savefig(analysis_fig, dpi=200, bbox_inches="tight")
  print(f"Saved overall statistical analysis plot to: {analysis_fig}")
  plt.close()


def plot_three_sections_analysis(all_df, station, yyyymmdd):
  """
    Divides the 24-run statistical analysis into the 3 time sections:
    (time<11am, 11am<time<11pm, 11pm<time) and plots Mean ± 1 Std Dev & Spread across them.
    """
  sections = [
      ("time<11am", "royalblue"),
      ("11am<time<11pm", "darkorange"),
      ("11pm<time", "crimson"),
  ]

  section_stats = {}
  summary_rows = []

  print("\n=== Quantitative Comparison Across the 3 Time Sections ===")
  for sec_name, _ in sections:
    sub_df = all_df[all_df["TimeGroup"] == sec_name]
    if sub_df.empty:
      continue
    stats = compute_grouped_stats(sub_df)
    section_stats[sec_name] = stats

    n_runs = sub_df["RunHH"].nunique()
    avg_temp_spread = stats["Temp_Range"].mean()
    max_temp_spread = stats["Temp_Range"].max()
    avg_rh_spread = stats["RH_Range"].mean()
    max_rh_spread = stats["RH_Range"].max()
    avg_wind_spread = stats["WindSpd_Range"].mean()

    summary_rows.append({
        "TimeSection": sec_name,
        "RunCount": n_runs,
        "Avg_Temp_Spread_C": round(avg_temp_spread, 2),
        "Max_Temp_Spread_C": round(max_temp_spread, 2),
        "Avg_RH_Spread_Pct": round(avg_rh_spread, 2),
        "Max_RH_Spread_Pct": round(max_rh_spread, 2),
        "Avg_Wind_Spread_kmh": round(avg_wind_spread, 2),
    })

  comp_df = pd.DataFrame(summary_rows)
  print(comp_df.to_string(index=False))

  comp_csv = f"{station}_{yyyymmdd}_3sections_summary.csv"
  comp_df.to_csv(comp_csv, index=False)
  print(f"Saved 3-section comparison table to: {comp_csv}")

  # Plot 4x2 Grid: Left column = Mean ± 1 Std Dev by Section; Right column = Internal Spread (Max-Min) by Section
  fig, axes = plt.subplots(4, 2, figsize=(24, 14), sharex=True)
  fig.suptitle(
      f"{station} — 3 Time Sections Analysis ({yyyymmdd}): Mean Trajectory vs. Intra-Group Spread",
      fontsize=14,
      fontweight="bold",
      y=0.995,
  )

  for sec_name, color in sections:
    if sec_name not in section_stats:
      continue
    st = section_stats[sec_name]

    # Row 0: Temperature Mean ± Std & Range
    axes[0, 0].plot(st.index, st["Temp_Mean"], color=color, linewidth=1.8, label=sec_name)
    axes[0, 0].fill_between(
        st.index,
        st["Temp_Mean"] - st["Temp_Std"].fillna(0),
        st["Temp_Mean"] + st["Temp_Std"].fillna(0),
        color=color,
        alpha=0.15,
    )
    axes[0, 1].plot(st.index, st["Temp_Range"], color=color, linewidth=1.5, label=sec_name)

    # Row 1: Relative Humidity Mean ± Std & Range
    axes[1, 0].plot(st.index, st["RH_Mean"], color=color, linewidth=1.8, label=sec_name)
    axes[1, 0].fill_between(
        st.index,
        st["RH_Mean"] - st["RH_Std"].fillna(0),
        st["RH_Mean"] + st["RH_Std"].fillna(0),
        color=color,
        alpha=0.15,
    )
    axes[1, 1].plot(st.index, st["RH_Range"], color=color, linewidth=1.5, label=sec_name)

    # Row 2: Wind Speed Mean & Max
    axes[2, 0].plot(st.index, st["WindSpd_Mean"], color=color, linewidth=1.8, label=sec_name)
    axes[2, 1].plot(st.index, st["WindSpd_Range"], color=color, linewidth=1.5, label=sec_name)

    # Row 3: Run Count per Section
    axes[3, 0].plot(
        st.index,
        st["Run_Count"],
        color=color,
        linewidth=1.8,
        drawstyle="steps-mid",
        label=sec_name,
    )

  # Right bottom panel: Inter-Section Mean Divergence (Max Section Mean - Min Section Mean)
  if len(section_stats) >= 2:
    temp_means = pd.concat([df["Temp_Mean"] for df in section_stats.values()], axis=1)
    inter_section_temp_diff = temp_means.max(axis=1) - temp_means.min(axis=1)
    axes[3, 1].fill_between(
        inter_section_temp_diff.index, 0, inter_section_temp_diff, color="purple", alpha=0.3
    )
    axes[3, 1].plot(
        inter_section_temp_diff.index,
        inter_section_temp_diff,
        color="purple",
        linewidth=1.8,
        label="Max Diff Between 3 Sections (°C)",
    )

  # Labels and formatting
  row_titles = [
      ("Temp Mean ± 1σ (°C)", "Intra-Section Temp Spread (°C)"),
      ("RH Mean ± 1σ (%)", "Intra-Section RH Spread (%)"),
      ("Wind Speed Mean (km/h)", "Intra-Section Wind Spread (km/h)"),
      ("Active Runs in Section", "Inter-Section Temp Shift (°C)"),
  ]

  for r, (left_ylabel, right_ylabel) in enumerate(row_titles):
    axes[r, 0].set_ylabel(left_ylabel, fontsize=10.5, fontweight="bold")
    axes[r, 1].set_ylabel(right_ylabel, fontsize=10.5, fontweight="bold")
    axes[r, 0].grid(True, linestyle="--", alpha=0.4)
    axes[r, 1].grid(True, linestyle="--", alpha=0.4)

  axes[0, 0].set_title("Group Mean & ±1 Std Dev Envelope", fontsize=12, fontweight="bold")
  axes[0, 1].set_title("Run-to-Run Spread (Max - Min) Within Each Group", fontsize=12, fontweight="bold")
  axes[0, 0].legend(title="Time Section", loc="upper right", fontsize=9)
  axes[3, 1].legend(loc="upper right", fontsize=9)

  for c in range(2):
    axes[3, c].xaxis.set_major_locator(mdates.DayLocator(interval=1))
    axes[3, c].xaxis.set_major_formatter(mdates.DateFormatter("%Y-%m-%d\n%H:%M"))
    axes[3, c].xaxis.set_minor_locator(mdates.HourLocator(byhour=[6, 12, 18]))
    axes[3, c].set_xlabel("Forecast Target Time (HKT)", fontsize=11, fontweight="bold")

  plt.tight_layout()
  sec_fig = f"{station}_{yyyymmdd}_3sections_analysis.png"
  plt.savefig(sec_fig, dpi=200, bbox_inches="tight")
  print(f"Saved 3-section statistical analysis plot to: {sec_fig}")
  plt.close()


def analyze_and_plot_24h(base_dir, station, target_date):
  yyyy = target_date[:4]
  yyyymm = target_date[:6]
  yyyymmdd = target_date[:8]

  pattern = os.path.join(
      base_dir, yyyy, yyyymm, yyyymmdd, f"{yyyymmdd}*", f"{station}_*.csv"
  )
  files = sorted(glob.glob(pattern))

  if not files:
    print(f"No files found matching: {pattern}")
    return

  print(f"Found {len(files)} hourly run files for {station} on {yyyymmdd}.")

  runs = []
  for fp in files:
    df_run = load_single_forecast(fp)
    if not df_run.empty:
      runs.append(df_run)

  all_df = pd.concat(runs, ignore_index=True)

  # 1. Overall 24-Run Summary CSV & Plot
  grouped = compute_grouped_stats(all_df)
  summary_csv = f"{station}_{yyyymmdd}_24h_run_analysis.csv"
  grouped.round(2).to_csv(summary_csv)
  print(f"Saved aggregated statistics to: {summary_csv}")
  plot_analysis_summary(summary_csv, station, yyyymmdd)

  # 2. Three Time Sections Analysis & Plot
  plot_three_sections_analysis(all_df, station, yyyymmdd)

  # 3. Spaghetti Plot of All 24 Runs Colored by 3 Time Types & Labeled with 'hh'
  fig, axes = plt.subplots(4, 1, figsize=(16, 14), sharex=True)
  fig.suptitle(
      f"{station} — All {len(runs)} Hourly Forecast Runs ({yyyymmdd}) | 3 Time Groups",
      fontsize=14,
      fontweight="bold",
      y=0.995,
  )

  metrics = [
      ("ForecastTemperature", "Temperature (°C)", axes[0]),
      ("ForecastRelativeHumidity", "Relative Humidity (%)", axes[1]),
      ("ForecastWindSpeed", "Wind Speed (km/h)", axes[2]),
      ("ForecastWindDirection", "Wind Direction (°)", axes[3]),
  ]

  seen_groups = set()

  for df_run in runs:
    hh_str = df_run["RunHH"].iloc[0]
    group_label, color = get_time_group(hh_str)

    legend_label = group_label if group_label not in seen_groups else None
    seen_groups.add(group_label)

    for col, _, ax in metrics:
      if col == "ForecastWindDirection":
        ax.scatter(
            df_run["ForecastHour"],
            df_run[col],
            color=color,
            alpha=0.55,
            s=10,
            label=legend_label,
        )
      else:
        ax.plot(
            df_run["ForecastHour"],
            df_run[col],
            color=color,
            alpha=0.65,
            linewidth=1.3,
            label=legend_label,
        )

      x_start = df_run["ForecastHour"].iloc[0]
      y_start = df_run[col].iloc[0]
      x_end = df_run["ForecastHour"].iloc[-1]
      y_end = df_run[col].iloc[-1]

      ax.annotate(
          hh_str,
          xy=(x_start, y_start),
          xytext=(-4, 2),
          textcoords="offset points",
          fontsize=7.5,
          fontweight="bold",
          color=color,
          ha="right",
          va="bottom",
      )
      ax.annotate(
          hh_str,
          xy=(x_end, y_end),
          xytext=(4, 0),
          textcoords="offset points",
          fontsize=7.5,
          fontweight="bold",
          color=color,
          ha="left",
          va="center",
      )

  for col, ylabel, ax in metrics:
    ax.set_ylabel(ylabel, fontsize=11, fontweight="bold")
    ax.grid(True, linestyle="--", alpha=0.4)
    ax.margins(x=0.03)
    if col == "ForecastWindDirection":
      ax.set_ylim(0, 360)
      ax.set_yticks([0, 90, 180, 270, 360])
      ax.set_yticklabels(["N (0°)", "E (90°)", "S (180°)", "W (270°)", "N (360°)"])

  axes[0].legend(
      title="Run Time Group",
      loc="upper right",
      frameon=True,
      fontsize=10,
      title_fontsize=10,
  )

  axes[3].xaxis.set_major_locator(mdates.DayLocator(interval=1))
  axes[3].xaxis.set_major_formatter(mdates.DateFormatter("%Y-%m-%d\n%H:%M"))
  axes[3].xaxis.set_minor_locator(mdates.HourLocator(byhour=[6, 12, 18]))
  axes[3].set_xlabel("Forecast Target Time (HKT)", fontsize=11, fontweight="bold")

  plt.tight_layout()
  output_fig = f"{station}_{yyyymmdd}_all_24_runs.png"
  plt.savefig(output_fig, dpi=200, bbox_inches="tight")
  print(f"Saved combined 24-run plot to: {output_fig}")
  plt.close()


if __name__ == "__main__":
  BASE_DIR = "/EM/emsdm/hko_ocf/HourlyWeatherForecast"
  STATION = "HKO"
  TARGET_DATE = "20261124"

  analyze_and_plot_24h(BASE_DIR, STATION, TARGET_DATE)
```
{% endfold %}

## Official HKO Open Data API (Territory-Wide Forecasts)

If you need a stable, officially supported API for production applications (and do not strictly need hourly station-by-station forecasts), use HKO's official Open Data API. No API key is required.

* **Base Endpoint:** `[https://data.weather.gov.hk/weatherAPI/opendata/weather.php](https://data.weather.gov.hk/weatherAPI/opendata/weather.php)`
* **9-Day Weather Forecast (`fnd`):**
`[https://data.weather.gov.hk/weatherAPI/opendata/weather.php?dataType=fnd&lang=tc](https://data.weather.gov.hk/weatherAPI/opendata/weather.php?dataType=fnd&lang=tc)` *(use `lang=en` for English)*
* **Local Weather Forecast (`flw` - Today/Tomorrow):**
`[https://data.weather.gov.hk/weatherAPI/opendata/weather.php?dataType=flw&lang=tc](https://data.weather.gov.hk/weatherAPI/opendata/weather.php?dataType=flw&lang=tc)`
* **Current Regional Weather Report (`rhrread` - Observations):**
`[https://data.weather.gov.hk/weatherAPI/opendata/weather.php?dataType=rhrread&lang=tc](https://data.weather.gov.hk/weatherAPI/opendata/weather.php?dataType=rhrread&lang=tc)`

### **Gridded Rainfall Nowcast (Next 2 Hours):**
If you specifically want the short-term 2-hour rainfall forecast displayed at the top right of your screenshot, HKO provides a dedicated dataset on `DATA.GOV.HK` updated every 12 minutes:

* **CSV Download:** `[https://data.weather.gov.hk/weatherAPI/hko_data/F3/Gridded_rainfall_nowcast.csv](https://data.weather.gov.hk/weatherAPI/hko_data/F3/Gridded_rainfall_nowcast.csv)`
