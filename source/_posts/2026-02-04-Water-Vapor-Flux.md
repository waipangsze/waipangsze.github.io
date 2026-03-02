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

# 基本輸送方程

在流體力學與氣象學中，水汽輸送方程是由基本輸送方程（General Transport Equation）推導而來的。

我們可以透過雷諾輸送定理（Reynolds Transport Theorem），將特定物理量（此處為水汽質量）隨時間的變化，分解為區域變化項與平流輸送項。

# 1. 基本輸送方程的通用形式 

基本輸送方程的通用形式 $\phi$ 對於任一單位質量的純量屬性 ，其在流場中的收支方程為：

$$
\frac{\partial \rho \phi}{\partial t} + \nabla \cdot (\rho \phi \vec{V}) = S
$$

- $\rho$: 流體（空氣）密度
- $\vec{V}$ : 風向量
- $S$: 來源或消失項（Source or Sink）

# 2. 水汽輸送方程的推導

當我們將 $\phi$ 替換為比濕 $q$（單位質量濕空氣中所含的水汽質量），並考慮大氣的連續性方程（質量守恆），我們可以得到水汽連續方程。

### 形式一：單位體積的水汽通量（局部變化形式）
在固定空間點觀測水汽含量的變化：

$$
\frac{\partial \rho q}{\partial t}  = - \nabla \cdot (\rho q \vec{V}) + (E - C)
$$

- $\frac{\partial \rho q}{\partial t}$：該點水汽密度的隨時間變化率。
- $\nabla \cdot (\rho q \vec{V})$：水汽通量散度。這是輸送的核心，代表水汽經由風力的匯聚（輻合）或散開（輻散）。
- $E - C$：源匯項。
  - $E$ 為蒸發（Evaporation），
  - $C$ 為凝結（Condensation）。

### 形式二：氣壓坐標系下的水平輸送（氣象預報常用）

由於氣象觀測多在氣壓層（如 850hPa）進行，利用靜力學方程替換密度，可得到常用的水平水汽輸送向量 $\vec{Q}$：

$$
\vec{Q} = \frac{1}{g} q \vec{V_h}
$$

## 四個組成項的來源與物理意義

根據上述方程，水汽輸送的強弱由以下四個關鍵維度決定：

| 項次 | 符號     | 物理量名稱                   | 來源與作用                                                                         |
|------|----------|------------------------------|------------------------------------------------------------------------------------|
| 1    | $q$        | 比濕 (Specific Humidity)     | 熱力學來源：來自下墊面（海洋）的蒸發。決定了「有多少水汽可供輸送」。               |
| 2    | $\vec{V_h}$        | 水平風向量 (Horizontal Wind) | 動力學來源：由氣壓梯度力驅動（如副高與氣旋間的南風）。決定了「輸送的方向與速度」。 |
| 3    | $\nabla \cdot (..)$        | 通量散度 (Flux Divergence)   | 幾何結構來源：風場的空間變化。若風速減慢或風向輻合，水汽會在此堆積成霧。           |
| 4    | $dp$ (或 $p$) | 垂直氣壓厚度                 | 空間權重來源：定義了輸送發生的層位。華南春季雨霧主要發生在底層（1000-850hPa）。    |


## 華南地區的春天（約 2 月至 4 月）常被形容為「回南天」，那種空氣濕到能擰出水、牆壁「冒汗」的景象，背後其實是一場複雜的大氣物理博弈。

以下為你拆解華南春天初期雨霧形成的機制與過程：

1. **核心形成機制：暖濕氣流與冷墊的對抗**
華南春季雨霧的本質是變性冷高壓與加強的南風（暖濕氣流）之間的交鋒。

- 冷空氣的遺留（冷墊）：
冬末春初，冷空氣勢力雖然減弱，但仍有殘餘的冷高壓脊控制華南。地表與近地層大氣經過一個冬天的冷卻，溫度較低。
- 暖濕氣流的強勢回歸：
隨著副熱帶高壓北抬或南海低壓槽活動，來自南海及孟加拉灣的暖濕氣流（偏南風）開始大規模北上。
- 逆溫層的形成：
當溫暖、潮濕的空氣爬升在較冷、較重的殘餘冷空氣之上時，會形成一個「下冷上暖」的逆溫層。這個逆溫層像一個巨大的「蓋子」，將水汽封鎖在近地面，極易冷凝成霧或低雲。

