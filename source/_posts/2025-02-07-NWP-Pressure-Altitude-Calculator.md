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