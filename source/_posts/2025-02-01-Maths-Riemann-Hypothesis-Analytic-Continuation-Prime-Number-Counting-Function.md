---
layout: post
title: Maths | 黎曼猜想/解析延拓/素數計數函數
categories: [Maths]
tags: [NWP, AI, ML]
author: wpsze
date: 2025-02-01 12:38:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://upload.wikimedia.org/wikipedia/commons/thumb/c/cb/Ueber_die_Anzahl_der_Primzahlen_unter_einer_gegebenen_Gr%C3%B6sse.pdf/page1-718px-Ueber_die_Anzahl_der_Primzahlen_unter_einer_gegebenen_Gr%C3%B6sse.pdf.jpg
banner_img: https://upload.wikimedia.org/wikipedia/commons/thumb/c/cb/Ueber_die_Anzahl_der_Primzahlen_unter_einer_gegebenen_Gr%C3%B6sse.pdf/page3-360px-Ueber_die_Anzahl_der_Primzahlen_unter_einer_gegebenen_Gr%C3%B6sse.pdf.jpg
---

# Euler Product Formula

The **Euler product formula** is a significant result in number theory that *connects the Riemann zeta function to prime numbers*. It states that the Riemann zeta function can be expressed both as an infinite series and as an infinite product over all prime numbers:

$$
\zeta(s) = \sum_{n=1}^{\infty} \frac{1}{n^s} = \prod_{p \text{ prime}} \frac{1}{1 - p^{-s}}
$$

- **Left Side**: The left-hand side represents the Riemann zeta function, which is defined for complex numbers $s$ with a real part greater than 1. This series converges for those values of $s$.
  
- **Right Side**: The right-hand side is the product over all prime numbers $p$, where each term $\frac{1}{1 - p^{-s}}$ can be expanded into a geometric series. This formulation highlights the deep connection between prime numbers and the distribution of integers.

## Historical Context

**Leonhard Euler first proved this formula in his work Variae observationes circa series infinitas in 1737**, demonstrating how the zeta function encapsulates properties of prime numbers. The proof uses basic algebraic manipulations and the concept of multiplicative functions, showcasing the relationship between the sum of reciprocals of integers raised to a power and the product over primes.

# Riemann Hypothesis

The **Riemann Hypothesis** is one of the most famous unsolved problems in mathematics, originally conjectured by Bernhard Riemann in 1859. It posits that all non-trivial zeros of the Riemann zeta function lie on a specific line in the complex plane known as the **critical line**, defined by:

$$
\text{Re}(s) = \frac{1}{2}
$$

## Details of the Hypothesis

- **Trivial Zeros**: The zeta function has trivial zeros at negative even integers (e.g., -2, -4, -6,...).
  
- **Non-Trivial Zeros**: The hypothesis asserts that all non-trivial zeros (those not at negative even integers) are located within a critical strip defined by $0 < \text{Re}(s) < 1$, specifically on the line where $\text{Re}(s) = \frac{1}{2}$.

# 黎曼猜想（Riemann Hypothesis）


黎曼猜想（Riemann Hypothesis）是數學中最重要和最著名的未解問題之一。黎曼猜想是由德國數學家波恩哈德·黎曼於1859年提出的。它與素數的分佈密切相關，特別是與素數計數函數（π(x)）的性質有關。

## 1. 黎曼猜想
黎曼猜想聲稱，所有非平凡的黎曼ζ函數的零點都位於複數平面上，實部為 1/2 的直線上，即 $\frac{1}{2} + it$ 的形式，其中 $t$ 是實數。

