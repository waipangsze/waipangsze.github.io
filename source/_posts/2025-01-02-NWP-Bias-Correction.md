---
layout: post
title: NWP | Bias Correction
categories: [NWP]
tags: [ML, AI, NWP]
author: wpsze
date: 2025-01-02 13:47:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

- [應用「模式輸出統計」校正數碼氣溫預報 | HKO | 2011](https://www.hko.gov.hk/hko/publica/reprint/r948.pdf)
  - 多元線性回歸及卡爾曼濾波
  - 出現次數最多的最佳因子，首三位為2米溫度、925hPa經向風及850hPa溫度
- [應用「模式輸出統計」校正相對濕度及風的數碼預報 | HKO | 2012](https://www.weather.gov.hk/tc/publica/reprint/files/r1010.pdf)
  - 把2010年9月至2011年8月的數據分為四季，分別找出不同統計模型在四季及全年(即2010年9月至2011年8月)的最佳因子組合。 
  - 不同季節的比較
  - 時間及空間的變化
  - t–測試
- [利用卡爾曼濾波修正每日地面氣溫預報 | 澳門地球物理暨氣象局](https://www.weather.gov.hk/tc/publica/ghm_21/files/MPaper_Choi.pdf)
- [按天氣情境修正數值預報模式的低溫預報 | HKO | 2016](https://www.hko.gov.hk/hko/publica/reprint/r1226.pdf)
