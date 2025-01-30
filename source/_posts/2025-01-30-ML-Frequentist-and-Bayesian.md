---
layout: post
title: ML | Frequentist and Bayesian (頻率派 和 貝葉斯派)
categories: ML
tags: [NWP, ML, AI, ML]
author: wpsze
date: 2025-01-30 17:22:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/6oQocBX.png
banner_img: https://i.imgur.com/6oQocBX.png
---

**貝葉斯派和頻率派**。在學習機器學習的過程中，應該聽過很多次這兩個專業詞彙，剛開始學不用過於深究，這不會影響你學習各種模型，當你了解完很多模型演算法後，再回過頭來看貝葉斯派和頻率派，就會有種醍醐灌頂的感覺，你在每種模型上都可以找到它們的哲學思想、方法論。

- 貝葉斯公式： $P(\theta|x)=\frac{P(x|\theta)P(\theta)}{P(x)}$
- 先驗機率指的是 $P(\theta)$ 
- 似然機率指的是$P(x|\theta)$ 
- 後驗機率指的是$P(\theta|x)$

是兩種不同的看待世界的方法論：**頻率派把模型參數看成未知的定量**，**最大概似估計（Maximum likelihood estimation, MLE)** 求解參數，往往最後變成最佳化問題。這一分支又被稱為統計學習。 

 - The MLE is defined as:
   $$
   \hat{\theta}_{MLE} = \arg \max_{\theta} P(X | \theta)
   $$

**貝葉斯派把模型參數看成未知的變數（機率分佈）**，**以 最大後驗概率估計(Maximum A Posteriori estimation, MAP)** 求解參數。
 - The MAP estimation is defined as:
   $$
   \hat{\theta}_{MAP} = \arg \max_{\theta} P(\theta |X) = \arg \max_{\theta} P(X | \theta) P(\theta)
   $$

- Here, $P(X | \theta)$ is the likelihood function, and $P(\theta)$ is the prior distribution. The MAP estimation can be seen as a modification of MLE that includes this prior information.

其實最大化後驗機率還不算純粹的貝葉斯派，純粹的貝葉斯派是求出具體的後驗，難度很大，要進行積分，也就是要求出 $P(x)$ 這一項，**像是蒙特卡羅方法、機率圖模型**。

$$
P(\theta|x)=\frac{P(x|\theta)P(\theta)}{P(x)} = \frac{P(x|\theta)P(\theta)}{\int P(x|\theta)P(\theta) d\theta}
$$

可以看到兩者最大的差異在於對參數的認知。

{% note primary %}
- **頻率派認為參數是常數，資料是變數**；
- **貝葉斯派則認為參數是變量，不可能求出固定的參數，資料是常數**。
{% endnote %}

| Frequentist  | Bayesian  | 
|:-------------:|:---------------:| 
| 頻率論方法透過大量獨立實驗將機率解釋為統計平均值（大數定律         | 貝葉斯方法則將機率解釋為信念度（degree of belief）（不需要大量的實驗）          | 
| 頻率學派把未知參數看成普通變數（固定值），把樣本看成隨機變數         | 貝葉斯學派把一切變數看成隨機變量          | 
| 頻率論僅僅利用抽樣數據         | 貝葉斯理論善於利用過去的知識和抽樣數據          | 

# Least squares method, Cross entropy, L1 regularization (Lasso), L2 regularization

## Least squares method

**最小二乘法 (least squares) 就是 雜訊符合常態分佈的 最大概似估計 (MLE) 的數學形式**。從機率角度給出了最小平方法的理論支撐。我們發現頻率派，往往轉換為極大似然法問題，也就是最優化求極值問題，這也被稱為統計學習，像決策樹，支持向量機都有最優化思想，都屬於這一分支。

$$
\begin{align}
y &= f(x) + \epsilon \qquad ; \epsilon \sim N(0, \sigma^2)\\
f(x) &= w^Tx \\
y|w,x &\sim N(w^T x, \sigma^2) \qquad ; \textrm{by given w and x} \\
P(y|w,x) &= \dfrac{1}{\sqrt{2 \pi}\sigma}\exp ( - \dfrac{(y - w^Tx)^2}{2\sigma^2}) \\
\textrm{By MLE,}\\
L(w) &= \log P(Y|X,w)\\
&= \log \Pi P(y_i|x_i, w)\\
&= \sum \log P(y_i|x_i,w)\\
&= \sum ( \log (\dfrac{1}{\sqrt{2\pi}\sigma}) - \dfrac{(y - w^Tx)^2}{2\sigma^2})\\
w &= \textrm{argmax} L(w) = \textrm{argmax} (- \dfrac{(y - w^Tx)^2}{2\sigma^2}) = \textrm{argmin} (y - w^Tx)^2
\end{align} 
$$


