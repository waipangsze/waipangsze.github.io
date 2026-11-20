---
layout: post
title: NWP | HKO | OCF Objective Consensus Forecast | 客觀整合預報
categories: NWP
tags: [NWP, HKO, WRF, MPAS, CMA, CMA_GFS, CMA_MESO]
author: wpsze
date: 2026-10-08 06:15:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/g1Xo2sI.jpeg
banner_img: https://i.imgur.com/g1Xo2sI.jpeg
---

# 香港分區天氣資訊平台

{% gi 5 3-2 %}
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

- It will update hourly, replacing past hours with observed data.
  - Remark: Please Avoding for Overwrite !!!
- crontab with `00HKT` and `12HKT`

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
    "SHL", "SF", "TMT", "TO", "TPK", "TUN", "YTS", "TME",
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
      file_timestamp = f"{yyyymmdd}_{hh}"
    elif len(ref_time) >= 8:
      yyyy, yyyymm, yyyymmdd = ref_time[:4], ref_time[:6], ref_time[:8]
      date_subpath = os.path.join(yyyy, yyyymm, yyyymmdd)
      file_timestamp = yyyymmdd
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