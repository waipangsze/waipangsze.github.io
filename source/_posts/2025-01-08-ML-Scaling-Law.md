---
layout: post
title: ML | Scaling Law
categories: [ML]
tags: [ML, AI, pytorch, LLM]
author: wpsze
date: 2025-01-08 09:28:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://miro.medium.com/v2/resize:fit:828/format:webp/1*7bmT0RvnsfR1vvQ2ZBh8WQ.png
banner_img: https://miro.medium.com/v2/resize:fit:828/format:webp/1*7bmT0RvnsfR1vvQ2ZBh8WQ.png
---

# Scaling Law

In machine learning, a neural scaling law is an empirical scaling law that describes how neural network performance changes as key factors are scaled up or down. These factors typically include the **number of parameters**, **training dataset size**, and **training cost**.

- Scaling Law 定義：我們可以用模型大小、Dataset大小、總計算量，來預測模型最終能力。（通常以相對簡單的函數型態, ex: Linear relationship）
- 如果我們接受原本Scaling Law的定義（模型性能可藉由參數量、Dataset大小、計算量預測），馬上就會衍伸出兩個很重要的問題。
  - Return（收益）：在固定的訓練計算量之下，我們所能得到的最好性能是多好？
  - Allocation（分配）：我們要怎麼分配我們的模型參數量跟Dataset大小。（假設計算量 = 參數量 * Dataset size，我們要大模型 * 少量data、中模型 * 中量data、還是小模型 * 大量data）
- 2022年DeepMind提出Chinchilla Scaling Law，同時解決了這兩個問題，並且依此改善了當時其他大模型的訓練方式。
  - 他們基於三種方式來找到訓練LLM的Scaling Law：
    - 固定模型大小，變化訓練Data數量。
    - 固定計算量（浮點運算），變化模型大小。
    - 對所有實驗結果，直接擬合參數化損失函數。
- **這就像是開頭OpenAI的圖，找到了一個預估訓練LLM回報的公式，依此，我們不用拿超大的模型跟數據，用幾個月甚至幾年的時間慢慢做實驗，只要在小模型、小數據下驗證、確認我們的公式，就可以設計一個更大型的訓練，大幅度減少了實驗成本**。

## 因應Scaling Law，我們要如何改變我們的工作模式與思考方式？

- 要能 Scaling，意味著組織必須能夠支援持續擴大（Scale）模型的infra跟GPU資源分配模式。
  - 首先，我們來說 infrastructure，以7B模型而言，粗估要佔用15GB~20GB的GPU memory，也就是說如果你全參數訓練，一張A100（80GB GPU memory）只能開 Batch = 4，如果你想要 Batch = 64, 128，那麼必須先進行跨卡和跨機的訓練環境設置。
  - 同時為了加速訓練，讓memory使用率變的更高，每一兩個月幾乎就有新的技術、python package出來，Ex: Flash Attention 2, Zero 3, Vllm, Gradient Checkpoint, CPU offloading …等，隨時追蹤最新的訓練優化技術，並且保持組織的Server要能夠高頻率做改變，如果一個組織對Server裡面pip版本更新、各種packages更新的週期都是以月為單位，注定會在infra層面落後全世界半年到一年。

## GPU的資源分配模式, 通過Scaling檢查

GPU的資源分配模式，傳統實驗室、公司的GPU資源模式不外乎兩種：

- 平均分攤GPU給每個人
- 因應Project去申請更大型的GPU cluster

{% note primary %}
更好的方法應該是盡量讓每個Project都具備在 3B 以下模型進行概念驗證的能力，並**設計正式的Scaling檢查機制**，只有**通過Scaling檢查的團隊**可以 "By experiments" 去使用更大的GPU Cluster。這邊提到Scaling檢查機制，核心就是，當我們想要跑7B甚至13B模型時，我們最好在3B以下的模型都有一致的Scaling Behavior。最好在BERT跟T5這兩種大小先進行過概念驗證。
{% endnote %}

## Scaling，其中最少都會包含三個階段

- Cold start：一開始模型太小、Data不夠、問題太難時，會呈現怎麼訓練都沒有幫助，好幾個不同大小的模型、Dataset訓練出來的結果都差不多爛，看不出Scaling的時期。
- Scaling：正常的Scaling時期。
- Plateau：撞到了某個隱形的天花板，可能是Dataset品質帶來的天花板、本身任務的Irreducible Error、Architecture的能力天花板。

![](https://miro.medium.com/v2/resize:fit:828/format:webp/1*7bmT0RvnsfR1vvQ2ZBh8WQ.png){width=600}

## 一個好的idea，一定要具備Scale的潛能



# References

- [LLM 10大觀念-1 Scaling Law](https://axk51013.medium.com/llm%E5%B0%88%E6%AC%84-%E8%BF%8E%E6%8E%A52024%E5%B9%B4-10%E5%80%8B%E5%BF%85%E9%A0%88%E8%A6%81%E6%90%9E%E6%87%82%E7%9A%84llm%E6%A6%82%E5%BF%B5-1-scaling-law-5f6a409d35c5)
- [Neural_scaling_law | wiki](https://en.wikipedia.org/wiki/Neural_scaling_law)
- [Kaplan, Jared, et al. "Scaling laws for neural language models." arXiv preprint arXiv:2001.08361 (2020). | OpenAI](https://arxiv.org/abs/2001.08361)

