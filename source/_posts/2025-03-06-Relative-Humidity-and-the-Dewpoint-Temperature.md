---
layout: post
title: "Relative Humidity and the Dewpoint Temperature"
categories: [Meteorology]
tags: [NWP, MPAS, WRF]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-03-06 10:43:00
index_img: https://i.imgur.com/N0hv8M5.png
banner_img: https://i.imgur.com/N0hv8M5.png
---

Key words: Relative Humidity (rh), Dewpoint Temperature (dewpoint), Water Vapor Mixing Ratio (qv)

- [Bolton, D. (1980). The computation of equivalent potential temperature. Monthly weather review, 108(7), 1046-1053.](https://journals.ametsoc.org/view/journals/mwre/108/7/1520-0493_1980_108_1046_tcoept_2_0_co_2.xml)
- [Lawrence, M. G. (2005). The relationship between relative humidity and the dewpoint temperature in moist air: A simple conversion and applications. Bulletin of the American Meteorological Society, 86(2), 225-234.](https://journals.ametsoc.org/view/journals/bams/86/2/bams-86-2-225.xml)
  - How are the dewpoint temperature and relative humidity related, and is there an easy and sufficiently accurate way to convert between them without using a calculator?

# Water vapor

**Water vapor is the gaseous state of water** ($\text{H}_2\text{O}$) existing below its boiling point, formed through vaporization or sublimation. It remains invisible unless water droplets are suspended within it (e.g., steam, fog). Its chemical formula is $\text{H}_2\text{O}_{(g)}$, with molecules existing as dimers or trimers in the vapor phase.

## Key Humidity Metrics

**1. Water Vapor Mixing Ratio**  
- **Definition**: Mass of water vapor ($m_v$) per unit mass of dry air ($m_d$):  
  $$
  w = \frac{m_v}{m_d} \quad (\text{units: g/kg or kg/kg})
  $$  
- Differs from specific humidity by <3% at typical atmospheric levels.  
- Used in meteorological diagrams (e.g., skew-T) to analyze air parcels.

**2. Absolute Humidity**  
- **Definition**: Mass of water vapor per unit volume of air:  
  $$
  \rho_v = \frac{m_v}{V} \quad (\text{units: g/m}^3\text{)}
  $$  
- Varies with temperature and pressure, making it less reliable for dynamic conditions.  
- Maximum values near 30 g/m³ in saturated tropical air.

**3. Specific Humidity**  
- **Definition**: Mass of water vapor ($m_v$) per total mass of moist air ($m_v + m_d$):  
  $$
  q = \frac{m_v}{m_v + m_d} \quad (\text{units: g/kg})
  $$  
- Remains constant during temperature/pressure changes unless moisture is added/removed.  
- Key for tracking air masses due to its stability.

**4. Relative Humidity (RH)**  
- **Definition**: Ratio of actual vapor pressure ($e$) to saturation vapor pressure ($e_s$) at air temperature:  
  $$
  \text{RH} = \frac{e}{e_s} \times 100\% 
  $$  
- Temperature-dependent: Warm air can hold more moisture, lowering RH at constant $e$.  
- At 100% RH, dew forms via condensation.

# Saturation vapor pressure (SVP)

Saturation vapor pressure (SVP) is the equilibrium pressure exerted by water vapor in air that is fully saturated (unable to hold more moisture) at a given temperature. It is a critical parameter for understanding humidity, cloud formation, and weather processes. Below are the key concepts and formulas:

---

## **Key Equations**

### 1. Clausius-Clapeyron Equation (Theoretical Basis)  
Describes the temperature dependence of SVP:  
$$
\frac{d e_s}{d T} \approx \frac{L_v \cdot e_s}{\Re_v \cdot T^2}
$$  

- $e_s$: Saturation vapor pressure  
- $L_v$: Latent heat of vaporization ($2.5 \times 10^3 \, \text{J/g}$ for water, $2.824 \times 10^3 \, \text{J/g}$ for ice)  
- $\Re_v$: Gas constant for water vapor ($461.5 \, \text{J/kg·K}$)  
- $T$: Temperature (K)  

This differential equation shows SVP increases exponentially with temperature.

### 2. Arden Buck Equation (Empirical)  
Widely used in meteorology for calculating SVP over liquid water and ice:  

**Over liquid water** ($T > 0^\circ \text{C}$):  
$$
e_s(T) = 6.1121 \exp\left(\left(18.678 - \frac{T}{234.5}\right) \cdot \frac{T}{257.14 + T}\right) \, \text{(hPa)}
$$  

**Over ice** ($T < 0^\circ \text{C}$):  
$$
e_s(T) = 6.1115 \exp\left(\left(23.036 - \frac{T}{333.7}\right) \cdot \frac{T}{279.82 + T}\right) \, \text{(hPa)}
$$  
- Valid for $-80^\circ \text{C} < T < 50^\circ \text{C}$ with errors < 0.5%.

### 3. Simplified Magnus Formula  
For quick approximations over water ($0^\circ \text{C} < T < 100^\circ \text{C}$):  
$$
e_s(T) = 6.112 \exp\left(\frac{17.67 \cdot T}{T + 243.5}\right) \, \text{(hPa)}
$$  
- $T$ in $^\circ \text{C}$.

### **Temperature Dependence**  
SVP doubles approximately every $10^\circ \text{C}$ (or $20^\circ \text{F}$) temperature increase. For example:  
- At $0^\circ \text{C}$: $6.11 \, \text{hPa}$  
- At $20^\circ \text{C}$: $23.4 \, \text{hPa}$  
- At $30^\circ \text{C}$: $42.4 \, \text{hPa}$.

### **Applications**  
1. **Relative Humidity (RH)**:  
   $$
   \text{RH} = \frac{\text{Actual Vapor Pressure}}{e_s(T)} \times 100\%
   $$  
   Indicates air’s moisture content relative to its capacity.  

2. **Dewpoint**: Temperature at which air becomes saturated ($RH = 100\%$). Calculated iteratively using $e = e_s(T_d)$.  

3. **Weather Prediction**: Used in thermodynamic diagrams (e.g., skew-T) to assess cloud formation and storm potential.

# WRF

- [relative humidity | 2020](https://forum.mmm.ucar.edu/threads/relative-humidity.9134/)
  - The Relative Humidity (RH) is simply the mixing ratio divided by the saturation mixing ratio. wrfout files include mixing ratio and T, from which you can easily obtain RH.
  - Here is a small piece of Fortran to calculate relative humidity from mixing ratio:
    ```fortran
    real, parameter :: svp1=611.2
    real, parameter :: svp2=17.67
    real, parameter :: svp3=29.65
    real, parameter :: svpt0=273.15
    real, parameter :: eps = 0.622
    rh = 1.E2 * (p*q/(q*(1.-eps) + eps))/(svp1*exp(svp2*(t-svpt0)/(T-svp3)))
    ```
  - Note that **t (temperature), p (pressure) and q (mixing ratio)** are output variables in wrfout.
- [How to derive Specific Humidity from WRF output ? | 2024](https://forum.mmm.ucar.edu/threads/how-to-derive-specific-humidity-from-wrf-output.16403/)
  - You're right, that specific humidity is not a variable that is available to output with wrf. It looks like there is one (called QLEV_URB3D) if you happen to be using an urban parameterization scheme. Otherwise, you may have to calculate it during post-processing. I would recommend using either NCL or python to try to get the output you want.