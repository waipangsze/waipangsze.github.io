---
layout: post
title: ML | Diffusion Models (Denoising diffusion probabilistic models)
categories: [ML]
tags: [ML, AI, pytorch, Diffusion, UNet]
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

- [Ho, Jonathan, Ajay Jain, and Pieter Abbeel. "Denoising diffusion probabilistic models." Advances in neural information processing systems 33 (2020): 6840-6851.](https://proceedings.neurips.cc/paper/2020/file/4c5bcfec8584af0d967f1ab10179ca4b-Paper.pdf)

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
![](https://i.imgur.com/JMP2TcX.png)
{% endgi %}

## 重參數化技巧 (Reparameterization trick)

重參數化技巧在許多工作 (VAE) 中有所引用。如果我們要從某個分佈中隨機取樣 (高斯分佈) 一個樣本，這個過程是無法反傳梯度的。而這個經由高斯雜訊取樣得到 $x_t$ 的過程在diffusion中到處都是，因此我們需要透過重參數技巧來使得他可微。最通常的做法是吧隨機性透過一個獨立的隨機變數 $(\epsilon)$ 引導過去。舉個例子，如果要從高斯分佈 $z\sim\mathcal{N}(z;\mu_\theta,\sigma_\theta^2\mathbf{I})$ 取樣一個 $z$,我們可以寫成：

$$
z=\mu_\theta+\sigma_\theta\odot\epsilon,\epsilon\sim\mathcal{N}(0,\mathbf{I})
$$

上式的z依舊是有隨機性的，且滿足平均值為 $\mu_\theta$ 變異數為 $\sigma_\theta^2$ 的高斯分佈。這裡的 $\mu_\theta$ ,$\sigma_\theta^2$可以是由參數 $\theta$ 的神經網路推論得到的。整個「採樣」過程依舊梯度可導，隨機性被轉嫁到了$\epsilon$上。

## Forward Diffusion Process
### 任意時刻的$x_t$可以由$x_0$和$\beta$表示

![Forward Diffusion Process - add noise to the image](https://i.imgur.com/JtK0R8X.png){width=600}

透過這個 Reparameterization trick，我們可以將取樣影像 $x_t$ 表示如下：

$$
\begin{align}
z &= \mu + \sigma \epsilon \qquad ;\epsilon \sim N(0,I)\\
x_t &= \sqrt{1-\beta_t} x_{t-1} + \sqrt{\beta_t} \epsilon_{t-1} \\
&= \sqrt{a_t} x_{t-1} + \sqrt{1 - a_t} \epsilon_{t-1} \\
\end{align}
$$

能夠透過 $x_0$ 和 $\beta$ 快速得到 $x_t$ 對後續 diffusion model 的推論和推導有巨大作用。

{% gi 12 2-2-2-2-2-2 %}
![](https://i.imgur.com/dYILzWB.png)
![](https://i.imgur.com/lpFiGtT.png)
{% endgi %}

{% note primary %}
現在我們可以使用這個公式在任何時間步直接取樣 $x_t$，這使得 Forward Diffusion Process 更快, 
$$
x_t = \sqrt{1-\bar{\alpha}_t} x_{0} + \sqrt{1-\bar{\alpha}_t} \epsilon
$$
{% endnote %}

## Reverse Diffusion Process

![](https://i.imgur.com/2g9HXta.png){width=600}

{% note primary %}
Unlike the forward process, we cannot use $q(x_{t-1}|x_t)$ to reverse the noise since it is **intractable** (**uncomputable**).
{% endnote %}

- Thus we need to train a neural network $p_\theta(x_{t-1}|x_t)$ to approximate $q(x_{t-1}|x_t)$ . 
- The approximation $p_\theta(x_{t-1}|x_t)$ follows **a normal distribution** and **its mean and variance** are set as follows

$$
\begin{align}
\mu_\theta &:= \tilde{\mu}_t(x_t, x_0)\\
\Sigma_\theta &:= \tilde{\beta}_t \mathbf{I}
\end{align}
$$

**Loss Function**: We can define our loss as a **Negative Log-Likelihood**:

![](https://i.imgur.com/i69Cxwx.png){width=400}

**This setup is very similar to the one in VAE**. instead of optimizing the intractable loss function itself, we can optimize the **Variational Lower Bound**.

![](https://i.imgur.com/GAvchZh.png){width=700}

1. $L_T$ Constant term
2. $L_{t-1}$ Stepwise denoising term
   1. ![](https://i.imgur.com/672L1Hw.png){width=600}
   2. To approximate the target denoising step $q$, we only need to approximate its mean using a neural network. So **we set the approximated mean** $\mu_\theta$ to be in the **same form as the target mean** $\tilde{\mu}_t$ (with **a learnable neural network** $\epsilon_\theta$)
   3. Experimentally, better results can be achieved by ignoring the weighting term and simply comparing the target and predicted noises with MSE.
   4. So, it turns out that to approximate the desired denoising step $q$, we just need to approximate the noise $\epsilon_t$ using a neural network $\epsilon_\theta$.
3. $L0$ Reconstruction term
   1. This is the reconstruction loss of the last denoising step and it can be ignored during training for the following reasons: It can be approximated using the same neural network in $L_{t-1}$. Ignoring it makes the sample quality better and makes it simpler to implement.

### Simplified Loss (ToDo)

So the final simplified training objective ([Ho et al. (2020)](https://arxiv.org/abs/2006.11239) empirically found that training the diffusion model works better with a simplified objective that ignores the weighting term)  is as follows:

![](https://i.imgur.com/sMyAqVN.png){width=400}

## With UNet model ***

- A random time step t will be selected for each training sample (image).
- Apply the Gaussian noise (corresponding to t) to each image.
- Convert the time steps to embeddings (vectors).

![](https://i.imgur.com/PmD5Azy.png){width=600}

## Training

![](https://i.imgur.com/bnNS9z4.png){width=400}

### training step

The official training algorithm is as above, and the following diagram is an illustration of how a training step works

![](https://i.imgur.com/LgIOlQO.png){width=600}

## Reverse Diffusion (Sampling)

![](https://i.imgur.com/F2EuVPU.png){width=400}

### generating step

We can generate images from noises using the above algorithm. The following diagram is an illustration of it. Note that in the last step, we simply output the learned mean $\mu_\theta(x_{t=1}, t=1)$ without adding the extra noise to it.

![](https://i.imgur.com/wQIehey.png){width=600}

## Summary

Summary Here are some main takeaways from this article:

- The Diffusion model is divided into two parts: forward diffusion and reverse diffusion.
- The forward diffusion can be done using the closed-form formula.
- The backward diffusion can be done using a trained neural network.
- To approximate the desired denoising step q, we just need to approximate the noise $\epsilon_t$ using a neural network $\epsilon_\theta$.
- Training on the simplified loss function yields better sample quality.

# Literature review

- An example of training a diffusion model for modeling a 2D swiss roll data. (Image source: [Sohl-Dickstein et al., 2015](https://arxiv.org/abs/1503.03585))
  - ![](https://i.imgur.com/oVtF9Tt.png){width=500}

# Diffusion Models vs GANs vs VAEs

擴散模型（Diffusion Models）與傳統的生成對抗網絡（GANs）和變分自編碼器（VAEs）相比，具有多項顯著的優勢和特點。以下是這三種生成模型的比較：

## 1. **穩定性**
- **擴散模型**：訓練過程相對穩定，不容易出現梯度消失或爆炸等問題。這使得擴散模型在訓練時更易於優化，並且能夠生成高質量的樣本。
- **GANs**：訓練過程不穩定，容易出現模式崩潰（mode collapse），即生成器只能生成有限類型的數據，這限制了其多樣性。
- **VAEs**：雖然訓練過程較為穩定，但生成的數據多樣性相對較低，可能不適合需要高多樣性的應用。

## 2. **生成質量**
- **擴散模型**：能夠捕捉到更多的細節信息，生成更加真實、細膩的圖像。這使得擴散模型在圖像生成等任務中表現出色。
- **GANs**：在生成質量方面通常表現良好，特別是在需要高保真度的應用中，如圖像合成和風格轉換。
- **VAEs**：雖然訓練穩定，但生成的數據質量可能不如GANs高，因此在複雜數據生成任務中表現較弱。

## 3. **計算資源與效率**
- **擴散模型**：在訓練和生成過程中需要消耗大量計算資源，並且由於需要多次迭代和反向擴散過程，導致采樣速度較慢。
- **GANs**：通常需要較大的計算資源，但一旦訓練完成，其生成速度相對較快。
- **VAEs**：計算量相對較小，訓練過程較快，但可能無法產生足夠多樣化的數據。

## 4. **可控性**
- **擴散模型**：具有良好的可控性，可以通過調整擴散步長、噪聲強度等參數來精細控制生成結果，以滿足用戶需求。
- **GANs**：可控性較差，因為生成器和判別器之間的對抗性質使得難以直接控制輸出特徵。
- **VAEs**：通過隱變量進行控制，但可控性通常不如擴散模型強。

## 5. **應用場景**
- **擴散模型**：已在計算機視覺、自然語言處理、語音合成等多個領域取得成功應用，如圖像生成、超分辨率、去噪等任務。
- **GANs**：廣泛應用於圖像生成、風格轉換等需要高質量生成的場景。
- **VAEs**：更適合於數據分析、降維等應用場景，並且具有良好的解釋性。

## 總結
總體而言，擴散模型在穩定性、生成質量和可控性方面展現了顯著優勢，使其成為當前深度學習領域中一個重要的研究方向。隨著技術的不斷發展，擴散模型有望在更多應用中取代傳統的GANs和VAEs。

# References

1. [A Beginner's Guide to Diffusion Models: Understanding the Basics and Beyond | Subhradip Roy's Blog (**Recommend**)](https://roysubhradip.hashnode.dev/a-beginners-guide-to-diffusion-models-understanding-the-basics-and-beyond)
2. [What are Diffusion Models? (**Recommend**)](https://lilianweng.github.io/posts/2021-07-11-diffusion-models/#forward-diffusion-process)
3. [【生成式AI】淺談圖像生成模型 Diffusion Model 原理](https://www.youtube.com/playlist?list=PLJV_el3uVTsNi7PgekEUFsyVllAJXRsP-)
4. [理解 Stable Diffusion UNet 网络](https://blog.cnbang.net/tech/3823/)
5. [Introduction to Diffusion Models for Machine Learning](https://www.assemblyai.com/blog/diffusion-models-for-machine-learning-introduction/)