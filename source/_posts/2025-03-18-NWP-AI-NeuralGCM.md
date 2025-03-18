---
layout: post
title: NWP | AI | NeuralGCM
categories: NWP
tags: [NWP, ML, AI]
author: wpsze
date: 2025-03-18 17:44:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

- [NWP | AI for Numerical Weather Prediction (NWP)](https://waipangsze.github.io/2024/10/09/AI-for-Numerical-Weather-Prediction-NWP/)

---

**NeuralGCM** is trained through a sophisticated process that combines differentiable physical modeling with machine learning optimization techniques. Here's a breakdown of its training methodology:

## Core Training Components

**1. Hybrid Architecture Integration**

- Combines a **differentiable dynamical core** (handling large-scale atmospheric physics) with a **neural network physics module** (modeling unresolved processes like cloud formation).
- The dynamical core solves discretized governing equations using JAX-based solvers, while neural networks parameterize subgrid-scale phenomena.

**2. End-to-End Online Training**

- Uses **stochastic gradient descent (SGD)** to optimize both physical and neural components simultaneously.
- Implements extended backpropagation through hundreds of simulation steps (up to 5-day weather trajectories).
- Gradually increases rollout length during training:
  - Starts with 6-hour forecasts
  - Progressively extends to 5-day predictions

**3. Data Integration & Loss Optimization**

- Trained on ERA5 reanalysis data for atmospheric variables
- Incorporates satellite precipitation data from IMERG V07 using:
  - Continuous Ranked Probability Score (CRPS) minimization
  - Physical consistency preservation techniques
- Loss function compares model outputs with observational ground truth

## Key Optimization Features

**Stochastic Gradient Descent Implementation**

- Updates model parameters using randomly selected data batches
- Balances computational efficiency with convergence stability through:
  - Learning rate scheduling
  - Data shuffling between epochs
  - Mini-batch processing

**Training Protocol**

- **Deterministic models**: Focus on single trajectory optimization
- **Stochastic models**: Incorporate uncertainty quantification
- Employs checkpointing for model persistence and analysis

## Performance Enhancements

- Achieves **2-15 day forecast accuracy** surpassing ECMWF ensembles
- Reduces climate simulation errors by 10-20% compared to traditional GCMs
- Enables laptop-scale climate modeling through computational efficiency gains

This training approach enables NeuralGCM to maintain physical consistency while learning from observational data, resulting in improved forecasting capability and computational efficiency compared to conventional climate models.

# References

1. [Kochkov, Dmitrii, et al. "Neural general circulation models for weather and climate." Nature 632.8027 (2024): 1060-1066.](https://www.nature.com/articles/s41586-024-07744-y)
2. [Neural General Circulation Models for Weather and Climate | arxiv](https://arxiv.org/abs/2311.07222)
3. [NeuralGCM：大活来了，AI天气预报进入下半场？ | 2023-12](https://mp.weixin.qq.com/s/wgJHpQ0Ww_IjoTPcuoDrbQ)