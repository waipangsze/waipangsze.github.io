---
layout: post
title: NWP | DA | Kalman Filter
categories: [NWP]
tags: [NWP, MPAS, WRF, DA, KF, EKF, EnKF]
author: wpsze
date: 2025-04-02 14:02:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/ChQMBeV.png
banner_img: https://i.imgur.com/ChQMBeV.png
---

**Optimal Interpolation (OI), 3D-Variational Assimilation (3DVar), and the Kalman Filter (KF) share foundational mathematical objectives** but differ in implementation and error covariance treatment. Below is a formulation of their core equations and key comparisons.

---

## Mathematical Formulations

### **Optimal Interpolation (OI)**
$$
\mathbf{x}^a = \mathbf{x}^b + \mathbf{W}\left(\mathbf{y}^o - \mathbf{H}\mathbf{x}^b\right)
$$
- **Weights**: $\mathbf{W} = \mathbf{B}\mathbf{H}^T\left(\mathbf{H}\mathbf{B}\mathbf{H}^T + \mathbf{R}\right)^{-1}$  
- **Static Covariances**: Background ($\mathbf{B}$) and observation ($\mathbf{R}$) error covariances are predefined and stationary.

{% note primary %}
[**NWP | DA | Optimal weight matrix W**](https://waipangsze.github.io/2025/04/02/NWP-DA-Optimal-weight-matrix-W/)
{% endnote %}

---

### **3D-Variational Assimilation (3DVar)**
$$
J(\mathbf{x}) = \frac{1}{2}(\mathbf{x}-\mathbf{x}^b)^T\mathbf{B}^{-1}(\mathbf{x}-\mathbf{x}^b) + \frac{1}{2}(\mathbf{y}^o-\mathbf{H}\mathbf{x})^T\mathbf{R}^{-1}(\mathbf{y}^o-\mathbf{H}\mathbf{x})
$$
- Minimizes $J(\mathbf{x})$ to find $\mathbf{x}^a$.
- **Equivalence to OI**: For linear $\mathbf{H}$ and Gaussian errors, 3DVar and OI yield identical solutions.

---

### **Kalman Filter (KF)**
**Predict Step**:
$$
\begin{aligned}
\mathbf{\hat{x}}_{k|k-1} &= \mathbf{F}\mathbf{\hat{x}}_{k-1|k-1} \quad &\text{(State prediction)} \\
\mathbf{P}_{k|k-1} &= \mathbf{F}\mathbf{P}_{k-1|k-1}\mathbf{F}^T + \mathbf{Q} \quad &\text{(Covariance prediction)}
\end{aligned}
$$
**Update Step**:
$$
\begin{aligned}
\mathbf{K}_k &= \mathbf{P}_{k|k-1}\mathbf{H}^T\left(\mathbf{H}\mathbf{P}_{k|k-1}\mathbf{H}^T + \mathbf{R}\right)^{-1} \quad &\text{(Kalman gain)} \\
\mathbf{\hat{x}}_{k|k} &= \mathbf{\hat{x}}_{k|k-1} + \mathbf{K}_k\left(\mathbf{y}^o - \mathbf{H}\mathbf{\hat{x}}_{k|k-1}\right) \quad &\text{(State update)} \\
\mathbf{P}_{k|k} &= (\mathbf{I} - \mathbf{K}_k\mathbf{H})\mathbf{P}_{k|k-1} \quad &\text{(Covariance update)}
\end{aligned}
$$
- **Dynamic Covariances**: $\mathbf{P}$ evolves with time, capturing flow-dependent errors.

![](https://i.imgur.com/ChQMBeV.png)

---

## Key Similarities
1. **Objective**: Minimize analysis error via $\mathbf{y}^o$ and $\mathbf{x}^b$.
2. **Linear-Gaussian Assumption**: All methods assume linear observation operators ($\mathbf{H}$) and Gaussian errors for optimality.
3. **Weighting**: Use inverse error covariances ($\mathbf{B}^{-1}$, $\mathbf{R}^{-1}$) to balance observations and background.

---

## Critical Differences
| Aspect                | **OI**                        | **3DVar**                     | **Kalman Filter**             |
|-----------------------|-------------------------------|-------------------------------|--------------------------------|
| **Covariance**        | Static $\mathbf{B}$           | Static $\mathbf{B}$           | Dynamic $\mathbf{P}_{k\|k}$ |
| **Computation**       | Direct matrix inversion       | Gradient-based minimization   | Recursive covariance updates   |
| **Error Propagation** | None (stationary)             | None (stationary)             | Model-derived ($\mathbf{F}$)|
| **Cost**              | Low                           | Moderate                      | High (ensemble/recursive)      |
| **Typical Use**       | Historical systems            | Operational NWP               | Adaptive systems, AI models    |

---

### Hybrid Methods (e.g., 3DEnVar)
Combine static $\mathbf{B}$ (from 3DVar) with ensemble-derived covariances (from EnKF) for flow-dependent updates. Example:
$$
\mathbf{B}_{\text{hybrid}} = \alpha \mathbf{B}_{\text{static}} + \beta \mathbf{B}_{\text{ensemble}}
$$

# References

1. [用最少的公式讲解几种传统数据同化方法的发展路径 | 2025](https://mp.weixin.qq.com/s?__biz=MzkxNjczOTc3Nw==&mid=2247486030&idx=1&sn=327f7931f068c47c416decfba2be0bbd&)
   1. 但凡提到传统同化方法的不足，我们言必及“**线性-高斯分布假设**”，然而我们在EKF中就已经适用非线性模式了，这个”线性-高斯分布假设“到底说的是什么呢？
   2. 一个根本性的问题，不确定性是否等价于误差协方差矩阵的大小？在自然界中，高斯分布（正态分布）是一个完美的分布，因为它仅使用均值和方差这两个关键参数就可以完全确定其形状和位置。
   3. 事实上，只有高斯分布才能只用协方差矩阵就完全确定其概率分布。**所以在同化算法中默认用协方差矩阵表示不确定性其实已经默认了对象的分布是高斯分布**。
   4. 在此情况下公式(3)显然是包含高斯分布假设的，所以一切卡尔曼滤波方法都含有高斯分布假设。至于线性假设更容易说明：高斯分布的随机变量经过非线性变换后，通常不会保持高斯分布，非线性模式积分会改变状态变量的分布特征，使其偏离原始的高斯分布。所以如果假设数据一直符合高斯分布的话，那必然还默认了模式是线性的。
   5. 线性-高斯分布假设是卡尔曼滤波算法的先天不足，且四维变分方法也无法幸免。**因为只要是只用协方差矩阵来代表状态不确定性的算法，毫无疑问都可以说它受限于”线性-高斯分布假设“**。