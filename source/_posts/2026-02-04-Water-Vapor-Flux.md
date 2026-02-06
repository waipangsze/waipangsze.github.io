---
layout: post
title: Water Vapor Flux | 水汽通量
categories: [Meteorology]
tags: [NWP, WRF, MPAS, Water Vapor Budget Equation, Water Vapor Flux, Precipitable Water]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2026-02-04 06:31:00
index_img: https://i.imgur.com/Ur9oZcE.png
banner_img: https://i.imgur.com/Ur9oZcE.png
---

# Water Vapor Budget Equation (moisture budget equation)

The water vapor budget equation (moisture budget equation) dictates that the local change in atmospheric moisture **over an area** equals evaporation minus precipitation and net horizontal moisture transport.

The Water Vapor Budget Equation (moisture budget equation) relates precipitable water $(PW)$ and water vapor flux divergence $\nabla \cdot \mathbf{Q}$ to precipitation $(P)$ and evaporation $(E)$: 

$$\frac{\partial PW}{\partial t}+\nabla \cdot \mathbf{Q}=E-P$$ 

The divergence $\nabla \cdot \mathbf{Q}$ represents the net transport of moisture out of a column, with negative values (convergence) associated with increased rainfall, and positive values with moisture export. 

- **Precipitable Water** $(PW)$: The total vertically integrated water vapor in a column of atmosphere, defined as 
$$PW=\frac{1}{g}\int _{p_{t}}^{p_{s}}q\,dp$$
where $q$ is specific humidity, $g$ is acceleration of gravity, $p_{s}$ is surface pressure, and $p_{t}$ is top pressure.
  - Unit of $PW$ = $kg/m^2$

- **Water Vapor Flux** $\mathbf{Q}$: The vertically integrated horizontal moisture flux vector is 

$$\mathbf{Q}=\frac{1}{g}\int _{p_{t}}^{p_{s}}q\mathbf{v}\,dp$$

where $\mathbf{v}$ is the horizontal wind vector. 

