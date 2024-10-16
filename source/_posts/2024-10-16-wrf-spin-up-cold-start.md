---
layout: post
title: NWP | MPAS | Cold Start/Spin up/Warm Start 
categories: [NWP]
tags: [WRF, MPAS, NWP]
author: wpsze
date: 2024-10-16 10:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/RwpxULg.png
banner_img: 
---

# Cold Start/Warm Start


## NCAR 

- [NCAR | Real-Time Four-Dimensional Data Assimilation (RT-FDDA)](https://ral.ucar.edu/solutions/products/real-time-four-dimensional-data-assimilation-rt-fdda)


{% note warning %}
Simulation is initialized using a **'cold start'** methodology. This means that they **start with no cloud and precipitation systems, or local thermally-driven circulations**. 
{% endnote %}

Therefore, a certain amount of model **'spin up' time** is required for the atmosphere to begin responding to the mesoscale forcing resulting from variations in the local complex physiography.

## WRF code

For use with **cold start (zero cloud fields)**, 

WRF/run/README.namelist

```text
&physics
 insert_init_cloud                   = .false., ! Default
                                     = .true.,  ! For use with cold start (zero cloud fields), this option will
                                                  use the icloud=3 scheme to produce initial cloud water and cloud
                                                  ice fields as well as ensure initial water vapor field matches
                                                  saturation, which retains clouds beyond the first few timesteps.
```

## 2023 | Developing spin-up time framework ...

1. Liu, Y., Zhuo, L., & Han, D. (2023). **Developing spin-up time framework for WRF extreme precipitation simulations**. Journal of Hydrology, 620, 129443.
   1. In fact, the length of spin-up **depends on the quality of initial inputs and the soil conditions** because the soil moisture content and latent heat flux will influence precipitation processes
   2. For example, **soil moisture responds much slower** than atmospheric variables to dynamical and thermo-dynamical processes
   3. In WRF extreme precipitation simulations, spin-up time allows the model to adjust from the IC to a state that is consistent with its own numerics and physics and to develop appropriate large-scale circulations
   4. Therefore, spin-up time is the required execution time that allows the WRF to reach a physical equilibrium state following the path defined by the BC, while forgetting about the IC
   5. The greater the inconsistencies between IC and model physics, the longer the spin-up time required

## 2021 | Forecast of Low Clouds ...

- Hagman, M., Svensson, G., & Angevine, W. M. (2021). Forecast of Low Clouds over a Snow Surface in the Arctic Using the WRF Model. Monthly Weather Review, 149(8), 2559-2579. https://doi.org/10.1175/MWR-D-20-0396.1

The model is **cold-started** for every simulation...Our experiments show that the problems arise in the initialization of the model. When the SAF WRF forecast begins, 

- it is initialized with specific humidity, winds, temperature and skin temperature from IFS HRES, **but no cloud information is passed from the host model**. 
- When low clouds are present in reality, they increase the downwelling longwave radiation to the surface. This energy source at the surface is especially important in a stably stratified environment with sharp surface inversions as it affects the energy budget at the surface, which, in turn, sets the skin temperature and temperature in the lowest layer of the atmosphere.
- **When no cloud information is available at the initial time in WRF**, this energy is lacking during the first hours when **WRF has to spin up its own clouds from the initialized humidity, a slow process in winter at high latitudes**. With less downwelling longwave radiation, the deficit of energy at the surface leads to a **rapid, unphysical drop in the skin temperature**. Drops of 20°C in 20 s (one time step) have been noted.

## 2018 | Sensitivity of initial‐condition and cloud microphysics ...

- Sikder, M. S., & Hossain, F. (2018). Sensitivity of initial‐condition and cloud microphysics to the forecasting of monsoon rainfall in South Asia. Meteorological Applications, 25(4), 493-509.
                                                
Besides the sensitivity test of the MP schemes in the WRF forecast, the performance of **four different WRF model initialization techniques** was tested in this study. 

- In the first experiment case, the **traditional “cold-start” technique** was used to initiate the WRF model using the GFS forecast (e.g. Givati et al., 2012). The IC of the WRF model was directly taken from the GFS forecast in this case. 
- The second case was also a **cold-start set-up, but the first-hour GFS forecast data were replaced by the GFS-FNL data** which are expected to represent a more accurate IC given the higher number of assimilated observations. Thus, the IC of the model is derived from the GFS-FNL and simulation was continued using the GFS forecast data as the model boundary. 

{% note warning %}
**Exclusion of the first 6 hr** simulation output of a cold-start model is a common practice used to eliminate the model “spin-up” time error. 
{% endnote %}

Although the first two cases involved cold-start initialization, the spin-up effect was not considered in this study to evaluate the advantages of other initialization techniques.

The next two cases were based on a **“warm start”** (**often called a “hot start”**) approach (e.g. Jankov et al., 2007). 

- The output of a **one-day pre-simulated WRF model** was used to initiate the WRF forecast model in these cases. In this way, the uncertainty related to model instability during the so-called spin-up time is expected to be reduced. The GFS-FNL data were used as the initial and boundary condition for this one-day pre-simulation (i.e. hindcast), since they contain more observed data than the operational GFS. **Thereafter, the WRF forecast model was initiated with the restart generated from this hindcast model, and continued with the GFS forecast data as the boundary condition** in the third experiment case. 
- The last experimental case was almost similar to the third experimental case. The only difference was **the first-hour GFS forecast data were replaced by the GFS-FNL data in the WRF forecast simulation**. Therefore, the last case is the fusion of the second and third experimental cases. 

These four experiment cases are denoted here by IC and a serial number denoting the experimental case. Hereafter, the IC1 means the first experiment case to initiate the WRF model; and IC2, IC3 and IC4 the second, third and fourth experimental cases, respectively.

## WRFDA point of view

### Cycling WRF and WRFDA

- Q1: What is "cycling" exactly? How do I set up cycling for WRF and WRFDA?
  - A1: "Cycling" is the process by which the output of a previous WRF forecast is used to initialize a new WRF forecast, rather than taking the initial conditions from some other analysis/forecast through WPS/real. This is usually done in the context of real-time forecast You can find detailed instructions on cycling with WRF/WRFDA in the [relevant section of the Users Guide](http://www2.mmm.ucar.edu/wrf/users/wrfda/Docs/user_guide_V3.9/users_guide_chap6.htm#_Cycling_with_WRF_1%3C/a)
- Q2: What is the difference between **"warm-start"** and **"cold-start"** forecasts?
  - A2: These terms are widely used in the field of atmospheric modeling and data assimilation, however, to quote this site from [the Naval Postgraduate School](http://www.oc.nps.edu/nom/modeling/initial.html), "The terms cold start and warm start may mean slightly different things to different people." In the context of WRF and WRFDA, a **"cold-start" forecast is when the first guess (fg) file** provided to WRFDA is a wrfinput file from WPS/real, while a **"warm-start" forecast is one where the first guess is a WRF forecast file (wrfout)**.

Because of the ambiguity in what constitutes a cold-start or warm-start, we try to avoid using these terms in the official documentation.

## WRF & MPAS-A Support Forum

- [When should I consider spin-up for WRF predictions?](https://forum.mmm.ucar.edu/threads/when-should-i-consider-spin-up-for-wrf-predictions.13821/)
  - **Usually 9-12 hours of spin-up time will be sufficient**. Note that WRF is under strong constraints of lateral forcing and changes in surface static data could be more important for long-term climate simulation.
- [long-term WRF run](https://forum.mmm.ucar.edu/threads/long-term-wrf-run.5241/)
  - I don't believe the length of the run should make a difference. **The spin-up time is simply to ensure that the model has had ample time to stabilize so that the results can become reliable**, and are not just simply based on the initial and boundary conditions. Once this happens, the model can go forward to generate estimations for forecasting purposes.
- [Long-time WRF simulation](https://forum.mmm.ucar.edu/threads/long-time-wrf-simulation.10067/)
  - Q:  I want to do a 1-year WRF simulation with analysis nudging using ERA5 hourly reanalysis data. Take 2019 for example, should I run WRF from Dec,2018 to Dec,2019, taking Dec in 2018 as spin-up period? If so, does nudging set in Dec,2018?
  - A: **You shouldn't need more than about 24-48 hours for spin-up**, so if you're interested in the data from 1 Jan 2019, you can just start it Dec 30 or 31 and it should be okay.
  - **Nudging is beneficial throughout the whole simulation**, especially for larger/continental sized domains. It's less necessary for smaller domains that are more constrained by the boundaries.
  - **As for long simulations, the only other thing that comes to mind is to make sure you have some additional SST input data**. The GFS provides SST data with their files, but the resolution is temporally coarse. You will want something with higher temporal resolution. You may already have some, or a resource for it, but if you need additional resources, you can see slide 6 from this [presentation](https://www2.mmm.ucar.edu/wrf/users/tutorial/202101/werner_data_util_pp.pdf).
- [Altering MPAS's initial conditions](https://forum.mmm.ucar.edu/threads/altering-mpass-initial-conditions.13680/)
  - Q: I am now considering changing initial conditions for the global MPAS run (60-3km mesh). Do you have any idea about the approximate spin-up time for changing different initial variables? For non-change run, I will spin up for about 6 hours and timestep is about 15 seconds. If I change the vegation type (say vegetation fraction), do I need to increase my spin-up time, e.g., a few hours more or even several days?
  - A: Based on our experience with WRF model, 9-12 hours of spin-up time is sufficient. Note that WRF is a limited-area model under constraints of large scale forcing. The spin-up time for MPAS global model could be different. A few papers have mentioned the spin-up issue for MPAS. For example, the spin-up time is set to 24h in the [study](https://macmaq.aqrc.ucdavis.edu/sites/g/files/dgvnsk6036/files/inline-files/Kemal Gurer_Poster.pdf). Please refer to the literature for more information.

# Spin-Up Time

In numerical weather prediction (NWP), **spin-up time** refers to the period required for a model to **adjust from its initial state to a more stable and representative equilibrium state**. This adjustment is crucial for achieving accurate forecasts, as it allows the model to correct any discrepancies in the initial conditions and dynamics.

## Key Characteristics of Spin-Up Time

### Dynamic and Thermal Adjustments

- **Initial Phase**: During the spin-up phase, the model undergoes significant dynamic and thermal adjustments. These adjustments are necessary for the model to align with the physical processes governing atmospheric behavior.

- **Duration**: Spin-up time can vary widely depending on the type of model and its configuration. For short-term weather forecast models, spin-up times typically range from several hours to about a dozen hours. In contrast, climate models may require spin-up periods extending from months to even decades.

### Impact on Forecast Accuracy
- **Forecast Reliability**: The forecasts produced during the spin-up phase are often considered unreliable. Many studies recommend disregarding results from this period when evaluating model performance[1]. This is due to potential inaccuracies caused by transient phenomena, such as spurious gravity waves, which can lead to increased errors in forecast variables.
- **Computational Resources**: Extended spin-up times can consume significant computational resources, particularly in climate models or ocean models. Reducing spin-up time is therefore essential for improving efficiency and forecast accuracy.

### Factors Influencing Spin-Up Time
- **Model Type**: Different models exhibit varying spin-up characteristics based on their design and purpose. For instance, global climate models generally have longer spin-up times compared to regional models.
- **Initial Conditions**: The quality of initial conditions derived from observational data or reanalysis influences how quickly a model can reach equilibrium. Models initialized with better data tend to have shorter spin-up periods.

In summary, spin-up time in NWP is a critical aspect that affects the accuracy and reliability of weather forecasts. Understanding and optimizing this period is essential for enhancing model performance and resource efficiency.

## 2021 Spin-up characteristics ...
- Ma, Z., Zhao, C., Gong, J., Zhang, J., Li, Z., Sun, J., Liu, Y., Chen, J., and Jiang, Q.: Spin-up characteristics with three types of initial fields and the restart effects on forecast accuracy in the GRAPES global forecast system, Geosci. Model Dev., 14, 205–221, <https://doi.org/10.5194/gmd-14-205-2021>, 2021.
- <https://gmd.copernicus.org/articles/14/205/2021/gmd-14-205-2021.pdf>

This process is called the spin-up in numerical modeling, and 

- the time required to reach a new equilibrium state is called the spin-up time (Wolcott and Warner, 1981; Kasahara et al., 1992; Séférian et al., 2016; Sheng et al., 2006; Liu et al., 2008; Xue et al., 2017). 
- During the dynamic and thermal adjustment in the spin-up, spurious gravity waves can be triggered, causing a rapid increase in the root-mean-square error of the forecast variables in the model and an underestimation of the forecast precipitation (Wehbe et al., 2019; Qian et al., 2003). It leads to unreliable forecast results during the spin-up. 
- Therefore, many studies generally do not consider the forecast results during the spin-up when evaluating the model forecasts (Lo et al., 2008; Kleczke et al., 2014; Xie et al., 2013; Zhao et al., 2012). 
- If the spin-up time is too long in the operational model, it would inevitably affect the forecast accuracy of the model. 
- In addition, the overlong spin-up in the climate model or the ocean model can consume excessive computing resources (Duben et al., 2014). 
- Therefore, studying the spin-up characteristics and reducing the spin-up time are of great significance for improving the model forecast and saving computing resources.

## 關於數值模式spin up的一些說明

- From <https://mp.weixin.qq.com/s/4osE0V9fxZO695N8AifxmA>

由於難以得到理想的準確的初始場，模式初始場各變數之間，或與外部條件之間存在不平衡或不匹配。因此模式啟動後，需要運行一段時間，以達到所需的「平衡狀態」。而達到這個狀態所需的時間，稱為spin up time（起轉時間）。在完成spin up之前，模式的輸出結果往往是不可信的。

對於不同的研究對像或不同模式，其spin up所關注的重點與所需的時間是不一樣的。

對於中小尺度氣像模式，例如WRF，初始場各變數特別是濕度場，往往過於平滑，使得模式的初始時刻的輻散場、非絕熱加熱與濕度場之間缺乏一致性，動力、熱力過程與水分循環不協調。**且中小尺度模式一般是由無雲初始場開始的，所以開始需要一定的時間產生相應的雲水信息，冷啟動之後往往需要若干小時才能產生符合實際的雲水信息**。

一般認為，WRF模式在6小時即可完成spin up（也許更短，6小時是一個比較保守的估計）。由於spin up的存在，限制了數值模式在短臨預報的應用。因此有不少資料同化的工作，追求盡可能同化雲水相關的觀測訊息，縮短模式spin up時間。

而在氣候模擬中，spin up的時間更長。例如在CMIP6的高解析度模式比較計劃 （HighResMIP）中，其第二層級 （Tier-2）試驗為1950—2050 年的百年耦合試驗。為了在試驗前盡可能達到海-氣準平衡態，HighResMIP工作小組建議先進行約 50年的spin up，之後再以「熱啟動」(restart) 的方式完成該段試驗。大氣的回饋過程較快，而海洋的狀態調整和回饋相對較慢。在海洋模式中，spin-up需要的時間更久。海洋模型需要多長時間才能達到平衡？ 與所關注的研究對象和使用的模型密切相關。

在海盆尺度和全球尺度的海洋環流模式中，模式要達到平衡狀態較難，可能需要數百年的時間。海洋模型以目前的海洋狀態（氣候態）初始化，並向前積分，直到環流結構和密度場等平衡匹配。深海海洋需要數百年的適應，上層海洋只需要大約50年左右。例如要研究上層海洋過程（如厄爾尼諾），則一般只需上層海洋（在主溫躍層之上）達到平衡狀態。

實際上，spin up時間尺度是第一和第二模態的長羅斯貝波（大規模行星波）從東到西穿過盆地、影響水流並最終影響水團特性所需的時間。沿著赤道，第一模態斜壓羅斯貝波橫渡太平洋約需9個月；然而，第二模態的長羅斯貝波斜壓性更強，傳播得更慢。因此，在赤道中需要spin up幾年時間才能達到平衡。

在中緯度地區，長波傳播得更慢。羅斯貝波橫渡太平洋大約需要 11 年。對於全球海洋模型，需要幾十年才能在海洋上層達到平衡。如果只考慮正壓模式，或者模型是二維的，那麼spin up調整時間會更快，大約幾天，因為正壓波傳播得更快。

如果想縮短spin up時間，還可以透過資料同化（nudge）的方式，使得模式狀態不斷逼近觀測。例如在正壓模型中，可以使用海表面高度資料（SSH）。

此外，如果只關心風力驅動的水循環，例如由 Ekman 抽吸引起的海面斜坡產生的流動，那麼幾天的spin up時間可能就足夠了。

# ** Atmospheric Kinetic Energy Spectra --> spinup time

- **The KE spectrum has a canonical structure characterized by different slopes at various wavenumbers**. **Initially, higher-wavenumber components have low energy, which gradually increases as the model spins up**. This adjustment is essential for accurately representing atmospheric dynamics, particularly for phenomena like monsoon rainfall
- The KE spectra in numerical simulations typically require a spin-up period of 12 to 18 hours to stabilize, as indicated by previous studies. This time is crucial for the model to adjust its initial conditions and reach a more representative state.
- **During the spin-up phase, spurious gravity waves may be generated**, leading to increased errors in forecast variables and underestimations of precipitation. The KE spectra help identify these discrepancies by showing how energy cascades through different scales, which can affect forecast reliability.
- **Atmospheric kinetic energy spectra provide valuable insights into the spin-up issue by illustrating how energy transitions through various scales during initialization**. This understanding is vital for improving forecast accuracy and optimizing model configurations in NWP systems.

The paper is,

- Skamarock, W. C., Park, S., Klemp, J. B., & Snyder, C. (2014). Atmospheric Kinetic Energy Spectra from Global High-Resolution Nonhydrostatic Simulations. Journal of the Atmospheric Sciences, 71(11), 4369-4381. https://doi.org/10.1175/JAS-D-14-0114.1

![Fig. 8. Tropospheric and stratospheric kinetic energy spectra for the 3-km MPAS simulations at simulation times 0, 6, 12, and 18 h. The day 5–15 averages are plotted for reference.](https://journals.ametsoc.org/view/journals/atsc/71/11/full-jas-d-14-0114.1-f8.jpg)

- Atmospheric prediction systems are designed and configured in part based on an understanding of these dynamics that, for example, have implications for forecast error growth and spinup time scales and filtering and subfilter-scale physics
- The KE spectra reveal that the MPAS configuration produces an effective model resolution (filter scale) of approximately 6Δx
- The spinup time is found to be somewhat less than a day, consistent with MPAS results presented in Fig. 8
- The spinup time should be comparable to an eddy turnover time.


# References

1. Wolcott, S. W., & Warner, T. T. (1981). A moisture analysis procedure utilizing surface and satellite data. Monthly Weather Review, 109(9), 1989-1998.
2. Kasahara, A., Mizzi, A. P., & Donner, L. J. (1992). Impact of cumulus initialization on the **spinup** of precipitation forecasts in the tropics. Monthly weather review, 120(7), 1360-1380.
3. Séférian, R., Gehlen, M., Bopp, L., Resplandy, L., Orr, J. C., Marti, O., ... & Romanou, A. (2016). Inconsistent strategies to **spin up** models in CMIP5: Implications for ocean biogeochemical model performance assessment. Geoscientific Model Development, 9(5), 1827-1851.
4. Sheng, C., Pu, Y., and Gao, S.: Effect of Chinese Doppler radar data on nowcasting output of mesoscale model, Chin. J. Atmos. Sci., 30, 93–107, 2006 (in Chinese).
5. Liu, S., Jiang, H., Hu, F., Zhang, C., Liu, H., Liang, F., Xin, G., and Wang, J.: Research of **spin-up processes** of land surface model of RAMs for different initial soil parameters, Acta Meteorol. Sin., 66, 351–358, 2008 (in Chinese).
6. Xue, C., Chen, X., Wu, Y., Xu, X., and Gao, Y.: Application of radar assimilation in local severe convective weather forecast, Chin. J. Atmos. Sci., 41, 673–690, 2017 (in Chinese).
7. Wehbe, Y., Temimi, M., Weston, M., Chaouch, N., Branch, O., Schwitalla, T., ... & Al Mandous, A. (2019). Analysis of an extreme weather event in a hyper-arid region using WRF-Hydro coupling, station, and satellite data. Natural Hazards and Earth System Sciences, 19(6), 1129-1149.
8.  Qian, J. H., Seth, A., & Zebiak, S. (2003). Reinitialized versus continuous simulations for regional climate downscaling. Monthly Weather Review, 131(11), 2857-2874.
9.  Lo, J. C. F., Yang, Z. L., & Pielke Sr, R. A. (2008). Assessment of three dynamical climate downscaling methods using the Weather Research and Forecasting (WRF) model. Journal of Geophysical Research: Atmospheres, 113(D9).
10. Weiss, S. J., Pyle, M. E., Janjic, Z., Bright, D. R., Kain, J. S., & DiMego, G. J. (2008, October). The operational high resolution window WRF model runs at NCEP: Advantages of multiple model runs for severe convective weather forecasting. In Preprints, 24th Conf. on Severe Local Storms, Savannah, GA, Amer. Meteor. Soc. P (Vol. 10).
11. Branch, O., Schwitalla, T., Temimi, M., Fonseca, R., Nelli, N., Weston, M., ... & Wulfmeyer, V. (2020). Seasonal and diurnal performance of daily forecasts with WRF-NOAHMP V3. 8.1 over the United Arab Emirates. Geoscientific Model Development Discussions, 2020, 1-40.
    1.  The first 6 h of each forecast (18:00 to 00:00 UTC) were then discarded from the analysis. **The 6 h allows time for the atmosphere to spin up after each cold start – in particular for the residual boundary layer to develop and dissipate before the convective boundary layer starts to develop after sunrise (∼ 06:00 LT), and for potential cloud development**.
