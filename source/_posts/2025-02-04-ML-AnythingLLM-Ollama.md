---
layout: post
title: ML | AnythingLLM + Ollama
categories: ML
tags: [ML, AI, Ollama, AnythingLLM]
author: wpsze
date: 2025-02-04 23:30:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/mhEGKtT.png
banner_img: https://i.imgur.com/mhEGKtT.png
---

# What is AnythingLLM?

- <https://docs.useanything.com/introduction>

**AnythingLLM** is the easiest to use, all-in-one AI application that can do RAG, AI Agents, and much more with no code or infrastructure headaches.

AnythingLLM is built by *Mintplex Labs*, Inc - founded by Timothy Carambat and went through *YCombinator* Summer 2022.

{% note primary %}
- You want a **zero-setup, private, and all-in-one AI application for local LLMs, RAG, and AI Agents all in one place without painful developer-required set up**.
- 用Ollama & AnythingLLM建構AI知識庫
{% endnote %}

- 多用戶支持：無論是團隊合作還是個人使用，AnythingLLM 都能夠輕鬆管理多用戶的權限和操作。
- 靈活的 LLM 和向量資料庫選擇：用戶可以根據需求選擇合適的 LLM 和向量資料庫，滿足不同的應用場景。
- 高效的文件管理：支持多種文件類型，如 PDF、TXT、DOCX 等，並通過簡單的 UI 進行管理。（目前不支援DOC格式）
- 自定義聊天小工具：可將聊天功能嵌入網站，提升互動體驗。
- 兩種聊天模式：在保留上下文的會話模式(平常使用，沒資料就直接根據LLM的知識回應)和問答的查詢模式(較嚴格，查詢到資料才回答，更減低幻覺發生)之間靈活切換。

{% gi 6 1-3-2 %}
![](https://i.imgur.com/mhEGKtT.png)
![](https://i.imgur.com/YIN7Uls.png)
![](https://i.imgur.com/CdkwoT9.png)
![](https://i.imgur.com/W0WcgyK.png)
{% endgi %}

## RAG

RAG (Retrieval-Augmented Generation) 可以想像成是一個小抄或字典，透過向量資料庫檢索出來的資料跟使用者的問題一併告訴語言模型，讓語言模型能夠減低幻覺，根據資料回答問題。

## Ollama

- 7B模型至少要8G內存，13B的要16G，想玩70B的大傢伙，那得有64G

# Installation

## MacOS

```sh
brew install --cask anythingllm
```

Once installed, you will find AnythingLLM in your Applications folder as well as you can use `cmd + spacebar` and type in `AnythingLLM` to run.

### ollama setup

- URL: http://127.0.0.1:11434 
  - No slash / at the end of URL
- updated workspace

{% gi 6 3-3 %}
![](https://i.imgur.com/06uxnHS.png)
![](https://i.imgur.com/6F8l0Ml.png)
![](https://i.imgur.com/ScdOVxc.png)
{% endgi %}

### upload PDF

{% gi 6 3-3 %}
![](https://i.imgur.com/Bdx3d7w.png)
![](https://i.imgur.com/RWEbIjz.png)
![](https://i.imgur.com/6VWZ55T.png)
![](https://i.imgur.com/6lib4wr.png)
![](https://i.imgur.com/PuYMkhw.png)
![](https://i.imgur.com/QgsPU6h.png)
{% endgi %}

# Comments

- [Seven Failure Points When Engineering a Retrieval Augmented Generation System](https://arxiv.org/html/2401.05856v1)
- [LLM RAG值得做吗？ - 小五哥的回答 - 知乎](https://www.zhihu.com/question/2818756749/answer/20825040322)
  - 1、不值得。RAG是增强检索，本质上是语义近似检索，LLM只是对检索条目的总结，重点是在检索，而不是在大模型。
  - 2、RAG对计算资源需求不高，向量库和向量模型对GPU的需求很低，甚至CPU也能跑，大模型用Ollama装量化模型，也不需要GPU，当然用CPU性能差些。
  - 3、我个人认为，RAG中是个算力不足、无法实践微调的临时过渡方案，没有前景。
  - 4、RAG的核心是文本分段策略，是数据层面的，非技术层面的。论文方面，两段检索、二次排名这些能出点成果。
- [LLM RAG值得做吗？ - 程序锅的回答 - 知乎](https://www.zhihu.com/question/2818756749/answer/52312494601)
  - 如果大模型你不会做RAG，那你还能做什么？大模型训练门槛极高、大模型微调效果不佳，老板让你做一个LLM应用，输出结果全靠提示工程？
  - 我的观点是大模型最近几年真正落地，都需要RAG来缓解幻觉问题。所以用好RAG以及掌握它的原理是很重要的
- [AnythingLLM 个人知识库局限性 - TroyLiu的文章 - 知乎](https://zhuanlan.zhihu.com/p/939571291)


# References

1. [AnythingLLM](https://anythingllm.com/)
2. [AnythingLLM + Ollama輕鬆架設多人用的客製化 RAG](https://medium.com/@pang2258/anythingllm-ollama%E8%BC%95%E9%AC%86%E6%9E%B6%E8%A8%AD%E5%A4%9A%E4%BA%BA%E7%94%A8%E7%9A%84%E5%AE%A2%E8%A3%BD%E5%8C%96-rag-2d05954bf771)
3. [使用ollama + AnythingLLM快速且简单的在本地部署llama3](https://www.cnblogs.com/jokingremarks/p/18151827)