## 2. 解析延拓 (analytic continuation)
黎曼ζ函數最初是對於實部大於 1 的數進行定義的，然後通過解析延拓（[analytic continuation](https://en.wikipedia.org/wiki/Analytic_continuation)）擴展到其他區域。這種延拓使得ζ函數在整個複數平面上（除了 $s = 1$ 的極點）都具有良好的性質。

## 3. 素數(質數)計數函數
素數計數函數 $\pi(x)$ 定義為小於或等於 $x$ 的素數的數量。黎曼猜想的正確性與素數的分佈密切相關，特別是它預測了素數的分佈模式。(例如 $\pi(10) = 4$，因為不超過10 的素數有 2, 3, 5, 7 。)

![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/dc/PrimePi.svg/800px-PrimePi.svg.png){width=400}

Of great interest in number theory is the growth rate of the prime-counting function. It was conjectured in the end of the 18th century by **Gauss** and by **Legendre** to be approximately

$$
\begin{align}
\lim_{x\rightarrow\infty} \frac{\pi(x)}{x/\log x}=1 \\
\lim_{x\rightarrow\infty}\frac{\pi(x)}{\operatorname{li}(x)} = 1
\end{align}
$$

This statement is the prime number theorem and its equivalent statement where $li$ is the **logarithmic integral function**. The prime number theorem was first proved in 1896 by Jacques Hadamard and by Charles de la Vallée Poussin independently, using properties of the Riemann zeta function introduced by Riemann in 1859. Proofs of the prime number theorem not using the zeta function or complex analysis were found around 1948 by Atle Selberg and by Paul Erdős (for the most part independently).

### More precise estimates

In 1899, de la Vallée Poussin proved that (for some positive constant $a$)
$$
\pi(x) = \operatorname{li} (x) + O \left(x e^{-a\sqrt{\log x}}\right) \quad\text{as } x \to \infty 
$$

In 2002, Kevin Ford proved that,
$$
\pi(x) = \operatorname{li} (x) + O \left(x \exp \left( -0.2098(\log x)^{3/5} (\log \log x)^{-1/5} \right) \right)
$$

Mossinghoff and Trudgian proved an explicit upper bound for the difference between $\pi(x)$ and $li(x)$, 
$$
\bigl| \pi(x) - \operatorname{li}(x) \bigr| \le 0.2593 \frac{x}{(\log x)^{3/4}} \exp \left( -\sqrt{ \frac{\log x}{6.315} } \right) \quad \text{for } x \ge 229
$$

![](https://www.changhai.org/articles/science/mathematics/riemann_hypothesis/images/prime_dist.gif){width=400}

現在有沒有一個公式可以比質數定理更精確地描述質數的分配呢？

### Exact form !!!

For $x > 1$, let $\pi_0(x) = \pi(x) - \frac{1}{2}$ when $x$ is a prime number, and $\pi_0(x) = \pi(x)$ otherwise. **Bernhard Riemann, in his work On the Number of Primes Less Than a Given Magnitude**, proved that $\pi_0(x)$ is equal to

$$
\pi_0(x)=R(x)-\sum_{\rho} R(x^{\rho}),
$$
where
$R(x)=\sum_{n=1}^{\infty} \frac{\mu(n)}{n}\text{ li}\left(x^{1/n}\right)$,

$\mu(n)$ is the **Möbius function**, $\text{li}(x)$ is the **logarithmic integral function**, $\rho$ indexes **every zero of the Riemann zeta function**, and $\text{li}(x^\rho)$ is not evaluated with a branch cut but instead considered as Ei$(\frac{\rho}{n}\log x)$ where Ei$(x)$ is the **exponential integral**. If the trivial zeros are collected and the sum is taken only over the non-trivial zeros $\rho$ of the Riemann zeta function, then $\pi_0(x)$ may be approximated.

$$
\pi_0(x)\approx R(x)-\sum_{\rho} R(x^{\rho})-\frac{1}{\log x}+\frac{1}{\pi}\arctan\frac{\pi}{\log x}
$$

The Riemann hypothesis suggests that every such non-trivial zero lies along $Re(s) = \frac{1}{2}$. 

這樣，我們可以說，如果我們知道了黎曼zeta函數的所有非平凡零點的精確位置，那麼就有了計算素數計數函數的精確公式。

![Riemann's explicit formula using the first 200 non-trivial zeros of the zeta function](https://upload.wikimedia.org/wikipedia/commons/8/89/Riemann_Explicit_Formula.gif){width=400}

## 4. 對數積分函數

The logarithmic integral has an integral representation defined for all positive real number $x \neq 1$,
$$
\operatorname{li}(x) = \int_0^x \frac{dt}{\ln t}
$$

$$
\operatorname{li}(x) = \lim_{\varepsilon \to 0+} \left( \int_0^{1-\varepsilon} \frac{dt}{\ln t} + \int_{1+\varepsilon}^x \frac{dt}{\ln t} \right)
$$

![](https://upload.wikimedia.org/wikipedia/commons/thumb/a/ad/Logarithmic_integral_function.svg/2880px-Logarithmic_integral_function.svg.png){width=400}

對數積分函數 (The offset logarithmic integral or Eulerian logarithmic integral)定義為：
$$
\text{Li}(x) = \int_2^x \frac{dt}{\ln t} = \operatorname{li}(x) - \operatorname{li}(2)
$$
這個積分從 2 開始，因為我們在考慮素數的時候，1 不是素數，而 $\log t$ 是自然對數。

- $\text{Li}(x)$ 是一個單調遞增的函數，隨著 $x$ 的增大，$\text{Li}(x)$ 會增大。
- 根據素數定理，對於大 $x$，素數計數函數 $\pi(x)$ 和對數積分函數之間的關係可以用漸近公式來表示：
$$
\pi(x) \sim \text{Li}(x) \text{（對於大 } x\text{）}
$$
這意味著當 $x$ 趨近於無限大時，$\pi(x)$ 和 $\text{Li}(x)$ 之間的比值趨近於 1。



$$
\Pi_0(x) = \operatorname{li}(x) - \sum_\rho \operatorname{li}(x^\rho) -\log 2 + \int_x^\infty\frac{dt}{t(t^2-1) \log t}
$$

# Videos

<iframe width="560" height="315" src="https://www.youtube.com/embed/T93SayXhw2w" title="1+2+3+4+...=-1/12 ？李永樂老師講黎曼猜想（1）" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/4vbcC4TcMGc" title="質數有多重要？數學家歐拉和高斯是如何研究質數的 ？李永樂老師講黎曼猜想（2）" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/NeoDdnSlRjk" title="懸賞100萬美元的“黎曼猜想”有多難？李永樂老師講什麽是黎曼猜想（3）" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/Y86kfiVEXd4" title="朗道-西格爾零點是什麼？張益唐證明了黎曼猜想嗎？" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/sD0NjbwqlYw" title="But what is the Riemann zeta function? Visualizing analytic continuation" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

# References

1. [On the Number of Primes Less Than a Given Magnitude](https://en.wikipedia.org/wiki/On_the_Number_of_Primes_Less_Than_a_Given_Magnitude)
   1. ![Bernhard Riemann's article concerning the number of primes that are less than a given magnitude.](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cb/Ueber_die_Anzahl_der_Primzahlen_unter_einer_gegebenen_Gr%C3%B6sse.pdf/page1-718px-Ueber_die_Anzahl_der_Primzahlen_unter_einer_gegebenen_Gr%C3%B6sse.pdf.jpg){width=200}
2. [【直观详解】通俗易懂了解什么是黎曼猜 | 2018](https://charlesliuyx.github.io/2018/09/20/%E3%80%90%E7%9B%B4%E8%A7%82%E8%AF%A6%E8%A7%A3%E3%80%91%E9%80%9A%E4%BF%97%E6%98%93%E6%87%82%E4%BA%86%E8%A7%A3%E4%BB%80%E4%B9%88%E6%98%AF%E9%BB%8E%E6%9B%BC%E7%8C%9C%E6%83%B3/)
3. [Riemann 猜想漫谈 | 卢昌海 (**推薦**)](https://www.changhai.org/articles/science/mathematics/riemann_hypothesis/)
4. [數學界的聖杯「黎曼猜想證明」，是否已被人類得手？|2018](https://pansci.asia/archives/147956)