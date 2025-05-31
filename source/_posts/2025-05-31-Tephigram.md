---
layout: post
title: Tephigram | 溫熵圖
categories: [Meteorology]
tags: [NWP, MPAS, WRF]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-05-31 11:15:00
index_img: 
banner_img: 
---

A **tephigram** is one of a number of thermodynamic diagrams commonly used in weather analysis and forecasting. The name evolved from the original name "T-ϕ-gram" to describe the axes of temperature (T) and entropy (ϕ) used to create the plot. Usually, temperature and dew point data from radiosondes are plotted on these diagrams to allow calculations of convective stability or convective available potential energy (CAPE). Wind barbs are often plotted at the side of a tephigram to indicate the winds at different heights.

具有水平的等壓線以及彼此之間夾角很大的等溫線和乾絕熱線

最主要的功能在於繪製探空圖，提供了大氣中溫度和露點溫度的垂直分布情形，我們可以藉由圖上的線條了解氣塊上升及下降過程，並推得凝結高度、濕球溫度...等熱力性質。圖表上的等值線可以讓人們更輕鬆地解讀大氣的特徵，簡化許多過去以手算或甚至擱置不管的繁雜計算過程。

# 利用溫熵圖評估大氣穩定度 | HKO

- [利用溫熵圖評估大氣穩定度 | HKO](https://www.hko.gov.hk/tc/education/meteorological-instruments/automatic-weather-stations/00504-assessing-atmospheric-stability-by-tephigram.html)
  - 大氣是否穩定與天氣現象的出現有非常密切的關係。例如，輻射霧會出現在比較穩定的大氣，而強對流天氣則發生在不穩定的大氣中。
  - 大氣穩定度原來跟大氣層的溫度及濕度隨高度的變化有關。簡單來說，大氣壓力隨高度上升而減少，當一塊氣團上升時會因為氣壓減小而膨脹；按能量守恆定律，空氣膨脹便會降溫。若果氣團內的水分未飽和，氣團每上升1公里，溫度便會下降9.8度（稱為乾直減率）。若果氣團內的水分已達到飽和，溫度則會按濕直減率下降。如上升氣團降溫後要比周圍的空氣暖，它便會因為密度較低而繼續上升（大氣不穩定）。反之，如上升氣團降溫後比周圍空氣冷，則會因自身的重量而下降至原來的位置（大氣穩定）(見圖一)。

![](https://i.imgur.com/iYbmaA2.png)

- **氣溫**是用黑色繪畫，
- **露點溫度**則用紅色繪畫。
- 黑線及紅線之間的距離越大，代表空氣越乾燥。
- 當兩線相當貼近，代表空氣潮濕有利於雲的形成。
- 在溫熵圖上的高度是用氣壓單位來表示。
- **圖中向右傾斜45度的直線為等溫線**； 
- **向左傾斜45度的直線為乾絕熱直減率**； 
- 接近垂直方向的曲線則是濕絕熱直減率；
- 而向右傾斜的粉紅色線是飽和水汽混合比，代表某溫度下飽和空氣中的含水量。

## 劃地為牢逆溫層 | HKO

- [劃地為牢**逆溫層** | HKO](https://www.hko.gov.hk/tc/education/meteorological-instruments/automatic-weather-stations/00521-inversion-layer-impose-its-own-bounds.html)
  - 在大氣層底部，氣溫一般會隨高度增加而下降。在標準大氣中從地面向上爬升直至對流層，每升高1000米，溫度就降低約6.5°C。然而在實際大氣環境中，有時高度增加時氣溫反而上升，這種倒置的溫度結構被稱為**逆溫層**。
  - 我們可以利用**溫熵圖上的氣溫剖線輕易找出逆溫層的所在** (圖一)。它常出現於晴朗微風的冬天晚上。沒有了雲的遮蔽，地球表面熱量能自由散發到大氣外，並令接近地面的空氣溫度迅速下降。微弱的風力減少了下層已變冷的空氣與上層仍較暖的空氣互相混合，以致出現了明顯下冷上暖的逆溫現象。這種輻射逆溫層通常在黎明前出現，並隨著日出帶來的增溫而消失。

![](https://i.imgur.com/cE2CW4p.png)

- 溫熵圖顯示逆溫層。
- 黑線表示探空氣球量度的氣溫，
- 而紅線表示露點溫度。
- 黑線相對於等溫線(綠線)向左傾斜表示溫度隨高度下降，屬於正常大氣的情況；
- **相反，黑線向右傾斜代表溫度隨高度上升，顯示該高度層出現逆溫**。
- **圖中橙色陰影標示的就是逆溫層**

<iframe width="560" height="315" src="https://www.youtube.com/embed/p7dIe9V7LKo" title="雲海．逆溫層" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 香港高空氣象觀測

- [香港高空氣象觀測](https://www.hko.gov.hk/tc/out_photo/upper-air-weather.htm)

{% gi 2 1-1 %}
![](https://i.imgur.com/CGapYNY.png)
![](https://i.imgur.com/PlBEnS8.png)
{% endgi %}

# The Tephigram

- <https://www.met.reading.ac.uk/~sgs02rpa/TEACHING/Tephigram.pdf>
  -  air temperature (T) and 
  -  dew-point temperature (Td)
  -  lifting condensation level (LCL), the altitude at which a rising parcel of air reaches saturation when lifted dry adiabatically

{% gi 5 2-2-1 %}
![](https://i.imgur.com/KGUV8XU.png)
![](https://i.imgur.com/zYlumBl.png)
![](https://i.imgur.com/fmdqy68.png)
![](https://i.imgur.com/ueDIYDJ.png)
{% endgi %}

- The **lifting condensation level or lifted condensation level (LCL)** is the height at which the relative humidity (RH) of an air parcel will reach 100% with respect to liquid water when it is cooled by dry adiabatic lifting. 
- **抬升凝結高度（LCL）** 是未飽和濕空氣塊乾絕熱上升，剛開始凝結的高度。 LCL可以透過乾絕熱過程中的溫度遞減率和露點溫度遞減率來估計

![](https://i.imgur.com/hmjv8aF.png){width=500}