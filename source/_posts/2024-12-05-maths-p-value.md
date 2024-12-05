---
layout: post
title: Maths | p-value
categories: [Maths]
tags: [NWP, AI, ML]
author: wpsze
date: 2024-12-05 13:31:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/EJrAqbw.png
banner_img: https://i.imgur.com/EJrAqbw.png
---

# p-value

In null-hypothesis significance testing, the p-value is the probability of obtaining test results at least as extreme as the result actually observed, under the assumption that the null hypothesis is correct.

p值（p value）就是當虛無假設為真時所得到的樣本觀察結果或更極端結果出現的概率。如果p值很小，說明在虛無假設下極端觀測結果的發生概率很小。而如果出現了，根據小概率原理，就有理由拒絕虛無假設；p值越小，拒絕虛無假設的理由越充分。

常常看到有人說p-value <0.05，所以達到統計上的意義，模型合理，但你真的知道這是什麼意思嗎? p 值是什麼？ p 值是由 Ronald Fisher 在 1920 年代發展出來的，已將近一百年。p 值檢定最開始，是檢定在一個 model 之下，實驗出來的 data 跟 model 到底吻合不吻合。這個被檢定的 model，我們把它叫做虛無假設（null hypothesis），一般情況下，這個被檢定的 model，是假設實驗並無系統性效應的，即效應是零，或是隨機狀態。在這個虛無假設之下，得到一個統計值，然後要算獲得這麼大（或這麼小）的統計值的機率有多少，這個或機率就是 p 值。我們得到 p 值以後，要作統計檢定。我們相約成俗地設定一個顯著水準，叫做 α，α 通常都是 0.05，有時候大家會嚴格一點用 0.01，比較不嚴格則用 0.10。如果我們的 α = 0.05，則若 p < 0.05，我們就可以拒絕虛無假設，並宣稱這個檢定在統計上是顯著的，否則檢定就不顯著，這是傳統的 p 值檢定方法。如果統計上顯著的話，我們就認為得到實驗結果的機會很小，所以就不接受虛無假設。

![](https://i.imgur.com/R9rzCZ6.png){width=600}
![](https://i.imgur.com/YNCQYZz.png)

# Statistical hypothesis test

A statistical hypothesis test is a method of statistical inference used to decide whether the data sufficiently supports a particular hypothesis. A statistical hypothesis test typically involves a calculation of a test statistic. Then a decision is made, either by comparing the test statistic to a critical value or equivalently by evaluating a p-value computed from the test statistic. Roughly 100 specialized statistical tests have been defined.

## Student's t-test

![](https://i.imgur.com/EJrAqbw.png){width=600}

Student's t-test is a statistical test used to test whether the difference between the response of two groups is statistically significant or not. It is any statistical hypothesis test in which the test statistic follows a **Student's t-distribution** under the null hypothesis. It is most commonly applied when the test statistic would **follow a normal distribution** if the value of a scaling term in the test statistic were known.

![](https://i.imgur.com/n8aDZgE.png){width=600}

雖然 t 檢定相對於假設的差異較為穩固，t 檢定實際上假設：

- 資料為**連續資料**。
- 樣本資料必須從母體隨機採樣。
- 變異數具同質性 (各群組資料的變異性相似)。
- **資料趨近於常態分佈**。
- 針對雙樣本 t 檢定 (Two sample t test)，我們必須有**獨立樣本**。若樣本並非獨立，則使用**配對t檢定 (Paired t test)**可能會較為合適。

- When the **normality assumption does not hold**, a non-parametric alternative to the t-test may have better statistical power.
  - **Nonparametric statistics** is a type of statistical analysis that makes minimal assumptions about the underlying distribution of the data being studied. Nonparametric tests are often used when the assumptions of parametric tests are evidently violated.
  - The first meaning of nonparametric involves techniques that **do not rely on data belonging to any particular** parametric family of probability distributions.
  - 非參數統計學（英語：nonparametric statistics），或稱無參數統計學、非參數統計分析，是統計學的一個分支，適用於**總體分佈情況未明、小樣本、總體分佈不為正態也不易轉換為正態**。
  - 參數統計（Parametric statistics）是統計學的一個分支，它假設樣本數據來自總體，而總體可以透過具有固定參數集的概率分佈 (e.g.正態分佈族) 進行充分建模。

## Wilcoxon signed-rank test

The Wilcoxon signed-rank test is a **non-parametric rank test** for statistical hypothesis testing used either to test the location of a population based on a sample of data, or to compare the locations of two populations using two matched samples.

- [单样本Wilcoxon符号秩检验(One Sample Wilcoxon Signed Rank Test)——理论介绍](https://mengte.online/archives/333)
- [单样本Wilcoxon符号秩检验(One Sample Wilcoxon Signed Rank Test)——Python软件实现](https://mengte.online/archives/12539)
- [配对样本Wilcoxon符号秩检验(Paired Samples Wilcoxon Signed Rank Test)——理论介绍](https://mengte.online/archives/340)
- [配对样本Wilcoxon符号秩检验(Paired Samples Wilcoxon Signed Rank Test)——Python软件实现](https://mengte.online/archives/12555)
- [兩組成對資料Wilcoxon signed-rank檢定 (Wilcoxon signed-rank test for paired samples，non-parametric)](https://statistics-using-python.blogspot.com/2019/08/wilcoxon-signed-rank-wilcoxon-signed.html)

# References

1. [T-Tests and P-Values](https://notebook.community/therealAJ/python-sandbox/data-science/learning/ud1/DataScience/TTest)
2. [使用 Python 进行 T检验](https://www.cnblogs.com/IvyWong/p/10134012.html)
   1. 单样本T检验(ttest_1samp)
   2. 两独立样本T检验(ttest_ind)
   3. 配对样本T检验(ttest_rel)
3. [【學習筆記】獨立樣本t檢定-理論+Python實作](https://medium.com/@jason8410271027/%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98-%E7%8D%A8%E7%AB%8B%E6%A8%A3%E6%9C%ACt%E6%AA%A2%E5%AE%9A-%E7%90%86%E8%AB%96-python%E5%AF%A6%E4%BD%9C-146699cf0bd9)
4. [看電影學統計: p值的陷阱](https://blog.udn.com/nilnimest/844041900)
5. [統計學:大家都喜歡問的系列-p值是什麼](https://chih-sheng-huang821.medium.com/%E7%B5%B1%E8%A8%88%E5%AD%B8-%E5%A4%A7%E5%AE%B6%E9%83%BD%E5%96%9C%E6%AD%A1%E5%95%8F%E7%9A%84%E7%B3%BB%E5%88%97-p%E5%80%BC%E6%98%AF%E4%BB%80%E9%BA%BC-2c03dbe8fddf)
6. [t 檢定](https://www.jmp.com/zh_tw/statistics-knowledge-portal/t-test.html)