## Cross-Entropy Loss Function

Cross-entropy is a fundamental concept in information theory and machine learning, **measuring the difference between two probability distributions**. It quantifies how well one probability distribution $q$ approximates another distribution $p$ over the same set of events.

### Definition

In mathematical terms, the cross-entropy $H(p, q)$ between two discrete probability distributions $p$ and $q$ is defined as:

$$
H(p, q) = -\sum_{x \in \mathcal{X}} p(x) \log(q(x))
$$

where $p(x)$ is the true distribution and $q(x)$ is the estimated distribution. This formula reflects the average number of bits needed to identify an event drawn from the true distribution when using a coding scheme optimized for the estimated distribution.

#### Relation to Kullback-Leibler Divergence

Cross-entropy is closely related to Kullback-Leibler (KL) divergence, which measures how one probability distribution diverges from a second, expected probability distribution. The relationship can be expressed as:

$$
D_{\mathrm{KL}}(p \parallel q) = H(p, q) - H(p)
$$

where $H(p)$ is the entropy of the true distribution $p$. This indicates that minimizing cross-entropy also minimizes KL divergence when $p$ is fixed

#### Binary classification

The cross-entropy loss function (also known as log loss) can be expressed for binary classification as:

$$
L(y, \hat{y}) = -[y \log(\hat{y}) + (1 - y) \log(1 - \hat{y})]
$$

where:
- $y$ is the true label (0 or 1),
- $\hat{y}$ is the predicted probability of the positive class.

For multiclass classification, it generalizes to:

$$
L(y, \hat{y}) = -\sum_{i=1}^{M} y_i \log(\hat{y}_i)
$$

where $M$ is the number of classes, and $y_i$ and $\hat{y}_i$ are the true label and predicted probability for class $i$, respectively.

## Regularization

$$
\begin{align}
y &= f(x) + \epsilon \qquad ; \epsilon \sim N(0, \sigma^2)\\
f(x) &= w^Tx \\
y|w &\sim N(w^T x, \sigma^2) \qquad ; \textrm{by given w and constant x} \\
P(y|w) &= \dfrac{1}{\sqrt{2 \pi}\sigma}\exp ( - \dfrac{(y - w^Tx)^2}{2\sigma_0^2}) \\
P(w) &= \dfrac{1}{\sqrt{2 \pi}\sigma}\exp ( - \dfrac{||w||)^2}{2\sigma_1^2}) \\
\textrm{By MAP,}\\
L(w) &= \log P(w|Y)\\
&= \log \Pi \dfrac{P(Y|w)P(w)}{P(y)}\\
&\sim \sum \log P(Y|w)P(w)\\
&= \sum ( \log (\dfrac{1}{\sqrt{2\pi}\sigma}) - \dfrac{(y - w^Tx)^2}{2\sigma_0^2} - \dfrac{||w||)^2}{2\sigma_1^2})\\
w &= \textrm{argmax} L(w) = \textrm{argmax} (- \dfrac{(y - w^Tx)^2}{2\sigma_0^2} - \dfrac{||w||)^2}{2\sigma_1^2}) = \textrm{argmin} ((y - w^Tx)^2 + \dfrac{\sigma_0^2}{\sigma_1^2}||w||^2)
\end{align} 
$$

### L1 Regularization (Lasso)

**Objective Function**: The L1 regularized least squares problem, often referred to as **Lasso regression**, modifies the standard least squares loss function by adding a penalty term based on the absolute values of the coefficients. The objective function can be expressed as:

$$
L_{L1} = \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2 + \lambda \sum_{j=1}^{p} |w_j|
$$

### L2 Regularization (Ridge)

**Objective Function**: The L2 regularized least squares problem, known as **Ridge regression**, similarly alters the least squares loss function by incorporating a penalty based on the squared values of the coefficients. The objective function is given by:

$$
L_{L2} = \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2 + \lambda \sum_{j=1}^{p} w_j^2
$$

發現**L2正則化就是假設參數符合常態分佈**的最大後驗法的數學形式！同理可得**L1正規化是假設參數符合拉普拉斯分佈**的最大後驗法。我們現在可以從機率角度解釋正規化到底在做什麼了。

