---
layout: post
title: NWP | TS and ETS
categories: [NWP]
tags: [MPAS, NWP, WRF, ERA5, GFS, TS, ETS, FSS, Rainfall, Precipitation]
author: wpsze
date: 2026-05-12 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/TgEccF7.png
banner_img: https://i.imgur.com/TgEccF7.png
---

# TS and ETS

Equitable Threat Score (ETS) 與 Threat Score (TS) 是氣象預報驗證中，用來衡量數值模式對特定降雨門檻預報能力的關鍵指標。

{% gi 5 3-2 %}
![](https://i.imgur.com/hW5nCJe.png)
![](https://i.imgur.com/YokKi7h.png)
![](https://i.imgur.com/asHBuUQ.png)
![](https://i.imgur.com/epeG0AK.png)
![](https://i.imgur.com/pAwTs11.png)
{% endgi %}

---

## 1. Threat Score (TS)

TS 又稱為 Critical Success Index (CSI)，主要衡量模式對「事件發生」的掌握能力。 

* 定義：正確預報出的事件次數（hits），除以「所有預報或實際發生的事件」總數。
* 公式：$TS = \frac{hits}{hits + false\ alarms + misses}$。
* 數值範圍：$0$ 到 $1$。$1$ 代表完全正確的完美預報；$0$ 則表示毫無預報能力。
* 限制：TS 對「稀有事件」（如強降雨）較為敏感，且不考慮僅憑「隨機猜測」而矇中的正確次數。 

## 2. Equitable Threat Score (ETS)

ETS 是 TS 的進階版，旨在修正「隨機矇中」的偏差，使其在比較不同氣候背景下的預報表現時更加公平（公正）。 

* 核心差異：ETS 從 hits 中減去了 預期隨機命中次數 (hits expected by chance)。
* 公式：$ETS = \frac{hits - hits_{chance}}{hits + false\ alarms + misses - hits_{chance}}$。
* 其中 $hits_{chance}$ 是假設預報與觀測完全無關時，僅憑隨機性可能命中的次數。
* 數值範圍：通常介於 $-1/3$ 到 $1$ 之間。
* ETS > 0：代表預報優於隨機猜測（具備預報技巧）。
   * ETS = 0：代表預報與隨機猜測無異。
   * ETS < 0：代表預報比隨機猜測還糟糕。 

## 總結對比

| 特性 | TS / CSI | ETS |
|---|---|---|
| 主要用途 | 衡量事件預報的成功率 | 移除隨機因素後的預報技巧評估 |
| 優點 | 計算直觀，考量了漏報與空報 | 適合比較稀有事件，對模式表現更具區分度 |
| 隨機性 | 未校正隨機命中，得分可能虛高 | 已校正隨機命中，結果更具公平性 |


## 如何計算隨機命中次數 ($hits_{chance}$)？

$hits_{chance}$ 的邏輯源自於機率論中的**獨立事件**。假設預報與實際觀測之間完全沒有物理關聯（純屬瞎猜），那麼兩者同時發生「事件」的機率，就是各自發生機率的乘積。

我們通常使用 **列聯表 (Contingency Table)** 來輔助計算：

|  | 觀測：有發生 (Yes) | 觀測：未發生 (No) | 總計 |
| --- | --- | --- | --- |
| **預報：有預報 (Yes)** | **Hits (a)** | **False Alarms (b)** | $F = a + b$ |
| **預報：未預報 (No)** | **Misses (c)** | **Correct Negatives (d)** | $M = c + d$ |
| **總計** | $O = a + c$ | $N = b + d$ | **$Total (T) = a+b+c+d$** |

## 計算公式：

$$hits_{chance} = \frac{(hits + false\ alarms) \times (hits + misses)}{Total}$$


或者用更直觀的說法：


$$hits_{chance} = \frac{預報有降雨的次數 (F) \times 實際有降雨的次數 (O)}{總樣本數 (T)}$$

**為什麼這樣算？**
這代表如果我們隨機亂撒預報，命中率會正比於「你預報得有多頻繁」以及「該事件發生的頻率」。

---

### 具體案例解讀：強降雨驗證

假設某模式在 100 天的夏季預報中，針對「豪雨門檻」的表現如下：

* **Hits (a):** 5 天 (正確抓到豪雨)
* **False Alarms (b):** 10 天 (報了沒下)
* **Misses (c):** 5 天 (漏掉沒報)
* **Correct Negatives (d):** 80 天 (成功預測沒大雨)

#### 1. 計算 TS：

$$TS = \frac{5}{5 + 10 + 5} = \frac{5}{20} = 0.25$$

#### 2. 計算 $hits_{chance}$：

首先找出預報總天數 $F = 15$，觀測總天數 $O = 10$，總樣本 $T = 100$。


$$hits_{chance} = \frac{15 \times 10}{100} = 1.5$$

#### 3. 計算 ETS：

$$ETS = \frac{5 - 1.5}{5 + 10 + 5 - 1.5} = \frac{3.5}{18.5} \approx 0.189$$

---

### 深度解讀：為什麼 ETS 會變低？

從上面的例子可以看出：

1. **數值下降：** $ETS (0.189) < TS (0.25)$。這是因為 ETS 扣除了那 1.5 天「可能只是運氣好」的成分。
2. **稀有事件的挑戰：** 當事件越稀有（如極端降雨），$hits_{chance}$ 會變得很小，TS 與 ETS 的差距就會縮小；但如果模式採取「亂槍打鳥」策略（大幅增加預報頻率），$hits_{chance}$ 會迅速攀升，導致 **ETS 劇降** 甚至變成負值。這就是為什麼 ETS 能有效懲罰那些過度預報、企圖靠數量換取命中的模式。

# Problems in traditional statistical measures

Traditional statistical measures like the **Threat Score (TS)** and **Equitable Threat Score (ETS)** are "point-to-point" metrics. While they are rigorous, they often fail to capture the value of high-resolution models, leading to what we call the **Double Penalty** problem.

To address this, the **Fractions Skill Score (FSS)** was developed as a "neighborhood-based" verification method. Here is a detailed breakdown of why traditional scores struggle and how FSS provides a more realistic evaluation.

---

## 1. The "Double Penalty" Problem

In high-resolution modeling, a forecast that is physically realistic but slightly shifted in space or time is penalized twice by traditional metrics:

1. **A False Alarm:** The model predicted rain where none occurred.
2. **A Miss:** The model failed to predict rain where it actually occurred.

A coarse-grained model that predicts "light rain everywhere" might actually achieve a higher TS than a high-resolution model that correctly simulates a thunderstorm's structure but misses the exact location by 10 kilometers. This creates a paradox where a **visually better forecast** receives a **statistically worse score**.

---

## 2. Neighborhood Verification: Fractions Skill Score (FSS)

Instead of requiring an exact pixel-to-pixel match, FSS evaluates how the **frequency (fraction)** of an event (e.g., rainfall > 5mm) compares between the forecast and observation within a specific spatial window (neighborhood).

### Stage I: Transformation to Fraction Grids

1. **Thresholding:** Both the forecast and observation fields are converted into binary fields (1 if above threshold, 0 if below).
2. **Smoothing:** A "neighborhood" (a square window of size $n \times n$) is moved across the grid.
3. **Fraction Calculation:** Within each window, we calculate the percentage of grid points that exceed the threshold.

### Stage II: Calculating the FSS

The FSS compares the forecast fractions ($P_f$) and observed fractions ($P_o$) using a formula similar to a mean squared error, normalized to range from **0 to 1**:

$$FSS = 1 - \frac{\frac{1}{N} \sum_{i=1}^{N} (P_{f,i} - P_{o,i})^2}{\frac{1}{N} [\sum_{i=1}^{N} P_{f,i}^2 + \sum_{i=1}^{N} P_{o,i}^2]}$$

* **FSS = 1:** Perfect match of fractions across the domain.
* **FSS = 0:** Complete mismatch.
* **FSS > $0.5 + f_o/2$:** Generally considered the threshold for "useful" skill (where $f_o$ is the domain-wide observed fraction).

---

## 3. Key Advantages of FSS

* **Scale-Dependency:** Unlike TS/ETS, FSS is calculated for various neighborhood sizes (e.g., 5km, 10km, 50km). This allows researchers to identify the **"useful scale"**—the point where the model starts becoming accurate.
* **Reward for Realism:** It recognizes that a high-resolution model simulating intense, localized cells is "closer to the truth" than a smooth, low-resolution blur, even if the placement isn't perfect.
* **Scientific Insight:** It tells us *how far off* the model is. If FSS is low at a 5km scale but high at a 20km scale, we know the model has the right intensity but a 20km displacement error.

---

### Summary Comparison

| Feature | Traditional Scores (TS/ETS) | Neighborhood Method (FSS) |
| --- | --- | --- |
| **Matching** | Exact grid-to-grid | Area-based fractions |
| **Sensitivity** | Very sensitive to small displacements | Tolerant of small spatial shifts |
| **Best For** | Coarse, synoptic-scale events | High-resolution, convective-scale events |
| **Primary Output** | Single accuracy value | Skill as a function of spatial scale |


# 中国气象局

- [唐文苑, 周庆亮, 刘鑫华, 朱文剑, & 毛旭. (2017). 国家级强对流天气分类预报检验分析. 气象, 43(1), 67-76.](http://qxqk.nmc.cn/html/2017/1/20170107.html)
- [谌芸, 曹勇, 孙健, 等, 2021. 中央气象台精细化网格降水预报技术的发展和思考. 气象, 47(6): 655-670. DOI: 10.7519/j.issn.1000-0526.2021.06.002.](http://qxqk.nmc.cn/html/2021/6/20210602.html)
  - ![](https://i.imgur.com/TgEccF7.png){width=300}
  - ![](https://i.imgur.com/6F3FCa5.png){width=300}
- [刘段灵, 陈超. 最优TS评分方法在逐时降水订正中的初步应用及评估[J]. 热带气象学报, 2022, 38(4): 611-620. doi: 10.16032/j.issn.1004-4965.2022.055 ](https://rdqxxb.itmm.org.cn/cn/article/doi/10.16032/j.issn.1004-4965.2022.055)
- [	黄丽萍, 邓莲堂, 王瑞春, 等. CMA-MESO关键技术集成及应用. 应用气象学报, 2022, 33(6): 641-654. DOI: 10.11898/1001-7313.20220601](https://qikan.camscma.cn/article/doi/10.11898/1001-7313.20220601)
  - ![](https://i.imgur.com/yGV9pWy.png){width=300}
- [刘会军, 吴启树, 危国飞, 等. 2024. 数值模式降水预报 OTS 订正法的实现技术 [J]. 大气科学, 48(5): 1891−1900.](https://www.iapjournals.ac.cn/dqkx/cn/article/pdf/preview/10.3878/j.issn.1006-9895.2307.22115.pdf)
- [张海燕, 吴启树, 危国飞, 等. 2025. 基于多源资料融合集成的短时强降水短时临近预报技术 [J]. 大气科学, 49(6): 1711−1726.](https://www.iapjournals.ac.cn/dqkx/cn/article/pdf/preview/10.3878/j.issn.1006-9895.2501.24061.pdf)
- [张博, 张迪, 陈双, 等. “25·7”华北特大暴雨数值预报检验评估. 应用气象学报, 2026, 37(2): 129-144. DOI: 10.11898/1001-7313.20260201](https://qikan.camscma.cn/article/doi/10.11898/1001-7313.20260201)

# 中央氣象局

- [陳冠杰, & 黃婉如. (2016). 中央氣象局全球預報模式對 2016 年 5~ 6 月東亞區域降雨之預報能力評析. 大氣科學, 44(4), 305-327.](http://mopl.as.ntu.edu.tw/web/ASJ/44/44-4-2.pdf)

# References

1. [**Verification of WRF Simulations | Ming Chen**](https://www2.mmm.ucar.edu/wrf/users/tutorial/presentation_pdfs/201901/chen_verification.pdf)