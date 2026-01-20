---
layout: post
title: NWP | ADEckS | Tropical Cyclone Track Data | UCAR
categories: [NWP]
tags: [MPAS, WRF, NWP, GFS, FNL, IFS, ERA5, CMA, TIGGE, TC, typhoon, tracker, UCAR, adecks_open]
author: wpsze
date: 2026-01-20 06:30:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/mcVeSmW.png
banner_img: https://i.imgur.com/mcVeSmW.png
---

# Tropical Cyclone Guidance Project (TCGP) | NCAR Research Applications Laboratory (RAL)

![the Tropical Cyclone Guidance Project (TCGP) | NCAR Research Applications Laboratory (RAL)](https://i.imgur.com/3GbfekQ.png){width=600}

- [Automated Tropical Cyclone Forecasting System](https://en.wikipedia.org/wiki/Automated_Tropical_Cyclone_Forecasting_System)
- <https://hurricanes.ral.ucar.edu/repository/>
  - An **a-deck** is a data file that contains a complete listing of all available forecast aid projections for the entire storm history. The real-time guidance system uses a subset of the track and intensity information contained in these files to create the real-time guidance plots. The name of the file, "a-deck", comes from the fact that the filenames start with an "a". Normally, all the model projections are included for the entire lifetime of the storm, so these files can get quite large (~1 MB). The files use a common format known as "ab-deck", according to the specifications of the . For more information on decoding the a-deck contents.
  - A **tcvitals** file is a small data file that contains the current operational estimate of the storm's center location, intensity, central pressure, and several other parameters at synoptic time (00, 06, 12, or 18 UTC). 
- <https://hurricanes.ral.ucar.edu/repository/data/adecks_open/>

# ADEckS

**adecks_open** 是一個公開數據儲存庫的目錄路徑，位於 UCAR 的颶風研究網站 之下，主要存放與熱帶氣旋（颱風/颶風）相關的 **「ADEckS」（Automated Decision-making Aid for Ensemble Track/Intensity）** 集合預報資料，供研究人員和公眾下載，包含不同年份和日期的預報數據檔案（.dat）。 

- 主要功能與用途：
  - 提供原始數據： 該目錄提供原始的集合預報數據，用於天氣研究和開發。
  - 追蹤與強度預報： **ADEckS** 是一種自動化輔助工具，用於分析多個集合預報模型，以提供颱風路徑和強度的決策輔助。
  - 公開存取： 以 `_open` 命名表示這些數據是公開的，可供 颶風研究實驗室 (RAL) 及其相關機構存取。 
- 範例檔案：
  - `aal112020.dat` 是一個 **2020** 年的資料檔案。 
- 簡而言之，adecks_open 是颶風預報數據的開放資料庫連結，內含用於颱風預報的集合模式數據。

## awp012025.dat

- <https://hurricanes.ral.ucar.edu/repository/data/adecks_open/awp012025.dat>

{% fold info @awp012025.dat %}
```dat
WP, 01, 2025061312, 03, CC00, 006, 199N, 1089E,  35,  993, XX,  34, NEQ, 0052, 0000, 0000, 0000
WP, 01, 2025061312, 03, CC00, 012, 206N, 1094E,  38,  993, XX,  34, NEQ, 0092, 0123, 0000, 0000
WP, 01, 2025061312, 03, CC00, 018, 218N, 1104E,  41,  993, XX,  34, NEQ, 0000, 0126, 0070, 0000
WP, 01, 2025061312, 03, CC00, 024, 232N, 1112E,  34,  994, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CC00, 030, 246N, 1122E,  26,  997, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CC00, 036, 263N, 1142E,  14,  998, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CC00, 042, 278N, 1162E,  14,  998, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CC00, 048, 291N, 1198E,  37,  998, XX,  34, NEQ, 0000, 0163, 0000, 0000
WP, 01, 2025061312, 03, CC00, 054, 309N, 1232E,  37,  999, XX,  34, NEQ, 0000, 0116, 0149, 0000
WP, 01, 2025061312, 03, CC00, 060, 339N, 1272E,  36,  999, XX,  34, NEQ, 0262, 0153, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 000, 192N, 1086E,  38,  993, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 006, 199N, 1088E,  35,  994, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 012, 207N, 1094E,  36,  994, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 018, 217N, 1103E,  38,  993, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 024, 230N, 1110E,  32,  995, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 030, 242N, 1119E,  26,  996, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 036, 256N, 1137E,  18,  998, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 042, 273N, 1158E,  16,  998, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 048, 287N, 1188E,  30,  998, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 054, 306N, 1223E,  36,  998, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 060, 337N, 1265E,  35,  997, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CEMN, 066, 369N, 1306E,  33,  995, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03,  CMC, 000, 190N, 1086E,  39,  991, XX,  34, NEQ, 0000, 0078, 0061, 0000
WP, 01, 2025061312, 03,  CMC, 006, 200N, 1089E,  36,  993, XX,  34, NEQ, 0050, 0000, 0092, 0000
WP, 01, 2025061312, 03,  CMC, 012, 207N, 1094E,  38,  993, XX,  34, NEQ, 0111, 0136, 0064, 0000
WP, 01, 2025061312, 03,  CMC, 018, 217N, 1103E,  45,  992, XX,  34, NEQ, 0000, 0130, 0000, 0000
WP, 01, 2025061312, 03,  CMC, 024, 230N, 1112E,  33,  994, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03,  CMC, 030, 243N, 1120E,  30,  996, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03,  CMC, 036, 254N, 1135E,  19,  998, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03,  CMC, 042, 270N, 1156E,  15,  999, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03,  CMC, 048, 284N, 1178E,  32, 1000, XX,  34, NEQ, 0000, 0195, 0000, 0000
WP, 01, 2025061312, 03,  CMC, 054, 289N, 1216E,  37, 1000, XX,  34, NEQ, 0267, 0106, 0151, 0000
WP, 01, 2025061312, 03,  CMC, 060, 308N, 1250E,  35, 1001, XX,  34, NEQ, 0000, 0081, 0000, 0000
WP, 01, 2025061312, 03,  CMC, 066, 342N, 1283E,  31,  998, XX,  34, NEQ, 0000, 0000, 0000, 0000
WP, 01, 2025061312, 03, CP01, 000, 195N, 1085E,  36,  994, XX,  34, NEQ, 0000, 0092, 0088, 0000
WP, 01, 2025061312, 03, CP01, 006, 201N, 1087E,  35,  996, XX,  34, NEQ, 0053, 0313, 0130, 0000
```
{% endfold %}

## **ADEckS**
**ADEckS** (Automated Decision-making Aid for Ensemble Track/Intensity) is a specialized software tool used by tropical cyclone forecasters (such as those at the National Hurricane Center) to synthesize large amounts of model data into a single, reliable forecast.

Think of it as a "consensus builder" that helps meteorologists make sense of the "spaghetti models" you often see during hurricane season.

---

### Core Functions of ADEckS

In modern meteorology, we don't just look at one model; we look at **ensembles** (dozens of variations of a model run with slightly different starting conditions). ADEckS is designed to handle this data in three specific ways:

* **Automated Consensus:** It calculates weighted averages of various models. Instead of a forecaster manually picking which model looks best, ADEckS can create a "consensus" track and intensity based on the historical performance of those models.
* **Uncertainty Visualization:** It helps identify how much the models disagree. If the ensemble members are tightly clustered, confidence is high; if they are widely spread, ADEckS helps visualize that uncertainty.
* **Data Integration (The "A-Deck"):** In hurricane forecasting, the **"A-Deck"** is the file format used for tropical cyclone forecast data. ADEckS automates the process of generating these files, which are then used to create the official "cone of uncertainty" and other public advisories.

### Why is it important?

Without a tool like ADEckS, a forecaster would have to manually look at 50+ different lines on a map and try to find the "middle ground." ADEckS streamlines this by:

1. **Filtering Noise:** It can ignore "outlier" models that are clearly off-track.
2. **Speed:** It processes massive datasets in seconds, which is vital when a storm is approaching land.
3. **Objective Analysis:** It removes some of the human bias by providing a mathematically sound starting point for the forecast.

### The "Deck" Nomenclature

In the world of the National Hurricane Center (NHC), data is organized into "Decks":

* **A-Deck:** Automated/Forecast data (where ADEckS lives).
* **B-Deck:** Best track data (historical record of where the storm actually went).
* **F-Deck:** Fix data (satellite/radar coordinates of the storm center).


# Real-Time Guidance | Current Active Storms

- <https://hurricanes.ral.ucar.edu/realtime/current/>
  - ![](https://i.imgur.com/mcVeSmW.png)
- **ATCF data files**
  - ```dat
    WP, 01, 2026011412, 03,  UKM,   0,  91N, 1302E,  30, 1005, TD,   0,    ,    0,    0,    0,    0,     ,     , 273,    ,    ,   W,    ,    ,    ,    ,        01W,    0,   ,   0,    ,    0,    0,    0,    0, 
    WP, 01, 2026011412, 03,  UKM,   6,  97N, 1304E,  26, 1002, TD,   0,    ,    0,    0,    0,    0,     ,     , 296,    ,    ,   W,    ,    ,    ,    ,        01W,    0,   ,   0,    ,    0,    0,    0,    0, 
    WP, 01, 2026011412, 03,  UKM,  12,  97N, 1294E,  29, 1003, TD,   0,    ,    0,    0,    0,    0,     ,     , 179,    ,    ,   W,    ,    ,    ,    ,        01W,    0,   ,   0,    ,    0,    0,    0,    0, 
    WP, 01, 2026011412, 03,  UKM,  18, 100N, 1300E,  28, 1000, TD,   0,    ,    0,    0,    0,    0,     ,     , 191,    ,    ,   W,    ,    ,    ,    ,        01W,    0,   ,   0,    ,    0,    0,    0,    0, 
    ```

## ATCF TECH identifier

- <https://hurricanes.ral.ucar.edu/repository/techlist/>

- An **ATCF (Automated Tropical Cyclone Forecasting) TECH identifier** is a short acronym (like HWRF, GFS, CMC) used within the ATCF system to label specific forecast models, techniques, or data sources for tropical cyclones, helping users distinguish between different forecast outputs (e.g., models, consensus, best tracks) in forecast files.
