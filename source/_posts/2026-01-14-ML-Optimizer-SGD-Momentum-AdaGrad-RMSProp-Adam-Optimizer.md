---
layout: post
title: ML | Optimizer | SGD, Momentum, AdaGrad, RMSProp, Adam Optimizer
categories: [ML]
tags: [ML, AI, pytorch, Optimizer, SGD, Momentum, AdaGrad, RMSProp, Adam]
author: wpsze
date: 2026-01-14 06:17:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/jNLbGHh.gif
banner_img: https://i.imgur.com/jNLbGHh.gif
---

# Optimizer

In machine learning, an Optimizer is the **"engine" that minimizes the loss function**. It updates the weights and biases of a model based on the gradients calculated during backpropagation.

The evolution from basic **Stochastic Gradient Descent (SGD)** to **Adam** represents a journey of solving specific mathematical hurdles like **"pathological curvature" (oscillations)** and **"vanishing learning rates"**.

# Stochastic Gradient Descent (SGD)

Standard Gradient Descent computes the gradient for the entire dataset before making one update. SGD instead updates parameters using only one sample (or a small "mini-batch") at a time. 

$$\theta_{t+1} = \theta_t - \eta \cdot \nabla_{\theta} J(\theta_t; x^{(i)}, y^{(i)})$$

where $\eta$ is the learning rate and $J$ is the loss.

- **Intuition**: It is like a drunk person walking down a hill; they take frequent, noisy steps. This noise actually helps the model "jump" out of shallow local minima.
- **Problem**: In "ravines" (where the surface curves much more steeply in one dimension than another), SGD oscillates wildly across the slopes, making very slow progress toward the actual minimum.

# Momentum

- [Polyak, Boris T. "Some methods of speeding up the convergence of iteration methods." Ussr computational mathematics and mathematical physics 4.5 (1964): 1-17.](https://hengshuaiyao.github.io/papers/polyak64.pdf)
- [Sutskever, I., Martens, J., Dahl, G., & Hinton, G. (2013, May). **On the importance of initialization and momentum in deep learning**. In International conference on machine learning (pp. 1139-1147). pmlr.](https://www.cs.toronto.edu/~fritz/absps/momentum.pdf)
  - ![](https://i.imgur.com/vf1JFVf.png){width=400}

Momentum was designed to **solve the "oscillation" problem of SGD**. It adds a fraction of the previous update to the current one.

where $\mu$ (usually 0.9) is the "momentum" or friction.

- **Intuition**: Imagine a heavy ball rolling down a hill. It gains speed in the direction of the consistent downward slope and ignores minor bumps or side-to-side noise.
- **Why use it?** It speeds up convergence significantly and "dampens" the zig-zagging seen in plain SGD.

# AdaGrad

- [Duchi, J., Hazan, E., & Singer, Y. (2011). Adaptive subgradient methods for online learning and stochastic optimization. Journal of machine learning research, 12(7).](https://www.jmlr.org/papers/volume12/duchi11a/duchi11a.pdf)

AdaGrad's name comes from **Adaptative Gradient**. Intuitively, it adapts the learning rate for each feature depending on the estimated geometry of the problem; particularly, it tends to assign higher learning rates to infrequent features, which ensures that the parameter updates rely less on frequency and more on relevance.

AdaGrad was introduced by Duchi et al. in a highly cited paper published in the Journal of machine learning research in 2011. It is arguably one of the most popular algorithms for machine learning (particularly for training deep neural networks) and it influenced the development of the Adam algorithm.

![](https://i.imgur.com/echwH7z.png){width=400}

# RMSProp

- **Tieleman and Hinton (2012)** proposed the RMSProp algorithm as a simple fix to decouple rate scheduling from coordinate-adaptive learning rates.
- **RMSprop** is unpublished optimization algorithm designed for neural networks, first proposed by *Geoff Hinton* in **lecture 6 of the online course “Neural Networks for Machine Learning”**.

- [Neural Networks for Machine LearningLecture 6 | Overview of mini-­‐batch gradient descent | Geoffrey Hinton | 2018](https://www.cs.toronto.edu/~tijmen/csc321/slides/lecture_slides_lec6.pdf)

**RMSProp (Root Mean Square Propagation)** is an adaptive learning rate optimization algorithm for training deep neural networks, designed to make training faster and more stable by `adjusting the learning rate for each parameter **individually**`, preventing issues like vanishing/exploding gradients and slow convergence, especially in complex models.

![](https://i.imgur.com/zuNsQja.png){width=400}

# Adam

- [Adam: A Method for Stochastic Optimization | Diederik P. Kingma, Jimmy Ba | 2014](https://arxiv.org/abs/1412.6980)

(Adaptive Moment Estimation) Adam is currently the "gold standard." It essentially combines Momentum (storing the first moment: mean of gradients) and RMSProp (storing the second moment: uncentered variance of gradients).

- **Update Mean** ($m_t$): $m_t = \beta_1 m_{t-1} + (1-\beta_1)g_t$ (Like Momentum)
- **Update Variance** ($v_t$): $v_t = \beta_2 v_{t-1} + (1-\beta_2)g_t^2$ (Like RMSProp)
- **Bias Correction**: Adjust $m_t$ and $v_t$ because they are initialized at zero.
- **Update**: $\theta_{t+1} = \theta_t - \frac{\eta \cdot \hat{m}_t}{\sqrt{\hat{v}_t} + \epsilon}$
- **Why use it?** It is computationally efficient, requires little memory, and usually works well with "default" hyperparameters ($\beta_1 = 0.9, \beta_2 = 0.999$).

# References

1. [[機器學習ML NOTE]SGD, Momentum, AdaGrad, Adam Optimizer](https://medium.com/%E9%9B%9E%E9%9B%9E%E8%88%87%E5%85%94%E5%85%94%E7%9A%84%E5%B7%A5%E7%A8%8B%E4%B8%96%E7%95%8C/%E6%A9%9F%E5%99%A8%E5%AD%B8%E7%BF%92ml-note-sgd-momentum-adagrad-adam-optimizer-f20568c968db)
2. [**Why Momentum Really Works**](https://distill.pub/2017/momentum/)
3. [ML入門（十二）SGD, AdaGrad, Momentum, RMSProp, Adam Optimizer](https://medium.com/chung-yi/ml%E5%85%A5%E9%96%80-%E5%8D%81%E4%BA%8C-sgd-adagrad-momentum-rmsprop-adam-optimizer-e331ef3cf5cf)
4. [12.8. RMSProp](https://d2l.ai/chapter_optimization/rmsprop.html)
