---
layout: post
title: ML | GPU Memory for ML model
categories: [ML]
tags: [ML, AI, pytorch, NVIDIA]
author: wpsze
date: 2025-01-07 08:54:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/17nibPC.png
banner_img: https://i.imgur.com/17nibPC.png
---

# GPU Memory you need to serve any ML models

**System Memory (RAM)** is not ideal for this purpose because it is slower than GPU memory. **GPU memory**, also known as **VRAM (Video Memory)** or **GDDR (Graphics DDR)**, is specifically designed for high-performance computing tasks like deep learning. It provides the speed and bandwidth needed to efficiently run large language models. 

{% note primary %}
So, the more VRAM the GPU has, the bigger Model/LLM it can host and serve
{% endnote %}

- P (parameters) : The number of parameters in the model. For example, GPT-3 has 175 Billion parameters, Llama-70b has 70 Billion Parameters etc.
- Q (Precision or Size per parameter) : Is the data type used to store the model parameters. Common data types include:
  - FP32 (32-bit floating point): 4 bytes per parameter
  - FP16 (half/BF16) (16-bit floating point): 2 bytes per parameter
  - INT8 (8-bit integer): 1 byte per parameter
  - INT4 (4-bit integer): 0.5 bytes per parameter

Overhead factor : This accounts for additional memory used during inference, such as storing activations (intermediate results) of the model. A typical overhead factor is 20%.

以 Llama-2-7b-hf 為例

- 因為全精度模型參數是 float32 類型, 佔用 4 個位元組，粗略計算：1b(10億)個模型參數，約佔用4G顯存(實際大小：10^9 * 4 / 1024^3 ~= 3.725 GB) ，則LLaMA 的參數量為7b，那麼載入模型參數所需的顯存為：3.725 * 7 ~= 26.075 GB
- 如果您的顯存不足 32GB，那麼可以設定半精度的 FP16/BF16 來加載，每個參數只佔 2 個字節，所需顯存就直接減半，只需要約 13GB。雖然模型效果會因精度損失而略微降低，但一般在可接受範圍。
- 如果您的顯存不足 16GB，那麼可以採用 int8 量化後，顯存再減半，只需要約 6.5GB，但是模型效果會更差一些。
- 如果您的顯存少於 8GB，那麼只能採用 int4 量化，顯示再減半，只需要約 3.26GB。


我們來說 infrastructure，以7B模型而言，粗估要佔用 15GB~20GB 的GPU memory，也就是說如果你全參數訓練，一張A100（80GB GPU memory）只能開 Batch = 4，如果你想要 Batch = 64, 128，那麼必須先進行跨卡和跨機的訓練環境設置。

## torch.dtype

- <https://pytorch.org/docs/stable/tensor_attributes.html>

```python
# add `torch_dtype=torch.float16` to use half the memory
```


# References

- [Estimating GPU Memory Consumption of Deep Learning Models | microsoft | 2020 (**Recommended**) ](https://www.microsoft.com/en-us/research/uploads/prod/2020/09/dnnmem.pdf)
- [Calculate : How much GPU Memory you need to serve any LLM ?](https://ksingh7.medium.com/calculate-how-much-gpu-memory-you-need-to-serve-any-llm-67301a844f21)
- [GPU memory requirements for serving Large Language Models](https://unfoldai.com/gpu-memory-requirements-for-llms/)
- [2020年搞深度学习需要什么样的GPU：请上48G显存](https://www.jiqizhixin.com/articles/2020-02-19-9)
- [挑战性能极限小显卡大作为，教你如何在有限资源下运行大型深度学习模型，GPU显存估算并高效利用全攻略！](https://cloud.tencent.com/developer/article/2403424)