{% note primary %}
**正規化就是引入了先驗知識**
{% endnote %}

我們知道世界上大多數事件是服從常態分佈的，像是身高、體重、成績等等。因此我們假設參數也符合常態分佈。引入先驗知識有什麼好處呢，我們現在拋一枚硬幣，50次中有30次都是正面向上，問你拋這枚硬幣的機率分佈，這時你想起你人生中遇到的大多數硬幣都是均勻的，儘管數據顯示不均勻，你還是會認為這枚硬幣是均勻的。如果你是這麼想的，**那你就引入了先驗知識。因此引入先驗知識在數據不足的時候有很大好處**。

{% note primary %}
看到這裡你不禁產生這樣的疑惑，最大後驗法也是轉換成最佳化問題啊，跟頻率派有什麼差別嗎？對的，最大後驗法不是原汁原味的貝葉斯派，它是貝葉斯派的妥協，因為求積分

$$
\int P(x|\theta)P(\theta) d\theta
$$

**太難了，所以最大後驗法是貝氏派向頻率派的妥協**。因為那積分太難求了，就預設其是一個定值，argmax之後，分母是定值那麼分子最大即可了；所以這就是頻率學派攻擊貝葉斯的落腳點：你這麼牛逼怎麼不把分母求出呢？貝葉斯妥協了
{% endnote %}

# More,

- 頻率派認為模型參數是客觀存在的，它就在那裡。因此把參數看成常數，如果有一個全知全能神，就能告訴你參數值是多少，當資料量成千上萬時，我們可以不斷逼近那個真實的參數。
- 貝葉斯派認為認為一切機率都是主觀的，因此把參數看成變量，不存在客觀存在的機率。
  - 在證明L2正規化時，你肯定想問為什麼假設參數的先驗分佈是常態分佈，沒錯，這就是主觀的，是主觀臆斷的。這也是頻率派常常抨擊貝葉斯派的一點，先驗分佈該如何取得？貝葉斯派是這樣辯護的：先驗分佈如何取得不重要，重要的是，我們可以不斷假設，不斷修改。而先驗分佈不是完全瞎猜的，而是基於我之前的人生經驗，例如太陽東升西落，硬幣總是50%向上。
- 具體到點估計的情形，其實貝葉斯和頻率派沒有什麼差別，因為從極大似然估計的角度來說，給參數加先驗其實可以被看作是加一個正則項，所以你用貝葉斯方法得到的最大後驗估計就等價於帶了正規項的極大似然估計。
  - 當然在大多數情況下，我們用貝葉斯方法是想算出參數的後驗分佈。有了後驗分佈你就相當於擁有了參數的所有信息，你想要最大後驗估計就用最大後驗估計，想要用後驗均值當估計量就用後驗均值當估計量，並且連算信賴區間的力氣都省
  - 你可以從後驗分佈直接找到信賴區間，而不需要像頻率派那樣苦哈哈地去算估計量的漸進分佈（Asymptotic Distribution），或是跑慢死人不償命的Bootstrap。所以有句玩笑，叫做如果你是聰明人那就去研究頻率派，但如果你是個懶人那就去搞貝葉斯，因為會算會跑模擬就行了（逃當然一切都是有代價的，具體到計算層面上，貝葉斯在許多模型的計算上都比頻率學派的要慢一大截。貝葉斯方法往往要求我們模擬整個後驗分佈（在大多數情況下後驗分佈/點估計都沒有明確解）。 （Importance Sampling）和拒絕採樣（Rejection Sampling）就別指望了，想算就只能用MCMC （馬爾科夫鏈蒙特卡洛），也就是透過模擬一條馬可夫鏈來模擬後驗樣本。
  - 不過近年來大家也開始對變分推論這種方法更感興趣（idea是用簡單分佈的組合去逼近複雜的後驗分佈）。目前來看變分推論的表現非常不錯，唯一的問題是會引入偏差，無法重建真正的後驗分佈，當然這就看這套方法接下來怎麼發展了。

# References

1. [机器学习中，频率派和贝叶斯派有什么核心差异？](https://www.zhihu.com/question/268906722/answer/2842695236)
2. [统计学里频率学派(Frequentist)与贝叶斯(Bayesian)学派的区别和在机器学习中的应用](https://zhuanlan.zhihu.com/p/337637096)