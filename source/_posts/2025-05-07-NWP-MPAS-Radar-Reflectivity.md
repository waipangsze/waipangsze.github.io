---
layout: post
title: NWP | MPAS | Radar Reflectivity
categories: [NWP]
tags: [NWP, MPAS, WRF, Meteorology, HKO]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-05-07 13:57:00
index_img: https://i.imgur.com/L1m8XkL.png
banner_img: https://i.imgur.com/L1m8XkL.png
---

# Radar Reflectivity(雷達回波)

在描述雷達回波圖時，總是避免不了用到一個單位 —— **dBZ** 。

**「dBZ」 是表示雷達回波強度的物理量**，可用來估算降雨和降雪強度及預測諸如冰雹、強風等災害性天氣出現的可能性。一般來說，它的數值越大，降雨、降雪的可能性越大，強度也越強。

- 當回波強度**大於或等於40dBZ**時，出現雷雨天氣的可能性較大；
- **當它的值在45dBZ或以上**時，出現暴雨、冰雹、強風等強烈對流天氣的可能性較大。

當然，判斷具體出現什麼天氣時，除了回波強度（dBZ）外，還要綜合考慮回波高度、回波面積、回波移動速度和方向以及演變情況等因素。 **「Z」是雷達反射因子**，與雨滴譜直徑的六次方成正比，單位是 $mm^6/m^3$ ；「dB」是分貝（decibel的縮寫），也可以理解為一個運算符號，**dBZ和Z的換算關係是：dBZ　＝10log（Z）** 。

## 雷達反射率因子Z值

雷達反射率因子Z值的大小，**只取決於雲、雨滴譜**的情況，反映了氣象目標內部降水粒子的尺度和數密度，常用來表示氣象目標的強度。 

- Z值既可透過**雲霧物理觀測的方法求得**，
- 也可以透過**雷達氣象方程式推算**出來。 

Z和粒子直徑的6次方成正比，顯示少數大水滴將提供散射回波功率的絕大部分。由於**雷達反射率因子 Z 只取決於氣象目標本身而與雷達參數和距離無關**，所以不同參數的雷達測得的值可以互相比較。

## Radar Bands

Weather watch meteorological radars operate within the microwave region of the electromagnetic spectrum (around 1 – 100 GHz), though there are meteorological applications for radars outside the microwave region as shown in the table.

**The most commonly used radars for weather surveillance are in the 5 and 10 cm wavelengths (around 6 and 3 GHz resp.)** 

- The part of the spectrum **near 10cm wavelength is commonly called "S" band**, and 
- the part **around 5cm wavelength is called "C" band**.

Radar systems are also commercially available at **X band (~3cm wavelength) that are typically used on ships, boats and aircraft**. These radars are more compact because of the smaller antenna size, but suffer more from attenuation in anything over moderate precipitation.