2. **兩大主要天氣現象：平流霧與回南天**

- **A. 平流霧 (Advection Fog)**
這是華南春季最常見的霧。
水汽過飽和： 暖濕空氣在經過寒冷的海面或地面時，底層大氣溫度迅速下降至露點以下。
凝結成霧： 空氣中的水汽凝結成微小水滴，隨風飄向陸地。這就是為什麼沿海城市（如香港、澳門、珠海）常出現「霧鎖大霧山」或「海霧入城」的奇觀。
- **B. 回南天 (The "Sweating" Walls)**
這是一種特殊的冷凝現象。
熱力滯後性： 建築物牆壁、地板在冬天被徹底冷卻。當極端潮濕的暖空氣突然襲來時，固體表面的溫度仍低於空氣露點。
物理過程： 暖空氣接觸到冰冷的表面，直接在物體表面液化，形成密集的珠水。這並非從牆內滲水，而是空氣中的水汽「抓」住了牆面的冷。

3. **雨的形成：地形抬升與層狀雲降雨**
春季初期的降雨通常不是夏天的雷陣雨，而是綿綿細雨（Drizzle）：

- **層雲降雨**： 暖濕氣流在冷墊上滑升，形成深厚的層狀雲。**由於上升動力較弱，水滴緩慢碰撞增大。**
- **地形效應**： 華南多丘陵山地。暖濕氣流遇到南嶺等山脈阻擋，被迫抬升，進一步冷卻凝結，導致山前地帶常年雲霧繚繞且細雨不斷。

## 深入了解如何從專業氣象圖(如 850hPa 風場圖或探空圖)中預判這種天氣的到來嗎?
要預判華南春季的雨霧（回南天），**氣象預報員主要觀察低空大氣的垂直構造與水汽輸送強度。**
以下是從 **850hPa 風場圖與探空圖**進行判讀的專業要點：

1. **850hPa 風場圖：尋找「水汽傳送帶」**

- **850hPa（約海拔 1,500 米）是大氣低層的重要層位**，能反映暖濕氣流的強弱。
- **低空急流 (Low-Level Jet, LLJ)**： 當 850hPa 出現強勁的偏南風（風速通常超過 12 m/s），代表大量來自南海的暖濕空氣正向北進攻。
- **切變線與輻合**： 若偏南風與北方的偏北風（冷空氣殘餘）在此高度交匯形成切變線，通常預示著持久的層狀雲降雨。
- **水汽通量**： 觀察等高線與風向，若風從海面直吹向陸地，且伴隨高濕區（比濕較大），則是典型的雨霧前兆。 

2. **探空圖 (Skew-T Log-P)：解讀「垂直密碼」**
探空圖能顯示某地從地面到高空的溫度與濕度垂直分布，是判斷霧與回南天的「金標準」。

- **逆溫層 (Inversion Layer)**： 觀察溫度曲線。正常是大氣隨高度遞減，若在近地面（約 925hPa 至 850hPa 之間）出現「下冷上暖」的現象，這就是逆溫層。它像蓋子一樣阻礙空氣對流，使水汽聚集在地面形成濃霧。
- **露點間隔 (Dew Point Depression)**： 查看溫度線（T）與露點線（Td）的距離。
  - **貼合（T ≈ Td）**： 代表該層大氣已飽和。若這種貼合出現在近地面且厚度較薄，即為霧。
  - **深厚飽和**： 若從地面到 700hPa 兩線都很接近，則會出現連綿細雨。
- **回南天判斷**： 當探空圖顯示底層（如 1000hPa）溫度顯著高於前幾日的地面溫度，且濕度極高（接近 100%），代表暖濕空氣已覆蓋在冰冷的建築物表面，即會引發反潮現象。

{% note primary %}
**簡單來說，華南春天的雨霧就是**：高比濕 ($q$) 的空氣，被強南風 ($\vec{V}$) 從海洋載運而來，並在低層大氣 ($dp$) 堆積的物理過程。
{% endnote %}

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