---
layout: post
title: MPAS | WRF | Solar Energy
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS]
author: wpsze
date: 2025-02-12 18:02:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/gXPusJ6.png
banner_img: https://i.imgur.com/gXPusJ6.png
---

# 太陽輻射

- 陽光從太陽到達地球表面的過程中，有部分直接穿越大氣層到達地面，稱為**太陽直接輻射**。
- 其餘的太陽光因被大氣中的空氣分子、水蒸氣及塵粒所散射或反射，而以間接的路徑抵達地面，稱為**太陽漫射輻射**。
- **總太陽輻射**是指所有到達地面的太陽光，即太陽直接輻射及太陽漫射輻射的總和。
- Units: W/m2
- 在晴天，海平面上的最大正常全天空日射約為 $1000 W/m^{2}$

太陽能是最充裕的再生能源，故此有需要多加了解它的強度。科學界普遍認為如果我們能收集所有到達地面的太陽能一個小時，便能滿足全球一年的能源需要。為減少對石化燃料的依賴，開發高效的太陽能技術，並將太陽能轉化為可用電能，已迅速成為全球為未來發展清潔能源的方向。太陽直接輻射及太陽漫射輻射的資料有助設計及製造高能源效益的裝置，減少對大型空氣調節設備，如冷氣機或暖氣機等的依賴。

香港天文台自一九五八年開始量度總太陽輻射，現時天文台分別在京士柏氣象站和西貢的滘西洲太陽輻射站量度太陽直接輻射及太陽漫射輻射數據。太陽漫射輻射是利用一部能遮蔽太陽直射的總日射表來量度，而太陽直接輻射則利用安裝在太陽自動追蹤裝置上，並對準太陽的直接日射表來量度。

- Global Horizontal Irradiance (GHI, Units: W/m2)
  - Global horizontal irradiance is the total solar radiation per unit area measured at a **horizontal surface on the earth**. It is typically presented in W/m2 and can be broken down into two components: direct normal irradiance (DNI) and diffuse horizontal irradiance (DHI)
  - The solar zenith angle of the Sun z
  - $$ \text{GHI} = \text{DHI} + \text{DNI} \times \cos (z) $$
- Direct Normal Irradiance (DNI)
- Diffuse Horizontal Irradiance (DIF or DHI)
- Clear-Sky Irradiance
  - Irradiance calculated prior to taking cloud cover into consideration. Clear-sky irradiance is calculated based on solar geometry, elevation, water vapor, ozone, albedo, aerosol optical depth, wind speed and temperature.
  - Clear-sky irradiance data is helpful when using solar resource data to analyze PV system performance. It helps in differentiating between cloudy conditions and other circumstances that may be affecting plant performance, such as equipment failure or losses due to snow cover or soiling.

