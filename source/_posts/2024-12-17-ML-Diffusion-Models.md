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
index_img: 
banner_img: 
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

# References

1. [Introduction to Diffusion Models for Machine Learning](https://www.assemblyai.com/blog/diffusion-models-for-machine-learning-introduction/)
2. [李宏毅 Hung-yi Lee is very famous on teaching machine learning course in taiwan](https://www.youtube.com/playlist?list=PLJV_el3uVTsNi7PgekEUFsyVllAJXRsP-)
3. [理解 Stable Diffusion UNet 网络](https://blog.cnbang.net/tech/3823/)