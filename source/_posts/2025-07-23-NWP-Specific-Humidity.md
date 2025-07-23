---
layout: post
title: NWP | Specific Humidity
categories: [NWP]
tags: [NWP, MAPS, WRF]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-07-23 17:03:00
index_img: https://i.imgur.com/N0hv8M5.png
banner_img: https://i.imgur.com/N0hv8M5.png
---

- [**Relative Humidity and the Dewpoint Temperature**](https://waipangsze.github.io/2025/03/06/Relative-Humidity-and-the-Dewpoint-Temperature/)
- [**NWP | Why is RH preferred over mixing ratio for initial moisture in NWP models**](https://waipangsze.github.io/2025/06/05/NWP-Why-is-RH-preferred-over-mixing-ratio-for-initial-moisture-in-NWP-models/)
- [**Tephigram | 溫熵圖**](https://waipangsze.github.io/2025/05/31/Tephigram/)

---

To convert relative humidity (RH) to specific humidity (q), you need the following parameters:
- **Relative humidity** (%)
- **Air temperature** (in °C or K)
- **Atmospheric pressure** (in hPa or Pa)

The formula for specific humidity is:

$$q = \frac{0.622 \cdot e}{P - (0.378 \cdot e)} $$

Where:

- $e$ is the actual vapor pressure
- $P$ is the atmospheric pressure
- 0.622 and 0.378 are constants related to the gas constants for dry air and water vapor

To calculate $e$, you first need the saturation vapor pressure ($e_s$) and then use relative humidity:

$$e = \frac{\text{RH} \cdot e_s}{100} $$

The saturation vapor pressure ($e_s$) can be approximated using the Tetens formula (for temperatures over water):

$$e_s = 6.1078 \cdot 10^{\frac{7.5T}{T + 237.3}} \text{ (in hPa, if T is in °C)} $$

### Step-by-Step Process
1. **Input Parameters**:
   - Relative humidity (RH, in %)
   - Temperature (T, in °C or K)
   - Pressure (P, in hPa or Pa)

2. **Calculate Saturation Vapor Pressure ($e_s$)**:
   - If T is in °C, use the Tetens formula: $e_s = 6.1078 \cdot 10^{\frac{7.5T}{T + 237.3}}$
   - If pressure is in Pa, convert $e_s$to Pa by multiplying by 100.

3. **Calculate Actual Vapor Pressure ($e$)**:
   - $e = \frac{\text{RH} \cdot e_s}{100}$

4. **Calculate Specific Humidity ($q$)**:
   - Ensure pressure ($P$) is in the same units as $e$(hPa or Pa).
   - Use: $q = \frac{0.622 \cdot e}{P - (0.378 \cdot e)}$
   - The result $q$is in kg/kg (dimensionless, mass of water vapor per mass of air).

### Example Calculation

Suppose:

- RH = 60%
- T = 25°C
- P = 1013.25 hPa

1. **Saturation Vapor Pressure**:
   $$e_s = 6.1078 \cdot 10^{\frac{7.5 \cdot 25}{25 + 237.3}} $$
   $$e_s = 6.1078 \cdot 10^{\frac{187.5}{262.3}} \approx 6.1078 \cdot 10^{0.7148} \approx 6.1078 \cdot 5.189 \approx 31.67 \, \text{hPa} $$

2. **Actual Vapor Pressure**:
   $$e = \frac{60 \cdot 31.67}{100} \approx 19.00 \, \text{hPa} $$

3. **Specific Humidity**:
   $$q = \frac{0.622 \cdot 19.00}{1013.25 - (0.378 \cdot 19.00)} $$
   $$q = \frac{11.818}{1013.25 - 7.182} \approx \frac{11.818}{1006.068} \approx 0.01175 \, \text{kg/kg} $$

So, the specific humidity is approximately **11.75 g/kg** (or 0.01175 kg/kg).

### Notes

- Ensure consistent units (e.g., convert pressure to Pa if needed: 1 hPa = 100 Pa).

# References

1. [3.1 Ways to Specify Water Vapor | METEO 300 - Dutton Institute](https://www.e-education.psu.edu/meteo300/node/519)