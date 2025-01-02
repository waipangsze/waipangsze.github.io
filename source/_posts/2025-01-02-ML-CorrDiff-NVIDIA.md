---
layout: post
title: ML | CorrDiff (NVIDIA)
categories: [ML]
tags: [ML, AI, pytorch, NVIDIA]
author: wpsze
date: 2025-01-02 10:01:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---


# News

- [生成式AI與傳統數值天氣預報的結合NVIDIA CorrDiff天氣降尺度模型 | 科學月刊 | 2024-07-15](https://www.scimonth.com.tw/archives/10997)
  - 輝達（NVIDIA）執行長黃仁勳今（2024）年3月在美國舉辦的GTC（GPU technology conference）大會及6月在臺灣舉辦的臺北國際電腦展（COMPUTEX）上，皆展示了一項稱為「CorrDiff」（Corrector Diffusion）的生成式人工智慧（generative artificial intelligence, generative AI）技術，並表示它可以應用於區域高解析度天氣預報上。
  - 對於氣象模擬與預報來說，縮小網格解析度一直是一大挑戰。傳統物理模式受限於運行時所需的龐大運算量，當今執行全球預報的最高解析度模式為ECMWF的九公里解析度全球模式。
  - 氣象署發展的「RWRF」（Radar WRF）高解析度極短期劇烈天氣預報系統（延伸閱讀2），可提升對臺灣區域劇烈天氣的預報能力。此系統奠基於美國國家大氣研究中心（National Center for Atmospheric Research）的「WRF」（Weather Research & Forecasting）數值天氣預報模式與它的資料同化系統（WRFDA），再由氣象署針對臺灣本地需求修改發展，自2016年起開始作業運作。RWRF系統的模式解析度為兩公里，涵蓋範圍如圖一所示。它能運行區域高解析度的**快速更新循環資料同化**，將高解析度的即時觀測資料運用於區域模式中，藉此得到區域範圍內更準確的分析與短期預報資料，比單純的動力降尺度預報更為複雜。**其中，資料同化使臺灣區域的氣象雷達觀測資料每半小時進入此模式中、地面氣象站觀測資料（包含人工和自動氣象站）每一小時進入模式中。RWRF系統以每小時的更新頻率提供13小時長度的預報資料，** 作為署內預報中心與民航局等相關政府單位的參考。
- 輝達執行長黃仁勳皆提到與中央氣象署合作開發的生成式AI模型「CorrDiff」，能透過AI將氣象資料解析度從25公里降至2公里，與傳統模式相比，更可讓單次推理速度提高1,000倍、能耗減少3,000倍。[link: 2024 年 09 月 28 日](https://technews.tw/2024/09/28/cwa-corrdiff-nvidia/)
  - 用CorrDiff來「降尺度」（也就是提高解析度）的重要性由此而生。
  - 以CorrDiff來說，便是由氣象署提供四年份、以每小時計的2公里氣象資料，共含四個變數，而輝達則拿出25公里氣象資料，並負責演算法及提供超級電腦運算資源，採「監督式學習」（Supervised learning）進行訓練，共同打磨出這款模型。
  - 「輝達在做CorrDiff之前，其實已經在全球天氣預報有非常好的成果，所以他接著就要找一個小尺度、天氣最困難最複雜的地方，」陳柏孚分析，台灣處在最大季風區、有複雜地形，又有颱風、梅雨，本身就夠挑戰、夠有特色，雙方合作是雙贏。

