---
layout: post
title: AI-DOP | Artificial Intelligence-Direct Observation Prediction | ECMWF
categories: NWP
tags: [NWP, ML, AI, ECMWF, Anemoi, AIFS, AI–DOP]
author: wpsze
date: 2026-09-01 07:03:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/NNOTKtj.png
banner_img: https://i.imgur.com/NNOTKtj.png
---

# AIFS-DOP

We introduce the **Artificial Intelligence Forecasting System for Direct Observation Prediction (AIFS-DOP)**. AIFS-DOP is trained on 

- **a 40-year harmonized dataset of gridded observations, without using numerical weather prediction (NWP) reanalysis or model data.** 

The resulting model is competitive with ECMWF's Integrated Forecasting System (IFS) when scored on a one year period of forecasts across 2021/2022. This progress on Direct Observation Prediction represents the first time that a data-driven model, trained solely on observations, is competitive with the IFS at medium ranges for several key upper-air and surface headline scores, when verified against observation data.

- [An update on AI–DOP: skilful weather forecasts produced directly from observations](https://www.ecmwf.int/en/newsletter/182/earth-system-science/update-ai-dop-skilful-weather-forecasts-produced-directly)
- [Machine learning opens new opportunities for global reanalysis | 20 July 2026](https://www.ecmwf.int/en/about/media-centre/news/2026/machine-learning-opens-new-opportunities-global-reanalysis)
- [AIFS-DOP: End-to-End Medium-Range Weather Prediction from Observations Alone with Machine Learning | arxiv](https://arxiv.org/abs/2606.19093)
  - The model uses an encoder-processor-decoder architecture, using a graph-based attention encoder/decoder and a transformer processor with sliding window attention as described in [Lang et al. [2024]](https://arxiv.org/abs/2406.01465).

![Figure 1: High-level model schematic: A single encoder is used for all observation types. The processor is as described
in Lang et al. [2024] using a residual connection. Then a single decoder to predict observations out onto a full grid.](https://i.imgur.com/NNOTKtj.png)

- **A single decoder** to predict observations out onto a **full grid**.
- AIFS-DOP operates on an **O96 octahedral reduced Gaussian grid**, which has a horizontal resolution of **approximately 1-degree (100 km to 112 km)**.