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

- [按天氣情境修正數值預報模式的低溫預報 | 2016 | HKO](https://www.hko.gov.hk/en/publica/reprint/files/r1226.pdf#2#2#2)
  - 該系統先利用實況觀測數據，以**卡爾曼濾波**後處理技巧對多個全球預報模式及天文台中尺度模式的直接輸出作出修正，再按照各模式在**過去三十天的表現訂定其比重**，製成集成預報。目前，OCF 的結果已成為預報員制定每日氣溫預測的主要客觀參考數據之一。
  - 雖然卡爾曼濾波能夠因應模式誤差在過去一段時間的變化而調節其修正幅度，**但在天氣情境急速變化之際，或未能及時掌握模式誤差可能出現的轉變**。

### 主觀預報  (可能)

主觀預報（Subjective Forecast）

在天文預報語境中，「主觀預報」通常涉及兩個層面：

1. 預報員的專業研判與修訂：自動化模式（如 OCF）提供客觀參考，但最終對外發布的官方預報仍仰賴預報員結合經驗、最新觀測、複雜天氣演變與即時天氣變化（如雷雨調整）進行綜合判斷與修訂。
2. 公眾主觀感受與「準確／可接受」門檻：天文台在評估預報表現時，除客觀計分外，也會考量市民的主觀觀感與滿意度，並曾設定「準確／可接受」的評分門檻（例如歷史檢討中曾以 75 分作為衡量門檻之一）來反映大眾對預報是否實用的感受。

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

    # Extract top-level metadata fields
    metadata_lines = [
        f"# LastModified: {data.get('LastModified', '')}",
        f"# StationCode: {data.get('StationCode', station)}",
        f"# Latitude: {data.get('Latitude', '')}",
        f"# Longitude: {data.get('Longitude', '')}",
        f"# ModelTime: {data.get('ModelTime', '')}",
    ]

    # Shared timestamp prefix derived from first HourlyForecast or ModelTime
    hourly_forecasts = data.get("HourlyWeatherForecast", [])
    daily_forecasts = data.get("DailyForecast", [])

    ref_time = ""
    if hourly_forecasts and hourly_forecasts[0].get("ForecastHour"):
      ref_time = str(hourly_forecasts[0]["ForecastHour"])
    elif data.get("ModelTime"):
      ref_time = "".join(filter(str.isdigit, str(data["ModelTime"])))

    if len(ref_time) >= 10:
      yyyy, yyyymm, yyyymmdd, hh = (
          ref_time[:4],
          ref_time[:6],
          ref_time[:8],
          ref_time[8:10],
      )
      date_subpath = os.path.join(yyyy, yyyymm, yyyymmdd)
      file_timestamp = f"{data.get('LastModified', '')}"
    elif len(ref_time) >= 8:
      yyyy, yyyymm, yyyymmdd = ref_time[:4], ref_time[:6], ref_time[:8]
      date_subpath = os.path.join(yyyy, yyyymm, yyyymmdd)
      file_timestamp = f"{data.get('LastModified', '')}"
    else:
      date_subpath = "unknown_date"
      file_timestamp = ref_time or "output"

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
        └── 20261119
HourlyWeatherForecast/
└── 2026
    └── 202611
        └── 20261119
```

![Some time slots may occur twice an hour.](https://i.imgur.com/yH6ttfS.png)

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
