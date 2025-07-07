---
layout: post
title: NWP | Eddy Dissipation Rate (EDR)
categories: [NWP]
tags: [NWP, MPAS, WRF, EDR, aviation, HKO]
author: wpsze
date: 2025-04-08 09:36:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/NtQb7zF.png
banner_img: https://i.imgur.com/NtQb7zF.png
---

# Eddy Dissipation Rate (EDR)

**Eddy Dissipation Rate (EDR)** is a measure of atmospheric turbulence intensity, quantifying how quickly turbulent kinetic energy (TKE) is dissipated in the atmosphere. It is an aircraft-independent metric used by aviation to assess turbulence severity.

## Definition and Units
- **EDR** is defined as the **cube root of the dissipation rate of TKE**, with units of $m^{2/3} s^{-1}$.
- However, in some contexts, EDR is expressed in $m^2/s^3$ when referring to the dissipation rate itself before taking the cube root.

## Equation Formula
The dissipation rate of TKE ($\epsilon$) is related to EDR by:
$$
EDR = \epsilon^{1/3} 
$$
where$ \epsilon$ is the dissipation rate of TKE, typically in $m^2/s^3$.

## Derivation of Dissipation Rate ($\epsilon$)
In fluid dynamics, the dissipation rate of TKE can be related to the turbulent velocity fluctuations ($u', v', w'$) and the integral scale of turbulence ($l_0$) through Kolmogorov's hypothesis:
$$
\epsilon = \frac{u'^3}{l_0} 
$$
However, in practical applications,$\epsilon$ **is often derived from mesoscale models using TKE closure schemes**.

## Practical Interpretation
- **EDR Values**: Range from 0 (smooth air) to 1 (intense turbulence), though actual values are often scaled for easier interpretation (e.g., multiplied by 100).
- **Aircraft Dependence**: While EDR itself is aircraft-independent, its impact on aircraft depends on the aircraft's size and weight.

## Example Calculation
Given a dissipation rate of TKE ($\epsilon = 0.1 \, m^2/s^3$), the EDR would be:
$$
EDR = (0.1)^{1/3} \approx 0.464 \, m^{2/3} s^{-1} 
$$

This value indicates moderate turbulence intensity, but its effect on an aircraft would depend on the aircraft's size.

## Why 1/3 power?

The Eddy Dissipation Rate (EDR) being related to the **one-third power of the energy dissipation rate** **emerges from scaling arguments in turbulence theory**.

### Turbulent Kinetic Energy and Dissipation

1. **Energy Cascade:** In turbulent flows, energy is transferred from larger scales (larger eddies) to smaller scales through a process called the energy cascade. Eventually, this energy is dissipated as heat due to viscous effects.

2. **Dissipation Rate ($\epsilon$):** The dissipation rate $\epsilon$ quantifies how much turbulent kinetic energy is converted into thermal energy per unit mass per unit time. It is typically measured in $\text{m}^2/\text{s}^3$.

### Scaling Arguments

#### **Kolmogorov's Turbulence Theory**
Kolmogorov's 1941 hypothesis states that small-scale turbulence statistics depend only on $\epsilon$ and kinematic viscosity ($\nu$). Key scales include:

- **Velocity scale**: $v_\eta = (\nu \epsilon)^{1/4}$  
- **Length scale**: $\eta = (\nu^3/\epsilon)^{1/4}$  
- **Time scale**: $\tau_\eta = (\nu/\epsilon)^{1/2}$  

The relation between EDR and the dissipation rate can be evaluated using **dimensional analysis** and **turbulence scaling laws**:

- **Turbulent Velocity ($u'$):** The root mean square of turbulent velocity fluctuations scales with the energy dissipation rate as:
  
  $$
  u' \sim (\epsilon \cdot L)^{1/3}
  $$

- **Characteristic Length Scale ($L$):** The characteristic length scale of turbulence affects how energy is distributed among eddies.

### Resulting EDR Expression

Putting it together, when we express the EDR in terms of these scales, we often end up with:

$$
\text{EDR} \sim \left( \frac{u'}{L^{1/3}} \right) \sim \epsilon^{1/3}
$$

This is why the EDR can be approximated as the one-third power of the energy dissipation rate in certain contexts. It captures the relationship between the energy scale and the turbulent structures present in the flow. 

(More ......)

# HKO

- [Performance of Eddy Dissipation Rate Estimates from Wind Profilers in Turbulence Detection | 2004](https://www.hko.gov.hk/tc/publica/reprint/files/r553.pdf)
- [Eddy Dissipation Rate (EDR) Profile Determined from a Minisodar in Terrain-disrupted Airflow | 2007](https://my.weather.gov.hk/tc/publica/reprint/files/r718.pdf)
- [Estimate of Eddy Dissipation Rate Using Spectrum Width Observed by the Hong Kong TDWR Radar | 2009](https://www.hko.gov.hk/en/publica/reprint/files/r848.pdf)
- [Performance of LIDAR-based Turbulence Detection Algorithm | 2008](https://www.hko.gov.hk/hko/publica/reprint/r758.pdf)
- [Chan, P. W., P. Zhang, and R. Doviak. "Calculation and application of eddy dissipation rate map based on spectrum width data of a S-band radar in Hong Kong." Mausam 67.2 (**2016**): 411-422.](https://repository.library.noaa.gov/view/noaa/32176)
  - The spectrum width data of an S-band radar in Hong Kong are used to calculate the map of eddy dissipation rate (EDR) with the objective of providing turbulence alerting service for the en-route aircraft in the Pearl River Delta region. 
  - The calculation methodology is different from that reported in the existing literature by also removing the wind shear contribution in determining the **radar-based EDR**. 
  - Time series graph for comparing $EDR^{1/3}$ between **aircraft (blue) and radar (pink)**. Comparison of $EDR^{1/3}$ between aircraft and radar for the second turbulence case
    - ![Time series graph for comparing EDR^{1/3} between aircraft (blue) and radar (pink). Comparison of EDR^{1/3} between aircraft and radar for the second turbulence case](https://i.imgur.com/U5B7Ofi.png){width=450}
- [Chan, Pak Wai, Kai Kwong Lai, and Qiu Sheng Li. "High‐resolution simulation of a severe case of low‐level windshear at the Hong Kong International Airport: Turbulence intensity and sensitivity to turbulence parameterization scheme." Atmospheric Science Letters 23.7 (**2022**): e1090.](https://rmets.onlinelibrary.wiley.com/doi/pdf/10.1002/asl.1090)
  - **Large eddy simulations** are performed for a severe windshear case of terrain-induced airflow disturbances at the Hong Kong International Airport.
    - (i) to investigate the performance of large eddy simulation in **capturing the turbulence intensity** to be encountered by the aircraft; and 
    - (ii) to **find out the impact of the choice of turbulence parameterization scheme** on the simulation results.
  - The common metric for turbulence intensity for aviation application is the cube root of eddy dissipation rate (EDR), $EDR^{1/3}$ , which is the dissipation rate of the turbulent kinetic energy.
  - Structure Function
    {% gi 4 2-2 %}
    ![](https://i.imgur.com/BWNsCvg.png)
    ![](https://i.imgur.com/cxNR0hN.png)
    {% endgi %}
- [Chan, Pak Wai, et al. "Observation and numerical simulation of a windshear case at an airport in the Qinghai-Tibet Plateau." Applied Sciences 14.23 (2024): 10981.](https://shixm.people.ust.hk/pdf/Windshear24.pdf)
  - ![](https://i.imgur.com/Fji6f5d.png)

# Literature Review

- [Forecasting clear-air turbulence | 2021 | ecmwf](https://www.ecmwf.int/en/newsletter/168/meteorology/forecasting-clear-air-turbulence)
  - **The eddy dissipation rate (EDR), which is the cube root of the dissipation rate of turbulent kinetic energy**, and hence has units of $m^{2/3} s^{–1}$, has become the International Civil Aviation Organization (ICAO) standard for aircraft reporting and therefore the standard measure for clear-air turbulence (CAT).
- [Eddy Dissipation Rate (EDR)](https://skybrary.aero/articles/eddy-dissipation-rate-edr)
  - **Eddy dissipation rate (EDR)** is an **objective, aircraft-independent, universal measure** of turbulence based on the rate at which energy dissipates in the atmosphere.
  - **Pilot reports (PIREPs)** on turbulence are inherently subjective. What is light turbulence to a B747 might be perceived as moderate to severe for a small single-engine aircraft. EDR provides a measurement that is not based on any particular aircraft, but its values can be referenced against aircraft size. 
- [Estimation of Eddy Dissipation Rates from Mesoscale Model Simulations | 2012 | NASA](https://ntrs.nasa.gov/api/citations/20120000925/downloads/20120000925.pdf)
  - The Eddy Dissipation Rate is an important metric for representing the intensity of atmospheric turbulence and is used as an input parameter for predicting the decay of aircraft wake vortices. In this study, the forecasts of eddy dissipation rates obtained from the current state-of-the-art mesoscale model are evaluated for terminal area applications. The Weather Research and Forecast mesoscale model is used to simulate the planetary boundary layer at high horizontal and vertical mesh resolutions. **The Bougeault-Lacarreré and the Mellor-Yamada-Janjić schemes implemented in the WRF model** are evaluated against data collected during the National Aeronautics and Space Administration’s Memphis Wake Vortex Field Experiment. Comparisons with other observations are included as well.
  - In this paper, EDR is energy dissipation rate. If consider this $EDR^{1/3}$, it is same as above definition.
  - {% gi 3 1-1-1%}
    ![](https://i.imgur.com/NtQb7zF.png)
    ![](https://i.imgur.com/5mkmsUh.png)
    ![](https://i.imgur.com/xOQr14P.png)
    {% endgi%}
- [EDR Implementation Update Turbulence Mitigation Workshop IV | 2021 | Gregory Meymaris | NCAR/RAL](https://fpaw.aero/sites/default/files/144/talk2-selfpresentation-meymaris-edr-implementation-tw4-2021.pdf)
  - Measures EDR, an atmospheric turbulence intensity metric; not aircraft dependent
  - **Energy (Eddy) Dissipation Rate**
  - {% gi 4 2-2%}
    ![](https://i.imgur.com/aPPNsKT.png)
    ![](https://i.imgur.com/6oB2eVh.png)
    ![](https://i.imgur.com/8kQaVMk.png)
    ![](https://i.imgur.com/jpAgrGZ.png)
    {% endgi%}

# Measuring the **Eddy Dissipation Rate (EDR)**

Measuring the **Eddy Dissipation Rate (EDR)** from high-frequency wind measurements involves several techniques, each leveraging different mathematical and statistical approaches. Here's an overview of how EDR is typically measured using high-frequency data:

## Techniques for Measuring EDR

1. **Second-Order Structure Function**:
   - This method involves analyzing the spatial or temporal differences in wind velocity components (e.g., zonal, meridional, vertical) to estimate the dissipation rate. The structure function is defined as:
     $$
     D_{u}(r) = \langle [u(x + r) - u(x)]^2 \rangle
     $$
   - For small separations $r$, $D_{u}(r)$ is proportional to $r^{2/3}$, which relates to the Kolmogorov scaling law, allowing the estimation of $\epsilon$ and thus EDR [link 1](https://amt.copernicus.org/articles/15/2277/2022/), [link 2](https://research.tudelft.nl/files/69746455/jtech_d_18_0084.1.pdf).

2. **Power Spectral Density (PSD)**:
   - PSD is used to analyze the distribution of turbulent kinetic energy across different frequencies. By fitting a power-law spectrum (typically $-5/3$  in the inertial subrange) to the PSD, one can estimate $\epsilon$ and subsequently EDR [link 3](https://my.weather.gov.hk/tc/publica/reprint/files/r718.pdf), [link 4](https://ntrs.nasa.gov/api/citations/20130003224/downloads/20130003224.pdf).
   - The relationship between PSD and $\epsilon$ is given by:
     $$
     E(k) = C \epsilon^{2/3} k^{-5/3}
     $$
   - Here, $C$ is a constant, $k$ is the wavenumber, and $E(k)$ is the energy spectrum.

3. **Von Kármán Wind Spectrum**:
   - Similar to PSD, but specifically tailored to atmospheric conditions, this method uses a more complex spectral form that accounts for both the inertial subrange and larger scales [link 1](https://amt.copernicus.org/articles/15/2277/2022/).

4. **Maximum-Likelihood Method**:
   - This statistical approach involves fitting a model to the observed wind data to maximize the likelihood of observing the data given the model parameters, which can include $\epsilon$ or EDR [link 1](https://amt.copernicus.org/articles/15/2277/2022/).

5. **Lognormal Mapping Technique and Parabolic Relationship**:
   - These methods involve transforming observed data (e.g., equivalent vertical gusts) into EDR estimates using predefined statistical relationships [link 1](https://amt.copernicus.org/articles/15/2277/2022/).

## High-Frequency Data Sources

- **Sonic Anemometers**: Provide high-frequency measurements of wind components close to the surface.
- **Radar Systems**: Can estimate wind velocities remotely, allowing for EDR calculations over larger areas.
- **Aircraft Data**: Commercial aircraft quick access recorder (QAR) data can be used to estimate EDR during flight.

## Challenges and Considerations

- **Sampling Rate**: Higher sampling rates (e.g., 20 Hz) are generally more accurate than lower rates (e.g., 1 Hz).
- **Data Quality**: Noise and gaps in data can affect EDR estimates, requiring careful data processing and interpolation.
- **Model Assumptions**: Techniques assume certain spectral behaviors (e.g., $-5/3$ power law), which may not always hold.

# References

1. [Turbulence Types, Tips, and Mitigation](https://www.weather.gov/media/zla/Jurecka_Sincavage%20Turbulence%20-%20SAWS%202023%20-%20Types%20Tips%20Mitigation.pptx.pdf)
2. [Improvements to the World Area Forecast System (WAFS) | 2020 | UK Met Office](https://www.icao.int/MID/Documents/2020/MET%20SG9%20VTC/MET%20SG9%20-%20PPT8%20-%20WAFS%20training%20presentation.pdf)
3. [Schröder, Marcel, et al. "Estimating the turbulent kinetic energy dissipation rate from one-dimensional velocity measurements in time." Atmospheric Measurement Techniques 17.2 (2024): 627-657.](https://amt.copernicus.org/articles/17/627/2024/amt-17-627-2024.html)