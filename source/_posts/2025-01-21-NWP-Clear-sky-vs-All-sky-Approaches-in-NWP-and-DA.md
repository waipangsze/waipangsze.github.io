---
layout: post
title: NWP | Clear-sky vs. All-sky Approaches in NWP and DA
categories: [NWP]
tags: [MPAS, NWP, WRF, DA]
author: wpsze
date: 2025-01-21 13:29:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/tckuU85.png
banner_img: https://i.imgur.com/tckuU85.png
---

# Clear-sky vs. All-sky Approaches in NWP and DA

In the field of Numerical Weather Prediction (NWP) and Data Assimilation (DA), the **clear-sky** and **all-sky** approaches represent two different methodologies for incorporating satellite observations into weather models. 

## Clear-sky Approach

The **clear-sky approach** has been the traditional method used in NWP since the 1980s, **where observations (obs) affected by clouds are typically excluded from the assimilation process**. This exclusion arises from the challenges in accurately modeling cloud and precipitation conditions, which can significantly distort satellite measurements. As a result, only cloud-free observations are assimilated, limiting the amount of useful data incorporated into weather models. (**其中受雲影響的觀測 (obs) 被排除在同化過程之外**)

- **Limitations**: 
  - Excludes a large percentage of available satellite data, particularly in cloudy regions.
  - Can lead to biases in model outputs due to lack of information about cloud dynamics and moisture distribution.

## All-sky Approach

The **all-sky approach**, developed more recently, aims to utilize all available satellite observations, **including those impacted by clouds and precipitation**. This method has gained traction over the last decade as advancements in data assimilation techniques have improved the ability to handle cloud-affected data effectively. 

- **Key Features**:
  - Direct assimilation of satellite radiances in cloudy conditions, allowing for a more comprehensive use of available data.
  - Enhances the analysis of atmospheric variables such as temperature, humidity, and wind speed by incorporating information from cloud structures.
  - Aims to correct systematic errors in forecasts by using cloud-related observations to inform model adjustments.

## Benefits of All-sky Assimilation

1. **Increased Data Utilization**: The all-sky approach allows for a more balanced assimilation of satellite observations across both clear and cloudy areas, overcoming previous biases towards clear conditions.
2. **Improved Forecast Accuracy**: Studies have shown that using all-sky data can lead to statistically significant reductions in forecast errors, particularly in regions where clouds are prevalent.
3. **Enhanced Coverage**: By including cloud-affected observations, the all-sky method improves coverage in meteorologically interesting areas that may otherwise be underrepresented in NWP models.

## Challenges Ahead

Despite its advantages, **the all-sky approach still faces challenges**, particularly concerning observation error modeling and the complexities introduced by cloud dynamics. Ongoing research is necessary to refine these methods further and ensure their operational viability across various forecasting systems.

In summary, while the clear-sky approach has served as a foundation for satellite data assimilation in NWP, the all-sky approach represents a significant evolution that seeks to leverage more comprehensive datasets for improved weather forecasting capabilities.

# Literature review

