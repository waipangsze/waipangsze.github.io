---
layout: post
title: MPAS-CMAQ | SMOKE | Emission Inventory processing data 
categories: [MPAS]
tags: [MPAS, NWP, WRF, CMAQ, SMOKE, ERA5, GFS]
author: wpsze
date: 2025-03-10 14:03:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/u13Gj88.png
banner_img: https://i.imgur.com/u13Gj88.png
---

- [SMOKE github](https://github.com/CEMPD/SMOKE/wiki/Running-SMOKE-using-EPA's-Emissions-Modeling-Platforms)
- [SMOKE documentation](https://www.cmascenter.org/smoke/documentation/5.1/html/ch01.html)

# The Sparse Matrix Operator Kernel Emissions (SMOKE) Modeling System

The MCNC Environmental Modeling Center (EMC) created the Sparse Matrix Operator Kernel Emissions (SMOKE) Modeling System to **allow emissions data processing methods to integrate high-performance-computing (HPC) sparse-matrix algorithms**. The SMOKE system is a significant addition to the available resources for decision-making about emissions controls for both urban and regional applications. The SMOKE prototype, available since 1996, was an effective tool for emissions processing in a number of regional air quality modeling applications. In 1998 and 1999, SMOKE was redesigned and improved with the support of the U.S. Environmental Protection Agency (EPA)

SMOKE can process criteria gaseous pollutants such as **carbon monoxide (CO), nitrogen oxides (NOx), volatile organic compounds (VOC), ammonia (NH3), sulfur dioxide (SO2); particulate matter (PM) pollutants such as PM 2.5 microns or less (PM2.5) and PM less than 10 microns (PM10)**; as well as a large array of **toxic pollutants**, such as **mercury, cadmium, benzene, and formaldehyde**. In fact, SMOKE has no limitation regarding the number or types of pollutants it can process.

The purpose of SMOKE (or any emissions processor) is 

- **to convert the resolution of the emission inventory data to the resolution needed by an air quality model (AQM)**. 

Emission inventories are typically available with an annual-total emissions value for each emissions source, or perhaps with an average-day emissions value. The AQMs, however, typically require emissions data on an hourly basis, for each model grid cell (and perhaps model layer), and for each model species.

- SMOKE 主要是一個排放處理系統，旨在**創建網格化、物種化、每小時排放量**，以輸入各種空氣品質模型，例如 CMAQ、REMSAD、CAMX 和 UAM。 SMOKE 支援區域、生物源、移動（道路和非道路）和點源排放處理，以處理標準、顆粒物和有毒污染物。對於生物源排放建模，SMOKE 使用生物源排放清單系統。 
- SMOKE 還與道路排放模型 MOVES 整合。SMOKE 模式是應用最為廣泛的排放源處理模式，現為美國 EPA 源排放處理的官方模型，**該模型中採用稀疏矩陣方法進行運算，直接為模式系統提供網格化的排放源**。SMOKE 模型可以為**多種空氣質量模型提供輸入，考慮了面源、點源及移動源 3 部分**，針對不同類型排放源的**空間分佈屬性、時間變化規律，設置不同比時空分配曲線，更合理地反映不同類型排放的時空特徵**。此外，還可根據用戶選擇的化學機制對排放數據進行化學分裂。

# 香港排放清單報告-HKEPD

- [2020年香港排放清單報告](https://www.epd.gov.hk/epd/sites/default/files/2020EIRC_15082022.pdf)

# TW-行政院環境保護署

- [	附件二 新增污染源處理程序排放檔製作](https://aqmc.moenv.gov.tw/download/CMAQ/%E9%99%84%E4%BB%B6%E4%BA%8C%20%E6%96%B0%E5%A2%9E%E6%B1%A1%E6%9F%93%E6%BA%90%E8%99%95%E7%90%86%E7%A8%8B%E5%BA%8F%E6%8E%92%E6%94%BE%E6%AA%94%E8%A3%BD%E4%BD%9C.pdf)
