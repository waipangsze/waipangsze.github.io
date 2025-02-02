---
layout: post
title: ML | Diffusion Models
categories: [ML]
tags: [ML, AI, pytorch]
author: wpsze
date: 2024-12-15 11:55:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/ArrrokC.png
banner_img: https://i.imgur.com/ArrrokC.png
---

# Diffusion Models

Diffusion Models are **generative models**, meaning that they are used to generate data similar to the data on which they are trained. Fundamentally, **Diffusion Models work by destroying training data through the successive addition of Gaussian noise, and then learning to recover the data by reversing this noising process**. After training, we can use the Diffusion Model to generate data by simply passing randomly sampled noise through the learned denoising process.

More specifically, a Diffusion Model is a **latent variable model** which maps to the latent space **using a fixed Markov chain**. This chain gradually adds noise to the data in order to obtain the approximate posterior $q(x_1:T|x_0)$, where $x_1, ..., x_T$ are the latent variables with the same dimensionality as $x_0$. In the figure below, we see such a Markov chain manifested for image data.

Ultimately, the image is asymptotically transformed to pure Gaussian noise. The goal of training a diffusion model is to learn the **reverse process**. By traversing backwards along this chain, we can generate new data.

![](https://i.imgur.com/BOHjnCW.png)
![](https://i.imgur.com/ArrrokC.png)

## Training

- Loss function: **Kullback-Leibler (KL) Divergences**. The KL Divergence is an asymmetric statistical distance measure of how much one probability distribution P differs from a reference distribution Q.

![](https://i.imgur.com/AGYXOQO.png)
![](https://i.imgur.com/EslrwLZ.png)

在擴散模型中，**反向過程**是學習數據分布的核心機制。以下是對這一過程的詳細闡述：

### 1. **反向過程的定義**
反向過程是指從一個完全噪聲化的樣本逐步去噪，以恢復出原始數據的過程。這個過程通常是在採樣階段進行，目標是從最終的噪聲數據 $x_T$ 開始，通過一系列逆向步驟，得到無噪聲的原始數據 $x_0$。

### 2. **前向過程與反向過程**
- **前向過程**是將數據逐步添加噪聲，形成一系列帶噪聲的樣本。數學上，這個過程可以表示為：
  $$
  q(x_t | x_{t-1}) = \mathcal{N}(x_t; \sqrt{1 - \beta_t} x_{t-1}, \beta_t I)
  $$
  其中 $\beta_t$ 是時間步 $t$ 的噪聲參數。

- **反向過程**則是從噪聲逐步去除，以恢復原始數據。通常假設為條件高斯分布：
  $$
  p_\theta(x_{t-1} | x_t) = \mathcal{N}(x_{t-1}; \mu_\theta(x_t, t), \Sigma_\theta(x_t, t))
  $$
  {% note primary %}
  這裡 $\mu_\theta$ 和 $\Sigma_\theta$ 是由神經網絡學習得到的 **均值** 和 **協方差**。
  {% endnote %}

### 3. **學習反向過程**
為了學習反向過程，模型需要通過神經網絡近似條件概率分布 $p_\theta(x_{t-1} | x_t)$。具體步驟如下：

- **初始化**：從標準正態分布 $N(0, I)$ 中隨機抽取初始樣本 $x_T$。

- **逐步去噪**：從時間步 $T$ 開始，逐步向前推進到時間步 $1$，在每個時間步 $t$ 中進行以下操作：
  - 抽取噪聲 $z$，當 $t > 1$ 時，從標準正態分布中抽取。
  - 使用 **參數化的均值和協方差進行去噪**：
    $$
    x_{t-1} = \frac{1}{\sqrt{\alpha_t}}(x_t - \frac{1 - \alpha_t}{\sqrt{1 - \bar{\alpha}_t}} \epsilon_\theta(x_t, t)) + \sigma_t z
    $$
    其中 $\alpha_t$ 和 $\bar{\alpha}_t$ 是預定義的噪聲參數，$\epsilon_\theta(x_t, t)$ 是 **神經網絡預測的噪聲**，$\sigma_t$ 是調整噪聲幅度的參數。

### 4. **目標函數**
為了優化神經網絡，模型通過最小化負對數似然來學習反向過程。可以使用變分下界（ELBO）來表示損失函數：
$$
L = L_0 + L_1 + ... + L_T
$$
其中每項損失 $L_t$ 實際上是兩個高斯分布之間的 **KL散度** ，可以明確地寫為相對於均值的L2損失。

### 5. **總結**
通過上述步驟，擴散模型能夠有效地學習到數據的複雜分布。**在訓練完成後，可以從標準正態分布中隨機抽取一個噪聲圖像，然後利用學到的反向過程將其轉化為與訓練數據相似的新圖像**。這種方法在圖像生成、音頻合成等領域展示了強大的應用潛力。

# Details

{% gi 12 2-2-2-2-2-2 %}
![](https://i.imgur.com/3fnkgvt.png)
{% endgi %}

# References

1. [由浅入深了解Diffusion Model](https://zhuanlan.zhihu.com/p/525106459?utm_psn=1869188646894170112)
2. [【生成式AI】淺談圖像生成模型 Diffusion Model 原理](https://www.youtube.com/playlist?list=PLJV_el3uVTsNi7PgekEUFsyVllAJXRsP-)
3. [理解 Stable Diffusion UNet 网络](https://blog.cnbang.net/tech/3823/)
4. [Introduction to Diffusion Models for Machine Learning](https://www.assemblyai.com/blog/diffusion-models-for-machine-learning-introduction/)