- [Wang, Jingnan, et al. "Comparison of assimilating all-sky and clear-sky satellite radiance for Typhoon Chan-Hom and Nangka forecasts." Atmosphere 11.6 (2020): 599.](https://www.mdpi.com/2073-4433/11/6/599/pdf)
  - In the past, the radiative transfer model was not able to represent the scattering properties of hydrometeors well. Furthermore, because of the poor understanding and predictability of clouds and precipitation, as well as the discontinuous, nonlinear nature of moist atmospheric processes [2], **satellite observations are mainly assimilated under clear-sky conditions, discarding a large number of observations in clouds and precipitation area**.
  - **In infrared channels particularly, most data are rejected because of “cloud contamination”** [3].
  - The European Centre for Medium-Range Weather Forecasts (ECMWF) has applied all-sky satellite radiance assimilation, directly making use of observations of clouds and precipitation which provide clearly positive effects on short-term forecasts [13,14]. 

![Figure 2. Brightness temperature simulation for AMSR2 channel 10 after (a) clear-sky quality control and (b) all-sky quality control.](https://i.imgur.com/tckuU85.png){width=600}

- [Duncan, D. I., Bormann, N., Geer, A. J., & Weston, P. (2022). Assimilation of AMSU-A in all-sky conditions. Monthly Weather Review, 150(5), 1023-1041.](https://journals.ametsoc.org/downloadpdf/view/journals/mwre/150/5/MWR-D-21-0273.1.pdf)

![First-guess departures for (top) all-sky and (bottom) clear-sky channel-5 observations assimilated during the 0000 UTC long window on 15 Sep 2019, normalized by the observation errors assigned. The National Hurricane Center (NHC) best track locations at 0000 UTC over the life of Humberto are given by a black ×, with a white circle showing the hurricane location on this date, just east of Florida. Data from all AMSU-A sensors are shown together. Departures are normalized by the assigned observation errors.](https://i.imgur.com/mItN9pH.png){width=600}

- [Li, Yu, Keyi Chen, and Zhipeng Xian. "Evaluation of all-Sky assimilation of FY-3C/MWHS-2 on Mei-yu precipitation forecasts over the Yangtze-Huaihe river basin." Advances in Atmospheric Sciences 38 (2021): 1397-1414.](https://link.springer.com/article/10.1007/s00376-021-0401-y)
  - 采用晴天、多云、雨区等全天候条件下同化微波观测资料对提升与云雨相关的重大天气过程的预报水平有潜在的积极作用。本文选取2017年6月28日至7月3日一次典型的梅雨锋暴雨过程进行了同化及预报研究,基于中尺度WRF-ARW模式及其同化系统WRFDA,采用3D-Var同化方法,利用风云三号C星微波湿度计(FY-3C/MWHS-2)观测资料进行了晴空和全天候同化试验。其结果表明:**全天候同化试验能更加充分的利用云雨区域的微波探测信息,其对FY-3C/MWHS-2的资料利用率比晴空同化试验高出10%**。另外,全天候同化试验减小了约0.5%的湿度场预报误差,对强降水的预报产生了正面影响,但其对风场和温度场的预报总体呈中性的影响。虽然本文中使用的全天候同化算法只能调节湿度、风和温度有关的变量,不能直接获取云雨的信息,但是其对暴雨预报的积极影响在防灾减灾方面存在潜在的业务应用前景。

- [Xu, Dongmei, et al. "Assimilating all-sky infrared radiances from Himawari-8 using the 3DVar method for the prediction of a severe storm over North China." Advances in Atmospheric Sciences 38 (2021): 661-676.](https://link.springer.com/article/10.1007/s00376-020-0219-z)
  - 使用3DVar方法吸收来自Himawari-8的全天红外辐射，以预测华北地区的一场强风暴。尽管雷达观测以高时空分辨率捕获了暴风雨结构，但在降水形成后，它们仅限于暴风雨区域内。对地静止卫星数据覆盖了风暴和周围环境降水形成之前雷达网络中的空白。该研究探索了使用Himawari-8数据与天气研究和预报模型数据同化系统（WRFDA）融合从Himawari-8数据中吸收水汽通道辐射的效果，该辐射对华北地区发生了严重的暴雨。这项研究最初在WRFDA系统的框架中增强了用于高级Himawari成像仪（AHI）辐射的快速云检测方案。在进行数据同化之前，先进行偏差校正，晴空AHI辐射的云探测和多云辐射的观测误差建模。完全应用了所有AHI辐射观测值，而没有对全天候AHI辐射数据同化进行任何质量控制。结果表明，通过使用依赖于云的观测误差模型，模拟的全天空AHI辐射度更适合观测，从而进一步提高了云的高度。全天候的AHI辐射同化会调整所有类型的水凝物变量，尤其是云水和降水雪。事实证明，通过对雷达反射率进行验证，吸收全天候AHI数据可以改善水凝物规格。因此，与仅使用常规和AHI晴朗天空辐射率数据的实验相比，在全天条件下对AHI观测值的同化对降水位置和强度都有整体改善的影响。

# Clear-sky vs. All-sky in DA

The implementation of **all-sky assimilation in the data assimilation (DA) process is generally considered more challenging than the clear-sky approach**. Here are the main factors contributing to this complexity:

## 1. **Complexity of Radiative Transfer Modeling**
All-sky assimilation involves dealing with the intricate effects of clouds on radiative transfer, which complicates the interpretation of satellite observations. The modeling of radiative transfer in cloudy conditions is inherently complex due to scattering effects caused by hydrometeors like ice, snow, and rain. This complexity makes it difficult to accurately interpret satellite observations, as the radiative signal can be significantly altered by cloud properties. Errors from scattering can lead to misinterpretation of temperature increments, complicating the assimilation process. Unlike clear-sky conditions, where the radiative signal is straightforward, clouds introduce scattering and absorption phenomena that must be accurately modeled to extract useful data from satellite observations.

## 2. **Observation Error Characterization**
In all-sky conditions, characterizing observation errors becomes more difficult due to the variability introduced by different cloud types and precipitation. This can lead to larger and correlated errors in observations, complicating the assimilation process and potentially degrading forecast accuracy if not properly managed.

## 3. **Data Volume and Quality Management**
While all-sky assimilation can provide a greater volume of data (up to 65% more than clear-sky assimilation), the challenge lies in managing this increased data volume effectively. The added complexity of integrating cloud-affected observations requires sophisticated methods to ensure that the quality of assimilated data does not compromise forecast performance.

## 4. **Operational Implementation Challenges**
Despite its potential benefits, all-sky assimilation has not yet been widely adopted operationally due to concerns about whether it can consistently improve forecast scores compared to existing clear-sky systems. Operational centers often have well-established clear-sky assimilation frameworks, making it difficult to transition to all-sky methods without risking degradation in forecast quality.

## 5. **Balancing Signal and Noise**
Incorporating cloud-affected observations requires careful balancing between meaningful signals and noise. The challenge is to ensure that the assimilation system does not overfit cloud observations, which can lead to inaccuracies in model initialization and subsequent forecasts.

## 6. **Zero Gradient Problem**
The zero gradient problem arises when data assimilation systems struggle to incorporate cloud information effectively in the absence of clouds in the background state. This issue complicates the initialization of clouds and can hinder the overall performance of the assimilation system

## Conclusion
Overall, while all-sky assimilation offers significant advantages in utilizing a broader range of satellite data, its implementation is fraught with challenges that require advanced modeling techniques and careful management of observation errors. These complexities make it a more demanding process compared to clear-sky assimilation in the DA framework.