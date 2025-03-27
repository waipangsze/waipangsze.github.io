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
index_img: https://i.imgur.com/gLe27TW.png
banner_img: https://i.imgur.com/gLe27TW.png
---

- [NWP | AI for Numerical Weather Prediction (NWP)](https://waipangsze.github.io/2024/10/09/AI-for-Numerical-Weather-Prediction-NWP/)

---

**NeuralGCM** is trained through a sophisticated process that combines differentiable physical modeling with machine learning optimization techniques. Here's a breakdown of its training methodology:

# Core Training Components

## **1. Hybrid Architecture Integration**

- Combines a **differentiable dynamical core** (handling large-scale atmospheric physics) with a **neural network physics module** (modeling unresolved processes like cloud formation).
- The dynamical core **solves discretized governing equations using JAX-based solvers**, while neural networks parameterize subgrid-scale phenomena.
  - A key innovation of `NeuralGCM` is that we rewrote the [**numerical solver for large-scale processes**](https://github.com/google-research/dinosaur) from scratch in [**JAX**](https://docs.jax.dev/en/latest/advanced-autodiff.html). 
    - **JAX’s autodiff makes it easy to compute higher-order derivatives**, because the functions that compute derivatives are themselves differentiable. Thus, higher-order derivatives are as easy as stacking transformations.
      ![](https://i.imgur.com/nyZhlkE.png){width=400}
  - This allowed us to use gradient-based optimization to tune the behavior of the **coupled system “online”** over many time-steps.
  - **Another bonus of writing the entire model in JAX is that it runs efficiently on TPUs and GPUs**, in contrast to traditional climate models that mostly run on CPUs.
- [**Dinosaur: Differentiable Dynamics for Global Atmospheric Modeling**](https://github.com/neuralgcm/dinosaur)
  - **Dinosaur** is an old-fashioned (some might say prehistoric) dynamical core for global atmospheric modeling, **re-written in JAX** to meet the needs of modern AI weather/climate models:
    - **Dynamics**: Dinosaur uses *spectral methods* to solve the shallow water equations and the primitive equations (moist and dry) on *sigma coordinates*.
    - **Auto-diff**: Dinosaur supports both forward- and backward-mode automatic differentiation in JAX. **This enables "online training" of hybrid AI/physics models**.
    - **Acceleration**: Dinosaur is designed to **run efficiently on modern accelerator hardware (GPU/TPU)**, including parallelization across multiple devices.

### Structure of the NeuralGCM model

![Structure of the NeuralGCM model.](https://i.imgur.com/gLe27TW.png)

![](https://i.imgur.com/MmzCDhv.png)
![](https://i.imgur.com/wgfMkyu.png)

1. Implemented in JAX, a library for highperformance code in Python that supports automatic differentiation.
2. Our learned physics module uses the single-column approach of GCMs, whereby information only from a single atmospheric column is used to predict the impact of unresolved processes occurring within that column.

![](https://i.imgur.com/qyqI8z9.png)

$$
\begin{align}
\widetilde{X}_{t+1} & = \widetilde{X}_{t} + \Delta t [ \Phi(\widetilde{X}_{t}) + \Psi (\widetilde{X}_{t})]\\
\Psi (\widetilde{X}_{t}) & = NN(\widetilde{X}_{t})
\end{align}
$$

which are in $k$-space.

- **Tendency Calculation**  
  - Neural network outputs scaled tendencies:  
    $$
    \frac{\partial \mathbf{X}}{\partial t} = \mathcal{N}(\mathbf{X}, \nabla\mathbf{X}, \text{forcing}) \cdot \sigma_X
    $$
    where $\sigma_X$ is variable-specific scaling  

### Grid spacing and Time step

We train a range of NeuralGCM models at horizontal resolutions with grid spacing of $2.8\deg$ (~ 310.8km), $1.4\deg$ (~ 155.4km), and $0.7\deg$ (~ 77.7km), corresponding to truncated linear Gaussian grids TL63, TL127, TL255, and each utilizing $32$ evenly spaced vertical levels on sigma coordinates.

{% gi 4 2-2 %}
![](https://i.imgur.com/EZU8eEU.png)
![](https://i.imgur.com/9MsH68G.png)
![](https://i.imgur.com/2zWwbbn.png)
![](https://i.imgur.com/YSSR09Z.png)
{% endgi %}

- Integration time step varies with resolution. This results in iterative updates to the model state **every 4-30 minutes**, depending on model resolution.
- Throughout time integration, dynamical core tendenciesare computedat every time step, while learned physics tendencies are only recomputed once **every 60minutes for our lowest resolution($2.8\deg$)** model and **every 30minutes for all others**. This is done to avoid excessive backpropagation through the neural networks in learned physics.
- The time-step size of the ODE solver is limited by the CFL condition on dynamics, and can be small relative to the time-scale of atmospheric change. Evaluating learned physics is approximately 1.5 × as expensive as a time step of the dynamical core. Accordingly, following the typical practice for GCMs, **we hold learned physics tendencies constant for multiple ODE time-steps to reduce computational expense, typically corresponding to 30 minutes of simulation time**.

![My understanding: To hold learned physics tendencies constant for multiple ODE time-steps to reduce computational expense, typically corresponding to 30 minutes of simulation time.](https://i.imgur.com/O6gyFxx.png)

- We **gradually increased the rollout length from 6 hours to 5 days**, which we found to be critical because our models are not accurate for multi-day prediction early in training.

## **2. End-to-End Online Training**

- Uses **stochastic gradient descent (SGD)** to optimize both physical and neural components simultaneously.
  - The time-step size of the ODE solver is **limited by the CFL condition on dynamics**, and can be small relative to the time-scale of atmospheric change. **Evaluating learned physics is approximately 1.5 × as expensive as a time step of the dynamical core**. Accordingly, following the typical practice for GCMs, **we hold learned physics tendencies constant for multiple ODE time-steps to reduce computational expense, typically corresponding to 30 minutes of simulation time**.
  - The differentiable dynamical core in NeuralGCM allows an end-to-end training approach, whereby **we advance the model multiple time steps before employing stochastic gradient descent (SGD) to minimize discrepancies between model predictions and conservatively regridded ERA5 data**.
- Implements extended backpropagation through hundreds of simulation steps (up to 5-day weather trajectories).
- Gradually increases rollout length during training:
  - Starts with 6-hour forecasts
  - Progressively extends to 5-day predictions

## **3. Data Integration & Loss Optimization**

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

# Training resources

![](https://i.imgur.com/wh6zgKn.png)

# References

1. [Kochkov, Dmitrii, et al. "Neural general circulation models for weather and climate." Nature 632.8027 (2024): 1060-1066.](https://www.nature.com/articles/s41586-024-07744-y)
2. [Neural General Circulation Models for Weather and Climate | arxiv](https://arxiv.org/abs/2311.07222)
3. [Fast, accurate climate modeling with NeuralGCM (**Recommend**)](https://research.google/blog/fast-accurate-climate-modeling-with-neuralgcm/)
4. [NeuralGCM：大活来了，AI天气预报进入下半场？ | 2023-12](https://mp.weixin.qq.com/s/wgJHpQ0Ww_IjoTPcuoDrbQ)
5. [When and why to use JAX](https://livebook.manning.com/book/deep-learning-with-jax/chapter-1/)