![Radar band frequencies and wavelengths](https://i.imgur.com/qlXeo9X.png){width=400}

# HKO Educational Resources

- [天氣雷達觀測 | HKO](https://www.hko.gov.hk/tc/education/meteorological-instruments/weather-radar/00192-weather-radar-observations-in-hong-kong.html)
  - RADAR (雷達)一詞是 RAdio Detection And Ranging 的縮寫，意思是以無線電波探測及測距。雷達發明於二次世界大戰前夕，最初用於軍事上。其後雷達應用涵蓋多個領域，其中一項重要的用途是天氣監察。透過探測大氣中的雨點，天氣雷達能非常有效地監察在香港出現的惡劣天氣，例如熱帶氣旋、雷暴和大雨。
  - 雷達不停發出微波脈沖，經大氣中的雨點反射，通過量度這些反射回來的訊號，就能探測到大氣中的降雨。一般來說，**反射回來的訊號越強，雨勢就越大**。至於**雨區與雷達之間的距離**，則可利用微波往返雨區所需的時間而計算出來。
  - 近年來多普勒天氣雷達越趨普及，它能夠量度**雨點移近(或遠離)雷達的速度**。
    - ![](https://www.hko.gov.hk/tc/education/images/fig_00192_radmetc2.jpg){width=400}
    - ![](https://i.imgur.com/OzcuZ10.png){width=400}
- [**天文台的雷達圖像廊**](https://www.hko.gov.hk/tc/wxinfo/radars/radar_gallery/index.htm)
  - **圖像廊搜羅了在惡劣天氣下所拍攝到的雷達圖像，如熱帶氣旋、暴雨、雹暴及雷暴。**
- [淺談機場多普勒天氣雷達（TDWR） | 2012 | HKO](https://www.hko.gov.hk/tc/education/meteorological-instruments/weather-radar/00188-on-terminal-doppler-weather-radar.html)
  - **機場多普勒天氣雷達的任務是探測機場範圍的低空風切變**，相比主要用來探測雨區發展和移動的天氣雷達其技術更為先進。**機場多普勒天氣雷達利用多普勒原理，探測雷達方向的徑向風速。**
  - 微下擊暴流的生命週期短，一般只維持約數分鐘。在雷暴活躍期間，雷達須作頻密掃描，以捕捉雷暴區氣流的急速變化。
    - ![](https://www.hko.gov.hk/tc/education/images/fig_00188_TDWR_1.jpg){width=400}
    - ![](https://www.hko.gov.hk/tc/education/images/fig_00188_TDWR_2c.jpg){width=400}
- [天氣雷達站的非一般避雷系統 | 2013 | HKO](https://www.hko.gov.hk/tc/education/meteorological-instruments/weather-radar/00187-extraordinary-lightning-protection-system-for-weather-radar-stations.html)
  - **天文台一直遵守天氣雷達站避雷接地電阻必須不多於1歐姆的嚴格要求**，以儘量減低受雷擊而影響雷達運作的機會。除了香港天文台，國際上也採用同等標準規範天氣雷達站的設計，世界氣象組織(WMO)轄下的儀器和觀測方法委員會(CIMO)在其IOM第88號報告[3]中便指出了這項要求。
- [利用雷達探測雨區的形相態 | 2015 | HKO](https://www.hko.gov.hk/tc/education/meteorological-instruments/weather-radar/00458-profiling-rain-areas-using-radar.html)
  - 天文台於2015年啟用新的**大老山雙偏振多普勒雷達**（圖二）。跟傳統的單偏振（電磁波的電場或磁場在單一平面上變化）雷達不同，新雷達能夠發射及**接收水平和垂直兩個不同偏振方向的電磁波脈沖**（圖三）。由於水滴的大小和冰的形態會導致不同偏振電磁波反射後的特性出現差異，利用水平和垂直偏振回波特性的差別，有助於判斷雨區的成分，以及雨量的多少。
- [如何解讀多普勒天氣雷達的風場 | 2019 | HKO](https://www.hko.gov.hk/tc/education/meteorological-instruments/weather-radar/00522-how-to-interpret-velocity-field-from-doppler-weather-radar.html)
  - 由於雨點會被風帶動，所以這是一個估算風速值頗好的方法。可是，**必須注意的是此速度是沿著雷達波束方向的風的分量，即徑向風速**。若風與雷達波束平行，徑向風速的值與真實風速相等，若風與雷達波束成垂直方向，徑向風速的值會變為零。徑向風速為零的線稱為零徑速線。
- [冰雹與鈎狀回波 | 2020 | HKO](https://www.hko.gov.hk/tc/education/weather/weather-phenomena/00550-hail-and-hook-echo.html)
  - 冰雹是強雷暴中產生的大冰粒。強雷暴出現時，大氣的垂直運動十分猛烈。由於大氣對流層的温度一般隨高度增加而下降，當較暖濕空氣上升時，空氣中的水分會遇冷凝結。**由於上升氣流猛烈，水汽會被帶到凍結層以上並不斷打滾，凝結成冰粒。打滾過程冰粒不斷吸收水分，像＂雪球＂般越滾越大，最後當上升氣流不能再承托冰粒重量時，這些超重的冰粒便會跌落地面，形成落雹**[1]（圖二）。
    - ![圖二   強雷暴引致落雹的過程。](https://www.hko.gov.hk/tc/education/weather/weather-phenomena/images/fig_00550-Fig2_hailprocess.png){width=400}
  - 天氣雷達是天文台監測冰雹的主要工具，雷達圖所顯示的回波反射率及其形狀為識別冰雹提供了重要線索。冰雹形成於強雷暴中，在雷達圖中除了有很強的回波反射率外，有時甚至會出現一種呈＂鈎＂狀的回波結構，稱為「鈎狀回波」，如2014年3月30日晚上的**雷達圖便出現了清晰可見的鈎狀回波特徵**（圖三）。
    - ![](https://www.hko.gov.hk/tc/education/weather/weather-phenomena/images/fig_00550-Fig3_radar.png)
- [應用相控陣天氣雷達監測惡劣天氣 | 2023 | HKO](https://www.hko.gov.hk/tc/education/meteorological-instruments/weather-radar/00689-application-of-phased-array-weather-radar-in-monitoring-inclement-weather.html)
  - 「相控陣天氣雷達」(Phased Array Weather Radar (PAWR)) 是「相位控制陣列天氣雷達」的簡稱，它由眾多獨立控制的小型天線收發單元組成，並排列成天線陣面，**通過控制各單元發射無線電波的相位差來改變雷達波束的發射方向**。相控陣雷達的優點是體積細小，可同時發射多個波束，利用機械及電子掃描方法在最短1分鐘內完成分層最高達68層的立體掃描，比現有的大帽山及大老山天氣雷達分別6分鐘和12層的掃描加密達5至6倍，可提供時間和空間上更高解像度的雷達圖像，幫助監測快速演變的中尺度惡劣天氣，如局地暴雨、冰雹和龍捲風等。
  - 天文台位於大欖角的機場多普勒天氣雷達於2018年8月29日早上成功偵察到一個在馬灣附近發展的**水龍捲**。
- [『氣象冷知識』：新大帽山天氣雷達 | 2024 | HKO](https://www.hko.gov.hk/tc/%E6%9C%80%E6%96%B0%E6%B6%88%E6%81%AF/108809/%E3%80%8E%E6%B0%A3%E8%B1%A1%E5%86%B7%E7%9F%A5%E8%AD%98%E3%80%8F%EF%BC%9A%E6%96%B0%E5%A4%A7%E5%B8%BD%E5%B1%B1%E5%A4%A9%E6%B0%A3%E9%9B%B7%E9%81%94)
  - 自1999年起服務香港24年的大帽山天氣雷達已於去年底退役，**並於今年換上更先進的雙偏振S波段多普勒天氣雷達**。新雷達的安裝過程橫跨冬、春二季，天文台同事及工作人員從零下兩度的寒風，到潮濕大霧的春天，均日以繼夜緊守全港最高山峰辛勤工作，最終成功在雨季來臨前完成安裝新雷達，支援天文台監測惡劣天氣。最新一集『氣象冷知識』會帶大家前往大帽山雷達重地，由科學主任和雷達機械師揭開新雷達安裝的難忘歷程。
  - **香港天文台新大帽山天氣雷達已於2024年3月完成安裝，並正式投入業務運作**，提供重要的觀測數據監測各類惡劣天氣，包括雷暴、暴雨和熱帶氣旋。**新雷達是香港第二台雙偏振 S 波段多普勒天氣雷達**，可更精確監測冰雹和降雨率，並配備更先進的硬件和軟件，提供更高質量的雷達產品以支援天氣分析。新雷達與大老山雷達24 小時全天候協同運作來監測惡劣天氣。
  - <iframe width="560" height="315" src="https://www.youtube.com/embed/5fthr7X0mik" title="新大帽山天氣雷達" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
- [相控陣天氣雷達與低空天氣 | 2025 | HKO](https://www.hko.gov.hk/tc/education/meteorological-instruments/weather-radar/00729-Phased-Array-Weather-Radar-and-Low-Altitude-Weather.html)
  - **天文台於2021年在沙螺灣設置了一台相控陣天氣雷達 (Phased Array Weather Radar (PAWR))**，經過3年多的運作，證實能有效探測對本港具高影響的惡劣天氣，為未來籌建掃描範圍覆蓋全港的相控陣天氣雷達網絡打下堅實的科學基礎。
  - 沙螺灣相控陣天氣雷達可在**一分鐘內完成最高達68層的立體掃描，並提供空間解像度低至30米**的雷達圖像。相比之下，天文台兩台分別在**大帽山和大老山的長程雷達需6分鐘完成12層立體掃描，雷達圖像空間解像度為150米**。
  - 相比大帽山和大老山的長程天氣雷達，相控陣天氣雷達可以六分之一的時間及五倍的解像度完成對大氣的立體掃描，能有效捕捉快速變化的惡劣天氣如龍捲風、冰雹、強烈雷暴及其引發的強陣風。

## 課程

- [天氣雷達及衛星圖像基礎講座.pdf](https://www.hko.gov.hk/en/education/edu03course/coursenotes/files/radar_satellite_notes.pdf)
- [香港天氣雷達觀測五十周年紀念 | 1959-2009 ](https://www.hko.gov.hk/en/wxinfo/radars/radar_gallery/files/Radar-50years.pdf)
  - ![](https://i.imgur.com/jHCknCO.png){width=300}
- [氣象章課程 – 教導組（童軍支部）](https://www.meteam.org/lecture/tutors/tutor(v1).pdf)

## HKO Reprint

- [多普勒激光雷達在香港國際機場的應用 | 2023](https://www.hko.gov.hk/en/publica/reprint/files/r523.pdf)
- [Dual Doppler radar analysis of 3-dimensional winds for heavy rain events in southern China | 2008](https://www.hko.gov.hk/en/publica/reprint/files/r774.pdf)
- [利用 S 波段遠程天氣雷達作快速掃描監測暴雨及熱帶氣旋發展之初探 | 2018 年 1 月](https://www.hko.gov.hk/hko/publica/reprint/r1339.pdf)
- [利用大帽山天氣雷達快速掃瞄資料進行對流尺度循環集合同化 | 2019](https://www.hko.gov.hk/hko/publica/reprint/r1357.pdf)
- [多普勒激光雷達在熱帶氣旋方面的觀測](https://www.hko.gov.hk/tc/aviat/articles/files/17th_gd_hk_macao_article_2.pdf)
- [REMOTE-SENSING THE WEATHER TO SUPPORT THE HONG KONG AIRPORT](https://www.hko.gov.hk/hko/publica/reprint/r368.pdf)
- [The Hong Kong Observatory’s Operational Data Management Systems | 2009](https://www.ecmwf.int/sites/default/files/elibrary/2009/15178-hong-kong-observatorys-operational-data-management-systems.pdf)

## HKO Observations

- [HKO | 天氣雷達圖像 (64 公里範圍, 3 公里高)](https://www.hko.gov.hk/tc/wxinfo/radars/radar_range1.htm)
- [Radar Images from Hong Kong Observatory | HKUST](https://envf.ust.hk/dataview/hko_radar/current/)

## HKO Literature Review

- [Chan Y-w, Chan P-w, Cheung P. Observation of Downburst Associated with Intense Thunderstorms Encountered by an Aircraft at Hong Kong International Airport. Applied Sciences. 2025; 15(4):2223. https://doi.org/10.3390/app15042223](https://www.mdpi.com/2076-3417/15/4/2223)
  - Three-dimensional wind field retrieval has been conducted from the radars, and the wind data so obtained are compared with the vertical velocity and eddy dissipation rate measured onboard the aircraft during the encountering of two **microbursts**.
  - {% gi 5 3-2 %}
    ![](https://i.imgur.com/L1m8XkL.png)
    ![](https://i.imgur.com/PV1XcQV.png)
    ![](https://i.imgur.com/xjC2wWn.png)
    {% endgi %}
- [Yang, Z. W., P. W. Chan, Y. W. Chan, K. Zhao, H. Q. Chen, C. R. Chen, and Y. Y. Xu, 2025: Application of three-dimensional wind fields and dual-polarization signals of an X-band Phased Array Weather Radar in diagnosing vertical motion and cloud electrification in convective storms. Adv. Atmos. Sci., 42(5), 968−980,  https://doi.org/10.1007/s00376-024-3286-8.](http://www.iapjournals.ac.cn/aas/article/doi/10.1007/s00376-024-3286-8)
  - **香港天文台于2021年在沙螺湾 (Sha Lo Wan (SLW)) 气流剖析仪站部署了一部X波段相控阵天气雷达 (X-band dual-polarization Phased Array Weather Radar (PAWR) )**，以提升对香港高影响天气的监测能力。该相控阵雷达具备快速立体扫描能力，**可在1分钟内完成对目标区域的扫描，空间分辨率达到30米**。本研究利用沙螺湾相控阵雷达所提供的双偏振参数，包括差分反射率($Z_{DR}$) 、差传播相位常数（$K_{DP}$) 及粒子相态分类产品，结合数据变分同化技术构建的三维风场，对**中尺度对流风暴的垂直运动和闪电特性进行了深入分析**，并验证沙螺湾相控阵雷达的观测数据。本研究选取分别发生在2022年9月18日及2023年6月17日的两个中尺度对流风暴事件作为研究个例。
  - In addition to the **SLW PAWR**, **HKO’s S-band radars at Tai Mo Shan (TMS) and Tate’s Cairn (TC)**, as well as the **C-band Terminal Doppler Weather Radar (TDWR) at Brothers Point (BP)** and the dense automatic weather station (AWS) network operated by HKO are used for analysis in the aforementioned **two mesoscale convective storms (MCS) cases**. 
    - ![](https://i.imgur.com/L1m8XkL.png){width=400}
    - ![](https://i.imgur.com/o6Dh12x.png){width=400}

# 教育

- [全港最高的天氣雷達站——大帽山天氣雷達站 | 二零二四年十月 第一期 | 黃樂詩](https://www.hokoon.edu.hk/%E5%8D%9A%E7%89%A9%E5%A4%A9%E5%9C%B0/%E5%8D%9A%E7%89%A9%E7%89%B9%E5%AF%AB/radar-station/)
  - 大帽山天氣雷達站於1999年落成，其後運作了24年。於服務期間，雷達站因受到不少強颱風（如「山竹」、「天鴿」等）的破壞，再加上老化的零件已無法重新配對，天文台決定於2023年11月拆卸和更新原有的天氣雷達。**2024年3月，新的天氣雷達站完成安裝並投入運作。新的雷達站造價高達3100萬，是香港第二台雙偏振 S 波段多普勒天氣雷達。到現時為止，新的雷達站已投入運作超過半年**。
  - 雷達站的運作原理並不複雜。首先雷達站會透過天線，**按順時針旋轉的方向向天空發出無線電波（又稱電磁波）**。電磁波會經過大氣中的雨點或冰雹，我們可以由電磁波反射回來的信號得知大氣中是否有降雨。一般而言，反射的信號越強就代表雨勢越大。我們也可根據雷達接收到的訊號強度配以顏色說明，製成雷達圖。
  - 新的雷達站為**雙偏振多普勒天氣雷達**，那麼「雙偏振技術」又如何提供更佳的測量支援呢？當雷達探測時，可透過傳播時電場的振動方向（偏振）而改變電磁波的振動方向，令**電磁波對大氣可作垂直和水平的掃描**。這樣能更有效地探測扁平狀的雨點及其大小，從而更好地分辨是水滴還是冰雹。雷達需要向四方八面掃描，因此會選擇設置於開揚位置，大帽山雷達站位於全港最高的位置（961米），沒有受到遮擋也有利於提升測量的準確率。
  - {% gi 5 2-1-2%}
    ![](https://i.imgur.com/p8WVC3u.png)
    ![](https://i.imgur.com/RnfOiMP.png)
    ![](https://i.imgur.com/pvg5eYt.png)
    ![](https://i.imgur.com/tpiKQp8.png)
    ![](https://i.imgur.com/9d192KP.png)
    {% endgi %}


# WRF

## wrf_dbz

Calculates simulated equivalent radar reflectivity factor [dBZ] from WRF model output. [link](https://www.ncl.ucar.edu/Document/Functions/Built-in/wrf_dbz.shtml)

This function computes equivalent reflectivity factor [dBZ] at each model grid point assuming spherical particles of constant density, with exponential size distributions. This function is based on **“dbzcalc.f” in RIP**.

- `dbz = wrf_user_getvar(a,"dbz",-1)`  ; calculate dbz for all times in file
- `mdbz = wrf_user_getvar(a,"mdbz",-1)`  ; calculate max dbz for all times in file

# MPAS

- [src/core_atmosphere/physics/mpas_atmphys_driver_microphysics.F#L656](https://github.com/MPAS-Dev/MPAS-Model/blob/41e9a3fb8ca6b9250a7405209a5c60996318409f/src/core_atmosphere/physics/mpas_atmphys_driver_microphysics.F#L656)

```fortran
!MPAS driver for parameterization of cloud microphysics processes.
!Laura D. Fowler (send comments to laura@ucar.edu).
!2013-05-01.
!
! subroutines in mpas_atmphys_driver_microphysics:
! ------------------------------------------------
! allocate_microphysics     : allocate local arrays for parameterization of cloud microphysics.
! deallocate_microphysics   : deallocate local arrays for parameterization of cloud microphysics.
! microphysics_init         : initialization of individual cloud microphysics schemes.
! driver_microphysics       : main driver (called from mpas_atm_time_integration).
! precip_from_MPAS          : initialize timestep local arrays for precipitation.
! precip_to_MPAS            : copy local arrays to MPAS arrays.
! compute_radar_reflectivity: compute radar reflectivities.
! compute_relhum            : compute relative humidity.
!
! WRF physics called from microphysics_driver:
! --------------------------------------------
! * module_mp_kessler : Kessler cloud microphysics.
! * module_mp_thompson: Thompson cloud microphysics.
! * module_mp_wsm6    : WSM6 cloud microphysics.
...
       do j = jts,jte
       do i = its,ite
          do k = kts,kte
             p1d(k) = pres_p(i,k,j)
             t1d(k) = th_p(i,k,j) * pi_p(i,k,j)
             qv1d(k)  = qv_p(i,k,j)
             qr1d(k)  = qr_p(i,k,j)
             qs1d(k)  = qs_p(i,k,j)
             qg1d(k)  = qg_p(i,k,j)
             dBZ1d(k) = -35._RKIND
             zp(k) = z_p(i,k,j) - z_p(i,1,j) + 0.5*dz_p(i,k,j) ! height AGL
          enddo

          call refl10cm_wsm6(qv1d,qr1d,qs1d,qg1d,t1d,p1d,dBZ1d,kts,kte)

          kp = 1
          do k = kts,kte
             dBZ1d(k) = max(-35._RKIND,dBZ1d(k))
             if(zp(k) .lt. 1000.) kp = k
          enddo
          refl10cm_max(i) = maxval(dBZ1d(:))
          w1 = (zp(kp+1)-1000.)/(zp(kp+1)-zp(kp))
          w2 = 1.0 - w1
          refl10cm_1km(i) = w1*dBZ1d(kp) + w2*dBZ1d(kp+1)
          refl10cm_1km_max(i) = max(refl10cm_1km_max(i),refl10cm_1km(i))
       enddo
       enddo
...
```

```xml
<var name="refl10cm_max" type="real" dimensions="nCells Time" units="dBZ"
        description="10 cm maximum radar reflectivity"
        packages="mp_thompson_in;mp_thompson_aers_in;mp_wsm6_in"/>

<var name="refl10cm_1km" type="real" dimensions="nCells Time" units="dBZ"
        description="diagnosed 10 cm radar reflectivity at 1 km AGL"
        packages="mp_thompson_in;mp_thompson_aers_in;mp_wsm6_in"/>

<var name="refl10cm_1km_max" type="real" dimensions="nCells Time" units="dBZ"
        description="maximum diagnosed 10 cm radar reflectivity at 1 km AGL since last output time"
        packages="mp_thompson_in;mp_thompson_aers_in;mp_wsm6_in"/>
```

{% fold info @mpas_atmphys_driver_microphysics.F#L656 %}
```fortran
!=================================================================================================================
 subroutine compute_radar_reflectivity(configs,diag_physics,its,ite)
!=================================================================================================================

!input arguments:
 type(mpas_pool_type),intent(in):: configs
 integer,intent(in):: its,ite

!inout arguments:
 type(mpas_pool_type),intent(inout):: diag_physics

!local pointers:
 character(len=StrKIND),pointer:: microp_scheme
 real(kind=RKIND),dimension(:),pointer:: refl10cm_max,refl10cm_1km,refl10cm_1km_max

!local variables and arrays:
 integer:: i,j,k,kp
 real(kind=RKIND),dimension(:),allocatable:: qv1d,qc1d,qr1d,qs1d,qg1d,t1d,p1d,nr1d,dBZ1d,zp
 real(kind=RKIND):: w1,w2

!-----------------------------------------------------------------------------------------------------------------

 call mpas_pool_get_config(configs,'config_microp_scheme',microp_scheme)

 call mpas_pool_get_array(diag_physics,'refl10cm_max',refl10cm_max)
 call mpas_pool_get_array(diag_physics,'refl10cm_1km',refl10cm_1km)
 call mpas_pool_get_array(diag_physics,'refl10cm_1km_max',refl10cm_1km_max)

 microp_select: select case(trim(microp_scheme))
    case ("mp_kessler")
       call physics_error_fatal('--- calculation of radar reflectivity is not available' // &
                                 'with kessler cloud microphysics')

    case ("mp_wsm6")
       if(.not.allocated(p1d)  ) allocate(p1d(kts:kte)  )
       if(.not.allocated(t1d)  ) allocate(t1d(kts:kte)  )
       if(.not.allocated(qv1d) ) allocate(qv1d(kts:kte) )
       if(.not.allocated(qr1d) ) allocate(qr1d(kts:kte) )
       if(.not.allocated(qs1d) ) allocate(qs1d(kts:kte) )
       if(.not.allocated(qg1d) ) allocate(qg1d(kts:kte) )
       if(.not.allocated(dBz1d)) allocate(dBZ1d(kts:kte))
       if(.not.allocated(zp)   ) allocate(zp(kts:kte)   )

       do j = jts,jte
       do i = its,ite
          do k = kts,kte
             p1d(k) = pres_p(i,k,j)
             t1d(k) = th_p(i,k,j) * pi_p(i,k,j)
             qv1d(k)  = qv_p(i,k,j)
             qr1d(k)  = qr_p(i,k,j)
             qs1d(k)  = qs_p(i,k,j)
             qg1d(k)  = qg_p(i,k,j)
             dBZ1d(k) = -35._RKIND
             zp(k) = z_p(i,k,j) - z_p(i,1,j) + 0.5*dz_p(i,k,j) ! height AGL
          enddo

          call refl10cm_wsm6(qv1d,qr1d,qs1d,qg1d,t1d,p1d,dBZ1d,kts,kte)

          kp = 1
          do k = kts,kte
             dBZ1d(k) = max(-35._RKIND,dBZ1d(k))
             if(zp(k) .lt. 1000.) kp = k
          enddo
          refl10cm_max(i) = maxval(dBZ1d(:))
          w1 = (zp(kp+1)-1000.)/(zp(kp+1)-zp(kp))
          w2 = 1.0 - w1
          refl10cm_1km(i) = w1*dBZ1d(kp) + w2*dBZ1d(kp+1)
          refl10cm_1km_max(i) = max(refl10cm_1km_max(i),refl10cm_1km(i))
       enddo
       enddo

       if(allocated(p1d)  ) deallocate(p1d  )
       if(allocated(t1d)  ) deallocate(t1d  )
       if(allocated(qv1d) ) deallocate(qv1d )
       if(allocated(qr1d) ) deallocate(qr1d )
       if(allocated(qs1d) ) deallocate(qs1d )
       if(allocated(qg1d) ) deallocate(qg1d )
       if(allocated(dBz1d)) deallocate(dBZ1d)
       if(allocated(zp)   ) deallocate(zp   )

    case ("mp_thompson","mp_thompson_aerosols")
       if(.not.allocated(p1d)  ) allocate(p1d(kts:kte)  )
       if(.not.allocated(t1d)  ) allocate(t1d(kts:kte)  )
       if(.not.allocated(qv1d) ) allocate(qv1d(kts:kte) )
       if(.not.allocated(qc1d) ) allocate(qc1d(kts:kte) )
       if(.not.allocated(qr1d) ) allocate(qr1d(kts:kte) )
       if(.not.allocated(qs1d) ) allocate(qs1d(kts:kte) )
       if(.not.allocated(qg1d) ) allocate(qg1d(kts:kte) )
       if(.not.allocated(nr1d) ) allocate(nr1d(kts:kte) )
       if(.not.allocated(dBz1d)) allocate(dBZ1d(kts:kte))
       if(.not.allocated(zp)   ) allocate(zp(kts:kte)   )

       do j = jts,jte
       do i = its,ite
          do k = kts,kte
             p1d(k) = pres_p(i,k,j)
             t1d(k) = th_p(i,k,j) * pi_p(i,k,j)
             qv1d(k)  = qv_p(i,k,j)
             qc1d(k)  = qc_p(i,k,j)
             qr1d(k)  = qr_p(i,k,j)
             qs1d(k)  = qs_p(i,k,j)
             qg1d(k)  = qg_p(i,k,j)
             nr1d(k)  = nr_p(i,k,j)
             dBZ1d(k) = -35._RKIND
             zp(k) = z_p(i,k,j) - z_p(i,1,j) + 0.5*dz_p(i,k,j) ! height AGL
          enddo

          call calc_refl10cm(qv1d,qc1d,qr1d,nr1d,qs1d,qg1d,t1d,p1d,dBZ1d,kts,kte,i,j)

          kp = 1
          do k = kts,kte
             dBZ1d(k) = max(-35._RKIND,dBZ1d(k))
             if(zp(k) .lt. 1000.) kp = k
          enddo
          refl10cm_max(i) = maxval(dBZ1d(:))
          w1 = (zp(kp+1)-1000.)/(zp(kp+1)-zp(kp))
          w2 = 1.0 - w1
          refl10cm_1km(i) = w1*dBZ1d(kp) + w2*dBZ1d(kp+1)
          refl10cm_1km_max(i) = max(refl10cm_1km_max(i),refl10cm_1km(i))
       enddo
       enddo

       if(allocated(p1d)  ) deallocate(p1d  )
       if(allocated(t1d)  ) deallocate(t1d  )
       if(allocated(qv1d) ) deallocate(qv1d )
       if(allocated(qc1d) ) deallocate(qc1d )
       if(allocated(qr1d) ) deallocate(qr1d )
       if(allocated(qs1d) ) deallocate(qs1d )
       if(allocated(qg1d) ) deallocate(qg1d )
       if(allocated(nr1d) ) deallocate(nr1d )
       if(allocated(dBz1d)) deallocate(dBZ1d)
       if(allocated(zp)   ) deallocate(zp   )

    case default
 end select microp_select

 end subroutine compute_radar_reflectivity
```
{% endfold %}

![](https://i.imgur.com/jMUSCCy.png)

```ncl
else if (variable_plotted .eq. "refl10cm_max") then
    res@cnLevelSelectionMode = "ExplicitLevels"   ; set explicit contour levels
    ;color = "WhiteBlueGreenYellowRed"
    color = (/"#ffffff", "#01A0F6", "#07EBEC", "#6DFA3D", "#00D806", "#019000", "#FFFF00", "#E7BF00", "#FF9000", "#FF0000", "#D60001", "#C00000", "#FF00EF", "#9601B4", "#AD90F0"/) ; Total = 1+14
    res@cnLevels    = (/5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70 /)   ; set levels for CMA
```

# Perplexity 的回答

The key equations and concepts related to weather radar, especially for **S-band and C-band radars, radar reflectivity (Z), dBZ, and raindrop diameter** are as follows:

## Radar Reflectivity Factor (Z)

- Radar reflectivity factor $Z$ is a measure of the total power returned to the radar by precipitation particles in a unit volume. It is proportional to the sum of the sixth power of the diameters of all raindrops in that volume:

$$
Z = \sum D_i^6
$$

where $D_i$ is the diameter of the $i$-th drop (in mm), and the sum is over all drops in 1 cubic meter volume[^2].

- $Z$ has units of mm$^6$/m$^3$.

- Because $Z$ depends on $D^6$, larger drops dominate the reflectivity.

## Conversion to Decibels (dBZ)

- Reflectivity in decibels is given by:

$$
\text{dBZ} = 10 \log_{10} \left(\frac{Z}{Z_0}\right)
$$

where $Z_0 = 1 \text{ mm}^6/\text{m}^3$ is the reference reflectivity[^1][^5].

## Radar Equation (Simplified)

- The radar reflectivity factor $$ Z $$ can be related to the radar received power $P_r$, range $r$, and radar constants by:

$$
Z = c_1 \frac{P_r r^2}{|K|^2}
$$

where $c_1$ is a radar-specific constant, and $|K|^2$ is the dielectric factor (about 0.93 for liquid water)[^2][^3].

## S-band and C-band Radar Wavelengths

- **S-band radar operates at wavelengths around 7.5 to 15 cm (about 10 cm typical)**, suitable for detecting precipitation with long range (~500 km)[^4][^5].

- **C-band radar operates at shorter wavelengths, typically around 5 cm**, used for moderate range precipitation detection[^3].

## Diameter of Water Droplets and Reflectivity

- The diameter of water droplets can be estimated from the liquid water content and number concentration of drops, assuming equal size drops:

$$
D = \left(\frac{6 \times \text{LWC}}{\pi \rho N}\right)^{1/3}
$$

where **LWC** is liquid water content (kg/m$^3$), $\rho$ is water density (~1000 kg/m$^3$), and $N$ is number of drops per m$^3$[^5].

- Using this diameter, the reflectivity $Z$ can be computed as:

$$
Z = N \times D^6
$$

and then converted to **dBZ**.

---

### Summary Table

| Parameter               | Formula / Value                                  | Notes                                  |
|------------------------|-------------------------------------------------|----------------------------------------|
| Radar reflectivity $Z$ | $Z = \sum D_i^6$ (mm$^6$/m$^3$)          | Sum over all drops in 1 m$^3$        |
| dBZ                    | $10 \log_{10}(Z / 1)$                        | Reference $Z_0 = 1$ mm$^6$/m$^3$ |
| Radar equation          | $Z = c_1 \frac{P_r r^2}{|K|^2}$              | $P_r$: received power, $r$: range  |
| S-band wavelength       | 7.5 to 15 cm (typ. ~10 cm)                       | Long range detection (~500 km)          |
| C-band wavelength       | ~5 cm                                            | Moderate range detection                 |
| Drop diameter $D$     | $\left(\frac{6 \times \text{LWC}}{\pi \rho N}\right)^{1/3}$ | LWC = liquid water content, $N$ = drop number concentration |

These equations form the basis for interpreting radar returns in terms of precipitation intensity and drop size distributions in weather radar systems.

References:[^1][^2][^3][^4][^5]

Citations:

[^1]: <https://www.jma.go.jp/jma/en/photogallery/WMO-ASEAN_Radar_Workshop_Jan2024/3_2_Basics%20of%20Weather%20Radar.pdf>
[^2]: <https://wx.erau.edu/faculty/mullerb/Wx365/Radar_equation/radar_equation.pdf>
[^3]: <https://www.arm.gov/publications/tech_reports/handbooks/csapr_handbook.pdf>
[^4]: <https://severeweather.wmo.int/TCFW/RAIV_Workshop2021/19_WeatherRadarPrinciples_StacySteward.pdf>
[^5]: <https://geo.libretexts.org/Bookshelves/Meteorology_and_Climate_Science/Practical_Meteorology_(Stull)/08:_Satellites_and_Radar/8.02:_Weather_Radars>
[^6]: <https://www.mdpi.com/2673-4931/19/1/23>
[^7]: <https://glossary.ametsoc.org/wiki/Radar_reflectivity_factor>
[^8]: <https://mountainscholar.org/bitstreams/8520e1c0-d080-46da-abd9-c135e2408c39/download>
[^9]: <https://journals.ametsoc.org/view/journals/apme/23/8/1520-0450_1984_023_1258_errffs_2_0_co_2.pdf>
[^10]: <https://blogs.millersville.edu/adecaria/files/2021/11/esci340-lesson10_radar.pdf>
[^11]: <https://hess.copernicus.org/articles/5/615/2001/hess-5-615-2001.pdf>
[^12]: <https://ams.confex.com/ams/pdfpapers/95684.pdf>
[^13]: <https://www.imdpune.gov.in/training/icitc/LN_11_57_Radar%20Theory.pdf>
[^14]: <https://www.noaa.gov/jetstream/reflectivity>
[^15]: <https://imdpune.gov.in/training/icitc/LN_11_57_Radar%20Theory.pdf>
[^16]: <http://jmr.cmsjournal.net/article/doi/10.1007/s13351-018-7152-4>
[^17]: <https://www.radartutorial.eu/15.weather/wr50.en.html>
[^18]: <https://jtm.itmm.org.cn/en/article/doi/10.46267/j.1006-8775.2020.025>
[^19]: <https://www.eecweathertech.com/pdf/EEC-The-Chicago-storm.pdf>
[^20]: <https://agupubs.onlinelibrary.wiley.com/doi/full/10.1029/2022GL099332>

