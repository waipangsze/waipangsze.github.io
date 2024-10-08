---
layout: post
title: Approximate Metric Equivalents for Degrees, Minutes, and Seconds
categories: [NWP]
tags: [WRF, MPAS]
author: wpsze
index_img: 
banner_img: 
---

# Approximate Metric Equivalents for Degrees, Minutes, and Seconds

At the equator for longitude and for latitude anywhere, the following approximations are valid:

```sh
1° = 111 km  (or 60 nautical miles)
0.1° = 11.1 km
0.01° = 1.11 km (2 decimals, km accuracy)
0.001° =111 m
0.0001° = 11.1 m
0.00001° = 1.11 m
0.000001° = 0.11 m (7 decimals, cm accuracy)

1' = 1.85 km  (or 1 nautical mile)
0.1' = 185 m
0.01' = 18.5 m
0.001' = 1.85 m

30" = 900 m
15" = 450 m
3" = 90 m
1" = 30 m
1/3" = 10 m
0.1" = 3 m
1/9" = 3 m
1/27" = 1 m
```
If you report a position to the nearest 0.01°  (two decimal places for latitude and longitude), you will only be accurate to around 1 km.  While this is adequate for rough locations on a global scale, for detailed work it will be inadequate.

# Latitude and longitude 
Lines of latitude and longitude form the grid system used on globes, maps and charts. Latitude is a measure of how far north or south somewhere is from the Equator; longitude is a measure of how far east or west it is from the Prime Meridian. Whilst lines (or parallels) of latitude all run parallel to the Equator, lines (or meridians) of longitude all converge at the Earth’s North and South Poles. The north–south line passing through any particular point on the Earth’s surface is known as the “local meridian”.

Although the Equator is the obvious zero point from which to measure latitude, there is no equivalent point from which to measure longitude. In 1884, an international conference decided that the Greenwich Meridian, as defined by the Airy Transit Circle at the Royal Observatory, Greenwich, should be adopted as the Prime or Zero Meridian for the World.

Latitude and longitude are both measured in degrees. **Each degree (°) is subdivided into 60 minutes (') and each minute into 60 seconds (")**. Each degree of latitude corresponds to a distance on the Earth‘s surface of about 111 km. Each degree of longitude however, corresponds to a distance that varies with latitude. The distance is about 111 km at the Equator, reducing to 0 km at the Poles.

# The prime meridian
The prime meridian forms the reference point by which longitudes are defined and expressed. This line makes it possible to measure the position of a location on Earth based on the longitude.

On Earth, the prime meridian -- which, like all other latitude and longitude lines, is also imaginary -- passes through Greenwich, England. For this reason, it also called the Greenwich meridian. Thus, the prime meridian is the imaginary north-south line that passes through the north and south poles, as well as Greenwich. It has a longitude of 0 degrees. The antemeridian, which is the opposite of the prime meridian, is located on the other side of the Earth and has a longitude of 180 degrees.

Longitudes and the prime meridian are both used to define time zones. **Greenwich Mean Time (GMT)**, also known as Coordinated Universal Time, is the standard time by which all other time zones are expressed -- for example:

GMT + 0 = Time in Greenwich, U.K.
GMT + 8 = China Standard Time
GMT - 8 = Pacific Standard Time

# Coordinated Universal Time or UTC
Coordinated Universal Time or UTC is the primary time standard by which the world regulates clocks and time. It is within about one second of mean solar time (such as UT1) at 0° longitude (at the IERS Reference Meridian as the currently used prime meridian) and is not adjusted for daylight saving time. It is effectively a successor to Greenwich Mean Time (GMT).

世界協調時間是世界上調節時鐘和時間的主要時間標準，它與0度經線的平太陽時相差不超過1秒，並不遵守夏令時。世界協調時間是最接近格林威治標準時間（GMT）的幾個替代時間系統之一。對於大多數用途來說，UTC時間被認為能與GMT時間互換，但GMT時間已不再被科學界所確定。