{% gi 6 3-3 %}
![](https://i.imgur.com/gXPusJ6.png)
![](https://i.imgur.com/fAEkJgV.png)
{% endgi %}

![](https://i.imgur.com/GlRJZm8.png)

## Τhe absorption bands of Earth's atmosphere

Τhe absorption bands of Earth's atmosphere (grey colour) delimit its atmospheric windows (middle panel) and the effect they have on both downgoing solar radiation and upgoing thermal radiation emitted near the surface is shown in the top panel. The individual absorption spectra of major greenhouse gases plus Rayleigh scattering are shown in the lower panel.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/5/55/Atmospheric_Transmission.svg/614px-Atmospheric_Transmission.svg.png)

# ECMWF | IFS | ERA5 : SSRD

- [What are the definitions of the radiation fields?](https://confluence.ecmwf.int/pages/viewpage.action?pageId=111155332)
  - `"Surface solar radiation downwards" (SSRD)` is the incident solar (shortwave) radiation and `"surface solar radiation" (SSR)` is the net solar radiation (the fraction of solar radiation not reflected by the surface). `"Surface net solar radiation, clear sky" (SSRC)` is a hypothetical field of net solar radiation assuming no clouds.
  - `"Surface thermal radiation downwards" (STRD)` is the incident thermal (longwave) radiation and `"surface thermal radiation" (STR)` is the net thermal radiation. `"Surface net thermal radiation, clear sky" (STRC)` is a hypothetical field of net thermal radiation assuming no clouds.
  - "Top solar radiation" is the net solar (shortwave) radiation at the top of the atmosphere (TOA). "Top net solar radiation, clear sky" is a hypothetical field assuming no clouds.
  - "Top thermal radiation" is the net thermal (longwave) radiation at the top of the atmosphere. "Top net thermal radiation, clear sky" is a hypothetical field assuming no clouds.

**Additional details on radiation parameters can be found here**: [Radiation-quantities-ecmwf-model-and-mars.pdf (document correct as of Cycle 41R1)](https://www.ecmwf.int/sites/default/files/elibrary/2015/18490-radiation-quantities-ecmwf-model-and-mars.pdf)

## Details of the Variable:

- **Name**: Surface Solar Radiation Downwards (SSRD)
- **GRIB Parameter ID**: 169 (in GRIB format, as used by ECMWF)
- **Units**: J/m² (accumulated over a time step, typically hourly or 3-hourly depending on the dataset)
- **Description**: This is the amount of solar radiation (shortwave) reaching the surface of the Earth, integrated over a specified time period. To convert it to an average irradiance in W/m², you divide the accumulated value (J/m²) by the time step in seconds (e.g., 3600 seconds for hourly data).

### Notes:
1. **Availability**: SSRD is available in many ECMWF IFS outputs, including the high-resolution deterministic forecast (HRES), ensemble forecasts (ENS), and reanalysis products like ERA5.
2. **Direct vs. Diffuse**: SSRD includes both direct and diffuse radiation. If you specifically need **Direct Normal Irradiance (DNI)** or diffuse components, these are not directly provided as separate variables in standard IFS outputs. However, they can sometimes be derived using additional post-processing or from specialized datasets (e.g., CAMS radiation services, which use IFS data).

### Practical Example:
If you retrieve SSRD from an hourly ERA5 dataset and the value is 720,000 J/m² over a 1-hour period, the average irradiance is:

$$
720,000 \text{J/m² ÷} 3600 \text{s} = 200 \text{W/m²}
$$

## ** Radiation Quantities

### Conventions

The following conventions are employed in the storage of fluxes by the ECMWF model:

1. Archived fluxes are either **downward** or **net**. Energy entering the Earth's atmosphere-surface system is taken as a positive quantity, and therefore downward fluxes are positive and net flux refers to the downward flux minus the upward flux. To obtain the upward flux ($F^{up}$) from the downward flux ($F^{dn}$) and net flux ($F^{net}$), use $F^{up} = F^{dn} - F^{net}$.
2. Physical fluxes archived by the ECMWF model are **accumulated** since the start of the relevant forecast, and therefore in **units of J m⁻² (or W m⁻² s)**. Thus, a daily mean (in W m⁻²) is obtained by retrieving the accumulated fluxes at $t_1 = t$ and $t_2 = t + 24$ hours (where $t$ is the time of the start of the average), taking the difference and dividing by $86400$, the number of seconds in a day.
3. **Clear-sky** quantities are computed for exactly the same atmospheric conditions of temperature, humidity, ozone, trace gases and aerosol, but assuming that *the clouds are not there*.
   1. **Clear-sky irradiance** represents the solar energy reaching the Earth's surface at a specific location and time, assuming there are no clouds present. It's the *theoretical maximum irradiance*, which is reduced by cloud cover in real-world conditions. Clear-sky irradiance is influenced by factors such as the Earth-Sun distance and their relative positions.
   2. **All-sky irradiance** considers all scattering and absorption by gases, aerosols, and clouds. It is another name for **"global horizontal irradiance" (GHI)**, which is the total solar irradiance (direct plus diffuse) incident on a horizontal surface. Semi-empirical models produce all-sky irradiance as a final output, accounting for all atmospheric factors, including cloud cover.
4. **Solar** or **short-wave** refers to radiation emitted by the Sun, then scattered, absorbed or transmitted by the atmosphere and reflected or absorbed by the surface. It corresponds roughly to the $0.2-4 \mu m$  or $50,000–2600 cm^{-1}$ part of the spectrum. Thermal, **terrestrial** or **long-wave** refers to radiation emitted and absorbed by the surface or by gases, clouds and particles within the atmosphere. It corresponds roughly to the $4–100 \mu m$ or $2600–10 cm^{-1}$ part of the spectrum. Note that there is some spectral overlap between the two, which is fully represented in the model, so the division between solar and thermal radiation should not be thought of as simply radiation with a wavelength shorter or longer than $4 \mu m$, but rather as radiation originating from the sun versus originating from emission by the Earth or its atmosphere.

{% gi 6 2-2-2 %}
![](https://i.imgur.com/pjyvEAB.png)
![](https://i.imgur.com/cHjHFhO.png)
![](https://i.imgur.com/e49unoQ.png)
![](https://i.imgur.com/5NVZC7h.png)
{% endgi %}

### Earth's Energy Budget Diagram

In its orbit around the Sun, the part of Earth that faces the Sun receives approximately $1,371 W/m2$ of energy. Averaged over the area of Earth's full sphere, the energy from sunlight coming to the top of the atmosphere is approximately $340 W/m2$.

![Scientists have been able to document global incoming and outgoing radiation averages, which provide an understanding of how energy is absorbed, reflected, and released by Earth's atmosphere, clouds, and surface. The numbers in parentheses represent the uncertainty range, or variability, associated with these averages. IPCC, WG1, 2021](https://i.imgur.com/QhR1rAk.png){width=500}

---

# GFS: DSWRF

The Global Forecast System (GFS) model is used to predict solar power generation by forecasting various weather parameters, including downward short-wave radiation flux (DSWRF). The GFS model is run daily at 00, 06, 12, and 18 UTC, providing hourly forecasts for up to 120 hours.

Here's how the GFS model and DSWRF are used in solar energy predictions:

*   **DSWRF Parameter:** GFS uses the DSWRF parameter, labeled as a "0-3 hour ave".
*   **NWP Variables:** The GFS model uses nine Numerical Weather Prediction (NWP) variables to predict solar generation at a site. These include shortwave radiation, cloud cover, air temperature, wind speed, and precipitation.
*   **Inputs for Models:** The parameters derived from GFS are used as inputs for solar irradiance models.
*   **Calibration:** GFS solar irradiation forecasts can be calibrated to improve accuracy.
*   **Bias Correction:** Correcting cloud cover bias in GFS-derived cloud forecasts can improve energy forecasts.

---

# WRF

WRF can output the following variables that meet your need:

- `GHI` should be in the output. It is a variable called `SWDOWN`. [link](https://forum.mmm.ucar.edu/threads/solar_diagnostics-output-variables-not-showing-in-wrfout.10871/)
  - The direct normal irradiance (`DNI`) and the diffuse irradiance are exposed via the Registry. You need to edit the file `Registry.EM_COMMON` and add the variables `SWDDNI` and `SWDDIF` to the WRF output by adding a `h` in the IO part of the table for these variables.
  - Please **recompile WRF** after you change Registry. Remember to type `./clean -a` before recompile the codes.
- `SWDDIR` "Shortwave surface downward direct irradiance" "W m-2" ""
- `SWDDIF` "Shortwave surface downward diffuse irradiance" "W m-2" ""

# WRF-Solar

- <https://ral.ucar.edu/solutions/products/wrf-solar>
- [Problem With WRF-Solar Output SWDDNI and SWDDIF.](https://forum.mmm.ucar.edu/threads/problem-with-wrf-solar-output-swddni-and-swddif.11345/)

```Registry.EM_Common
state real swddir ij misc 1 - rhd "SWDDIR" "Shortwave surface downward direct irradiance" "W m-2" ""
state real swddir2 ij misc 1 - rhd "SWDDIR2" "Shortwave surface downward direct irradiance from FARMS" "W m-2" ""
state real swddirc ij misc 1 - rhd "SWDDIRC" "Clear-sky Shortwave surface downward direct irradiance" "W m-2" ""
state real swddni ij misc 1 - rhd "SWDDNI" "Shortwave surface downward direct normal irradiance" "W m-2" ""
state real swddni2 ij misc 1 - rhd "SWDDNI2" "Shortwave surface downward direct normal irradiance from FARMS" "W m-2" ""
state real swddnic ij misc 1 - rhd "SWDDNIC" "Clear-sky Shortwave surface downward direct normal irradiance" "W m-2" ""
state real swddnic2 ij misc 1 - rhd "SWDDNIC2" "Clear-sky Shortwave surface downward direct normal irradiance from FARMS" "W/m^2" ""
state real swddif ij misc 1 - rhd "SWDDIF" "Shortwave surface downward diffuse irradiance" "W m-2" ""
state real swddif2 ij misc 1 - rhd "SWDDIF2" "Shortwave surface downward diffuse irradiance from FARMS" "W m-2" ""
```

- 雲和氣溶膠對太陽輻射的模擬影響最大，在簡單的WRF模式中，要充分考慮氣溶膠的化學過程，導致陰雨天太陽輻射的不良可信度散射，可用性不高。
- WRF-SOLAR積分模式考慮了氣溶膠對輻射的回饋和淺積分雲對輻射的回饋，很大程度上提高了太陽輻射的得分。
- WRF-Solar可以輸出直接輻射（直接法向輻照度，DNI）和散射輻射（漫射輻照度，DNI）。
- WRF-4.2以後的版本改進了相關的實體參數化方案，官方建議使用4.2.2或以上的版本。

# MPAS

- **MPAS v8.0.0**
- In ./src/core_atmosphere/physics, added the variables swddir, swddni, and swddif. All three variables are output from rrtmg_swrad and input to the updated Noah land surface scheme. [Commit 54d9344](https://github.com/MPAS-Dev/MPAS-Model/commit/54d934448a62f65971218bd554e7db5343b7c750)

```src/core_atmosphere/Registry.xml
<var name="swddir" type="real" dimensions="nCells Time" units="W m^{-2}"
        description="shortwave surface downward direct irradiance"/>

<var name="swddni" type="real" dimensions="nCells Time" units="W m^{-2}"
        description="shortwave surface downward direct normal irradiance"/>

<var name="swddif" type="real" dimensions="nCells Time" units="W m^{-2}"
        description="shortwave surface downward diffuse irradiance"/>
```

# References

1. [太陽輻射量的二十四小時時間序列 | HKO ](https://www.hko.gov.hk/tc/wxinfo/ts/display_element_solar.htm)
2. [輻射小知識 - 太陽輻射 | HKO ](https://www.hko.gov.hk/tc/radiation/tidbit/201003/solar.htm)
3. [中华人民共和国国家标准 | 太阳总辐射辐照量划分 | 2014](https://www.cma.gov.cn/zfxxgk/gknr/flfgbz/bz/202209/P020220921561224172396.pdf)
4. [[能源] WRF on AWS | 2021](https://www.liuchunhua.me/post/industry/utility_wrfonaws/wrfonaws/)
5. [行政院原子能委員會委託研究計畫研究報告 | 2016](https://www.nusc.gov.tw/share/file/information/fl-Tg5gcH6VoFxiLPkPRGA__.pdf)
6. [太阳辐射预报方法分类与评述 | 2023](https://epjournal.csee.org.cn/tyn/cn/article/pdf/preview/10.19911/j.1003-0417.tyn20220121.02.pdf)
7. [光伏发电预测——WRF-Solar](https://zhuanlan.zhihu.com/p/557953099)
8. [What is the clear sky irradiance?](https://solar-energy-connection.com/what-is-the-clear-sky-irradiance/)