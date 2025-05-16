---
layout: post
title: NWP | Pressure Altitude Calculator
categories: [NWP]
tags: [MPAS, NWP, WRF, PGW, ERA5, GFS, GRIB]
author: wpsze
date: 2025-02-07 10:52:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/KhpfeBi.png
banner_img: https://i.imgur.com/KhpfeBi.png
---

# Pressure Altitude Calculator

Pressure Altitude

- [Pressure Altitude Calculator | NOAA](https://www.weather.gov/epz/wxcalc_pressurealtitude)
- [What is the formula for the pressure altitude script?](https://www.weather.gov/epz/wxcalc_pressurealtitude)

From the user, a station pressure ($P_{sta}$) is given. To calculate the pressure altitude, the station pressure must be in units of millibars (mb). To see how to convert station pressure to millibars see the link below:
Pressure Conversion
Then, the pressure altitude ($h_{alt}$) can be calculated using the equation below:

$$
h_{alt} = \left(1 - \left(\frac{P_{sta}}{1013.25}\right)^{0.190284}\right) × 145366.45
$$

The answer will be in units of feet. To convert the answer to units of meters, see the equation below:

$$
hm = 0.3048 × h_{alt}
$$


# 常用的標準大氣內主要物理參數 (氣壓, 海拔高度)

| 氣壓 (hPa) | 飛行空層 (Flight level) | 溫度 (°C) | 空氣密度 (kg/m³) | 平均海拔高度 (呎) | 平均海拔高度 (米) |
|:----------:|:----------------------:|:---------:|:----------------:|:------------------:|:------------------:|
| 1013       |                        | 15        | 1.225            | 平均海平面         | 平均海平面         |
| 1000       |                        | 14        | 1.212            | 364                | 110                |
| 950        |                        | 12        | 1.163            | 1773               | 540                |
| 900        |                        | 9         | 1.113            | 3243               | 988                |
| 850        |                        | 6         | 1.063            | 4781               | 1457               |
| 800        |                        | 2         | 1.012            | 6394               | 1949               |
| 750        |                        | -1        | 0.960            | 8091               | 2466               |
| 700        | FL100                  | -5        | 0.908            | 9882               | 3012               |
| 650        |                        | -8        | 0.855            | 11780              | 3591               |
| 600        | FL140                  | -12       | 0.802            | 13801              | 4207               |
| 550        |                        | -17       | 0.747            | 15962              | 4866               |
| 500        | FL185                  | -21       | 0.692            | 18289              | 5575               |
| 450        |                        | -26       | 0.635            | 20812              | 6345               |
| 400        | FL235                  | -32       | 0.577            | 23574              | 7187               |
| 350        |                        | -38       | 0.518            | 26631              | 8119               |
| 300        | FL300                  | -45       | 0.467            | 30065              | 9166               |
| 250        | FL340                  | -51       | 0.395            | 33999              | 10365              |
| 200        | FL385                  | -55       | 0.322            | 38936              | 11787              |
| 150        | FL445                  | -57       | 0.241            | 44647              | 13611              |
| 100        |                        | -57       | 0.161            | 53083              | 16183              |