- **Divergence (Moisture Flux Divergence)** $\nabla \cdot \mathbf{Q}$: The horizontal divergence of the vapor flux, 
$$\nabla \cdot \mathbf{Q}=\frac{\partial Q_{x}}{\partial x}+\frac{\partial Q_{y}}{\partial y}$$. 
  - Positive divergence $\nabla \cdot \mathbf{Q}>0$ signifies net drying or export; 
  - Negative divergence (convergence, $\nabla \cdot \mathbf{Q}<0$ signifies moisture import, usually leading to increased $P$.

- **Time-Averaged Balance**: Over long periods (e.g., monthly), the storage term $\frac{\partial W}{\partial t}$ is often neglected, leading to $E-P \approx \nabla \cdot \mathbf{Q}$. This formula is fundamental for studying the hydrological cycle, water vapor budgets, and determining how moisture transport drives precipitation. 

---

# 水汽通量 (Water Vapor Flux)

在氣象學中，水汽通量是衡量水汽隨大氣運動而輸送的關鍵指標。您可以將其理解為大氣中的「隱形河流」。

## 1. 核心定義與公式

**水汽通量**是一個向量，定義為在單位時間內，流經垂直於氣流方向的單位面積的水汽質量。

其數學表達式（通常考慮水平方向）為：

$$\vec{F} = \frac{1}{g} q \vec{V}$$

* **q (比濕)**：空气中水分的含量。
* **$\vec{V}$ (風速向量)**：水汽移動的速度與方向。
* **g (重力加速度)**：用於計算單位截面積下整層大氣的質量。

## 2. 為什麼氣象預報總提到它？

在天氣分析中，單純有「濕空氣」是不夠的，必須要有**水汽的持續輸送**，才能支撐強降雨。

* **水汽來源 (Moisture Source)**：透過分析水汽通量，科學家可以追蹤降雨的水分來自哪裡。例如，台灣梅雨季節的水汽主要來自南海或印度洋。
* **大氣河流 (Atmospheric Rivers)**：當水汽通量極度集中成長條狀時，被稱為「大氣河流」，這通常是造成極端洪澇的主要原因。

## 3. 水汽通量散度 (Water Vapor Flux Divergence)

這是判斷會不會下雨的「金標準」：

1. **輻合 (Convergence, 負值)**：水汽在某地「堆積」。這就像河流匯入湖泊，水汽無處可去只能上升凝結，通常預示著**強降雨**。
2. **輻散 (Divergence, 正值)**：水汽從某地「流出」。即使當地很潮濕，但如果水汽都在往外跑，也很難維持長久的降雨。

## 4. 關鍵指標：整層積分水汽輸送 (IVT)

在研究颱風或梅雨時，科學家最常用的是 **IVT (Integrated Vapor Transport)**。它將從地面到大氣頂層的所有水汽通量加總，能更全面地反映整個大氣柱的水汽輸送強度。

## 總結

* **有水汽通量**：代表水汽正在「路過」。
* **有水汽通量輻合**：代表水汽正在「集合」，這是下大雨的前兆。

---

# 可降水量 (Precipitable Water)

- [Precipitable Water (PW)](https://misva.aeris-data.fr/en/precipitable-water-pw/)
- [Convert Water Vapor Mixing Ratio to Precipitable Water Vapor | NASA](https://www.earthdata.nasa.gov/learn/tutorials/convert-water-vapor-mixing-ratio-precipitable-water-vapor)
- [Atmospheric Water Vapor - Precipitable Water. (April 8, 2013).](https://annarborearthscience.weebly.com/uploads/1/3/9/7/13972856/9cl1.pdf)

<iframe width="560" height="315" src="https://www.youtube.com/embed/_SGvEnN4FsA" title="Precipitable Water Definition" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

**Precipitable water (PW)** is the depth of water in a column of the atmosphere, if all the water in that column were precipitated as rain. As a depth, the precipitable water is measured in millimeters or inches. The term is often abbreviated as "TPW", for Total Precipitable Water.

**Precipitable water** is defined as the integral of the water vapour content of the atmospheric column and is expressed in mm. It is given by the relationship below where q represents the specific humidity of the atmosphere (kg/kg), p the pressure (Pa) and g the gravity. The integral is calculated between the surface and the top of the atmosphere. In short, PW represents the amount of precipitation that would be produced if all the water vapour in a column of air were to condense.

$$
P = \frac{1}{g} \int q dp \approx \frac{1}{g} \int q_v dp
$$

- $q$ represents the specific humidity of the atmosphere (kg/kg)
- $q_v$ represents the mixing ratio of water vapor of the atmosphere (kg/kg)

$$
q = \frac{q_v}{1+q_v}
$$

For numerical integration using sounding layers, the total precipitable water is calculated by summing layers:
$$
\text{PW}=\frac{1}{g}\sum _{i=1}^{n} \bar{r_i} \Delta p_{i}
$$
where $\bar{r_i}$ is the **mean** mixing ratio and $\Delta p_{i}$ is the pressure thickness of layer $i$ (e.g., in $100\,hPa$ steps).

## Units and Conversion 

- $1\,kg/m^{2}$ of water is equivalent to $1\,mm$ of liquid water depth. 
- Therefore, the integral of water vapor in $kg/m^{2}$ gives the depth directly in $mm$. 

---

# 計算**水汽通量散度 (Water Vapor Flux Divergence, WVFD)**

- **Moisture Flux Divergence**

計算**水汽通量散度 (Water Vapor Flux Divergence, WVFD)** 是降水診斷中最關鍵的一步。它結合了「水汽含量」與「風場動力」，能直接指出大氣中水汽在哪裡匯聚（成雨）或枯竭。

在數值模型（如 WRF）的後處理中，計算過程通常分為以下三個層次：

## 1. 數學定義

水汽通量散度  $\nabla \cdot (q\vec{V})$   表示單位體積內水汽質量的變化率。

* **二維水平散度公式：**

$$\text{VFD} = \frac{\partial (qu)}{\partial x} + \frac{\partial (qv)}{\partial y}$$

* **整層積分散度 (VIMD)：**
這是預報中最常用的，代表整根氣柱的水汽匯聚情況：

$$\text{VIMD} = \int_{P_{top}}^{P_{sfc}} \nabla \cdot (q\vec{V}) \frac{dp}{g}$$

**判斷標準：**

- **負值 ($\text{VFD} < 0$)：** 稱為**輻合 (Convergence)**。水汽在此堆積，若配合上升運動，極易產生降雨。
- **正值 ($\text{VFD} > 0$)：** 稱為**輻散 (Divergence)**。水汽向外流散，天氣通常晴朗或降雨減弱。

## 2. WRF/MPAS 模型中的計算步驟

如果你手頭有 `wrfout` 數據，計算步驟如下：

### **Step 1: 提取並轉換變量**

* **比濕 ($q$)**：WRF 輸出的是混合比 `QVAPOR` ($w$)，需轉換： $q = \frac{w}{1+w}$。
* **風場 ($u, v$)**：將 $U$ 和 $V$ 從交錯網格 (Staggered Grid) 插值到質量網格中心。
* **單位統一**：確保單位為 $kg/kg$ 和 $m/s$。

### **Step 2: 計算通量分量**

計算每一個網格點上的水平輸送：

- $Flux_x = q \cdot u$
- $Flux_y = q \cdot v$

### **Step 3: 計算導數（散度）**

在球座標系或投影座標系（如 Lambert）下，不能簡單地用 $\Delta x$ ，需考慮地圖投影算子 ($m$)：

$$\text{VFD} = m^2 \left[ \frac{\partial}{\partial x} \left( \frac{qu}{m} \right) + \frac{\partial}{\partial y} \left( \frac{qv}{m} \right) \right]$$

*在多數後處理工具（如 NCL 或 Python MetPy）中，已有函數自動處理投影縮放。*

## 3. 診斷要點：為什麼有時候「散度」對不上「降水」？

在分析 WRF 結果時，你會發現有時候水汽輻合很強，卻沒下雨。請檢查以下兩點：

1. **垂直運動 ($w$)**：水汽匯聚了，但如果大氣穩定（沒有上升動力），水汽只會在地表堆積成霧或雲，不會變成雨。
2. **層位選擇**：通常看 **850 hPa** 或 **925 hPa** 的低層輻合，因為絕大部分水汽集中在邊界層。

---

# WRF

- [Output advection tendency of moisture variables](https://forum.mmm.ucar.edu/threads/output-advection-tendency-of-moisture-variables.8077/)
- [Weather Research and Forecasting (WRF) model with moisture tracers](https://github.com/damianinsua/WRF-WVTs)
  - The github branch you posted is from a WRF user, and we (UCAR) don't support codes specifically developed by individual users.

# MPAS

- [Yang, Q., L. R. Leung, S. A. Rauscher, T. D. Ringler, and M. A. Taylor, 2014: **Atmospheric Moisture Budget and Spatial Resolution Dependence of Precipitation Extremes in Aquaplanet Simulations**. J. Climate, 27, 3565–3581, https://doi.org/10.1175/JCLI-D-13-00468.1.](https://journals.ametsoc.org/view/journals/clim/27/10/jcli-d-13-00468.1.xml)
- [Núñez Ocasio, K. M., & Rios‐Berrios, R. (2023). African easterly wave evolution and tropical cyclogenesis in a Pre‐Helene (2006) hindcast using the model for prediction across scales‐atmosphere (MPAS‐A). Journal of Advances in Modeling Earth Systems, 15(2), e2022MS003181.](https://agupubs.onlinelibrary.wiley.com/doi/pdfdirect/10.1029/2022MS003181)
- [Cheng, Y., Lu, Y., Zhou, P., & Tang, J. (2026). Variable Resolution Simulations Over the Tibetan Plateau: Insights From MPAS‐A Transitioning Across Convective‐Permitting, Gray Zone, and Quasi‐Hydrostatic Scales. Journal of Geophysical Research: Atmospheres, 131(3), e2025JD044470.](https://agupubs.onlinelibrary.wiley.com/doi/abs/10.1029/2025JD044470)
- [MPAS moisture tracking? | Dec 24, 2025](https://forum.mmm.ucar.edu/threads/mpas-moisture-tracking.24835/)
  - As far as I know, MPAS doesn't have the capability to track water vapor.

```xml
<var name="precipw" type="real" dimensions="nCells Time" units="kg m^{-2}"
        description="precipitable water"/>
```

- [2015 | Modifications to add surface fields (u10, v10, t2, q2), plus precipw, to initial condition file and initial model history file.](https://github.com/MPAS-Dev/MPAS-Model/commit/d173db962118560efdb62a79c6f0f85deafac501)
  - Other minor changes included in this commit:
    - rename t2m to t2, th2m to th2 in atmosphere core
    - improvements to horizontal interpolation of meteorological fields in init_atmosphere core

```fortran
src/core_init_atmosphere/mpas_init_atm_cases.F
      real (kind=RKIND), dimension(:,:), pointer :: rw
      real (kind=RKIND), dimension(:), pointer :: precipw
      real (kind=RKIND), dimension(:,:), pointer :: uReconstructX
      call mpas_pool_get_array(diag, 'rho', rho)
      call mpas_pool_get_array(diag_physics, 'precipw', precipw)
      call mpas_pool_get_array(diag, 'uReconstructX', uReconstructX)

      !
      ! Calculation of the initial precipitable water:
      ! 
      do iCell = 1,nCells
         precipw(iCell) = 0.0
         do k = 1,nVertLevels
            precipw(iCell) = precipw(iCell) + rho_zz(k,iCell)*scalars(index_qv,k,iCell)*(zgrid(k+1,iCell)-zgrid(k,iCell))
         end do
      end do
```

```fortran
src/core_atmosphere/physics/mpas_atmphys_driver_microphysics.F
 real(kind=RKIND),pointer:: config_bucket_rainnc
 real(kind=RKIND),dimension(:),pointer:: precipw
 real(kind=RKIND),dimension(:),pointer:: graupelnc,rainnc,snownc
 call mpas_pool_get_array(diag_physics,'i_rainnc'  ,i_rainnc  )
 call mpas_pool_get_array(diag_physics,'precipw'   ,precipw   )
 call mpas_pool_get_array(diag_physics,'graupelnc' ,graupelnc )

!variables common to all cloud microphysics schemes:
 do j = jts,jte
 do i = its,ite

    !precipitable water:
    do k = kts,kte
       rho_a = rho_p(i,k,j) / (1._RKIND + qv_p(i,k,j))
       precipw(i) = precipw(i) + qv_p(i,k,j) * rho_a * dz_p(i,k,j)
    enddo
```

# References

1. [施永年, 刘惠兰, and 马开玉. "我国东部夏季大气中的水汽通量散度场." 南京气象学院学报 1 (1982).](http://dqkxxb.ijournals.cn/dqkxxb/article/abstract/19820113?st=article_issue)
2. [周冠博, 柳龙生, and 李兴宇. "水汽通量散度分解在一次台风暴雨中的应用." 气象 49.6 (2023): 671-681.](http://qxqk.nmc.cn/html/2023/6/20230603.html)