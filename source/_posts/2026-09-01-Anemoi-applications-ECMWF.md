---
layout: post
title: Anemoi applications | ECMWF
categories: NWP
tags: [NWP, ML, AI, ECMWF, Anemoi, AIFS, AICON]
author: wpsze
date: 2026-09-01 06:59:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/iZNjOUN.png
banner_img: https://i.imgur.com/iZNjOUN.png
---

# AICON

- [德国国家气象局用自己的数据体系，训练AICON并真正部署进业务 | 那么预报员怎么说？](https://mp.weixin.qq.com/s/g0qH8cMaQZeXMgAONiJMSQ)
- <https://arxiv.org/pdf/2608.24651>
 
德国国家气象局用自己的数据体系，训练AICON并真正部署进业务 (预报员, 国家气象业务)

1. Anemoi 框架
2. 接使用 ICON 的 terrain-following 模式层
3. 宁愿牺牲部分长时效 RMSE，也尽量保住高分辨率 ICON-DREAM 里的小尺度信息
4. 用 flip-flop index 衡量这种逐次预报的不一致 (业务价值：预报一致性。一个不乱跳的模式，对预报员的信任建立和决策辅助，可能比 RMSE 的微小提升更实在。)
5. 平滑问题没有彻底解决 (球谐功率谱分析)
   1. First, the data of ICON forecasts, AICON forecasts, ICON operational analysis (ICON ANA) and ICON-DREAM reanalysis on each model level separately have been horizontally interpolated from the **original irregular R03B07icosahedral grid** to a **full Gaussian F640 grid (with 1280 Gaussian-spaced latitudes and 2560 Gaussian-spaced longitudes,approx. 0.14°)**, using first order conservative mapping with **cdo** `remapcon`.
   2. The resulting 2D global fields are transformed into **spherical wave components** after **subtraction of the cell-area-weighted global mean value**. The subtraction of the global mean ensures that the integral over the full spectra equals the variance. `Power spectra` are obtained by the sum of squared amplitudes of the components for each spatial scale to measure how much variance exists at different scales (wavelengths).
   3. ![](https://i.imgur.com/E4H2GfL.png)
6. 台风路径好，强度弱
 
AICON 预测产品进入 DWD 数据分发体系和天气预警业务。

1. 软件栈采用 containerization
2. 放弃 bit-wise reproducibility

# Nested-EAGLE (NOAA) 

- [天气模式一定要分全球和区域吗？NOAA给出了一个AI答案](https://mp.weixin.qq.com/s/Tls75uq8R7bIq5ZTARJPWg)
- <https://arxiv.org/abs/2608.26822>

1. Anemoi 框架
2. 把短时和中期合成一个系统。
3. 在 0.25° 全球网格上，嵌入一个 6 km 的 CONUS 精细化区域。模型的状态空间由两部分拼成——CONUS 上用 HRRR 数据（从 3 km 保守重网格到 6 km，用 Lambert 等角圆锥投影），全球其余部分用 GFS 数据（0.25° 经纬网格）。
4. 模型是自回归的，输入 t 和 t−6h 两个时刻，预测 t+6h，采用 encoder–processor–decoder 的图 transformer 架构。关键差异在 latent mesh（潜网格）：它比数据空间粗约 16 倍（全球 O96 八面体网格），但在 CONUS 上保持相对更高的分辨率——这样模型既能看到全球天气尺度，又能聚焦 CONUS 的局地结构。
5. 训练用 8 年数据（2015 年 2 月–2023 年 1 月，共 11,616 个样本），验证 1 年，测试 1 年（2024 年 2 月–2025 年 1 月）。基准线有三个：业务 GFS、业务 HRRR、以及一个几乎相同但只用 GFS 数据训练的 ML-GFS-Base（用来隔离"嵌套 HRRR 数据"的作用）。
6. 近地面全面领先
7. 这是论文最有价值的实验。一个根本问题是：Nested-EAGLE 相对 ML-GFS-Base 的增益，究竟是推理时喂给它更好的 HRRR 初始场带来的，还是训练时把 HRRR 数据学进了模型权重？
   1. 结论：技巧是训练时学到的（baked into the model weights），并能在推理时携带过去，即使初始条件不那么准。这一方面说明模型对退化的初始条件是稳健的；另一方面也意味着，单纯升级初始条件不会立刻改善预报，必须把改进纳入训练才能让 ML 模型受益。
8. Nested-EAGLE 在本质上学会了"把 GFS 分析校正到 HRRR 分析"这个映射，并把这种校正能力延伸到了预报中。
9. 降水：位置准，幅度糊
10. 目标区外：技巧不迁移