---
layout: post
title: ML | Transformer | Attention, FFN, ResNet
categories: ML
tags: [NWP, ML, AI, RNN, Transformer, Attention, FFN, ResNet]
author: wpsze
date: 2025-01-30 21:20:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/m673yGz.png
banner_img: https://i.imgur.com/m673yGz.png
---

# "Attention is all you need" (2017)

- [Vaswani, A. (2017). **Attention is all you need**. Advances in Neural Information Processing Systems.](https://arxiv.org/abs/1706.03762)

{% gi 12 2-2-2-2-2-2 %}
![](https://i.imgur.com/MUotlGg.png)
![](https://i.imgur.com/swgaC77.png)
![](https://i.imgur.com/oqquyUc.png)
![](https://i.imgur.com/qA7n7K2.png)
![](https://i.imgur.com/MiCibQL.jpg)
![](https://i.imgur.com/Ddp94SL.png)
{% endgi %}

Encoder層和Decoder層內部結構如圖所示。

- Encoder具有兩層結構，self-attention和前饋神經網路。 self-attention計算句子中的每個詞都和其他詞的關聯，從而幫助模型更好地理解上下文語義，引入Muti-Head attention後，每個頭關注句子的不同位置，增強了Attention機制關注句子內部單詞之間作用的表達能力。**前饋神經網路為encoder引入非線性變換，增強了模型的擬合能力**。
- The encoder is composed of a stack of $N = 6$ identical layers. (**疊6次** !!!)
- Decoder接受output輸入的同時接受encoder的輸入，幫助目前節點取得到需要重點關注的內容
- **縮放因子**的作用是歸一化
- FFN的加入引入了非線性(ReLu激活函數)，變換了attention output的空間, 從而增加了模型的表現能力。把FFN去掉模型也是可以用的，但效果差很多了。
- 在每個block中，最後出現的是Layer Normalization，其作用是規範最佳化空間，加速收斂。
- 當我們使用梯度下降演算法做最佳化時，我們可能會對輸入資料進行歸一化，但是經過網路層作用後，我們的資料已經不是歸一化的了。隨著網路層數的增加，資料分佈不斷變化，偏差越來越大，導致我們不得不使用更小的學習率來穩定梯度。 Layer Normalization 的作用是確保資料特徵分佈的穩定性，將資料標準化到ReLU活化函數的作用區域，可以使得激活函數更好的發揮作用。**Normalization有兩種方法，Batch Normalization和Layer Normalization**。
- **Positional Encoding** : 位置資訊編碼位於encoder和decoder的embedding之後，每個block之前。它非常重要，沒有這部分模型就無法運作。 Positional Encoding是transformer的特有機制，彌補了Attention機制無法捕捉sequence中token位置資訊的缺點。Positional Embedding的成分直接疊加於Embedding之上，使得每個token的位置資訊和它的語義資訊(embedding)充分融合，並被傳遞到後續所有經過複雜變換的序列表達中去。
  - **提供位置信息**：位置編碼的主要功能是告訴模型每個輸入向量在序列中的具體位置。這對於理解語言中的語法結構和上下文至關重要，因為語言的意義往往依賴於詞語的順序。
  - **數學表達** : 位置編碼通常使用正弦和餘弦函數來生成，這樣可以保證不同位置之間的距離信息。具體公式如下：
    - 對於位置 $pos$ 和維度 $i$：
      $$
      \text{PE}(pos, 2i) = \sin\left(\frac{pos}{10000^{2i/d_{model}}}\right)
      $$
      $$
      \text{PE}(pos, 2i+1) = \cos\left(\frac{pos}{10000^{2i/d_{model}}}\right)
      $$
    其中，$d_{model}$ 是模型的維度（例如512），而 $i$ 是當前維度的索引。**這種設計使得每個位置都有一組唯一的向量表示，並且相鄰位置之間的關係可以通過這些向量的相似性來捕捉**!!!。
    - ![](https://i.imgur.com/A3FBmBf.png){width=400}
    - 句子中的位置獲得相應的位置編碼。例如，在句子 `貓坐在墊子上` 中，`貓`的位置編碼將與`坐`的不同，這樣模型就能夠理解它們之間的順序和關係。
- **Cross Attention**
  - 交叉注意力使得模型能夠在處理一個序列時，參考另一個序列的信息。這種機制特別適用於需要同時考慮多個模態或序列的任務，例如機器翻譯、圖像描述生成等。
  - **與自注意力的對比**
    - **自注意力（Self-Attention）**：主要關注同一序列內部元素之間的關係，允許每個元素關注到其他所有元素。這有助於捕捉長距離依賴關係。
      
    - **交叉注意力**：則專注於兩個不同序列之間的互動，允許模型在生成過程中參考其他序列的信息。
- Mask 機制
  - mask 表示掩碼，它對某些值進行掩蓋，使其在參數更新時不產生效果。 Transformer 模型裡面涉及兩種 mask，分別是 padding mask 和 sequence mask。其中，padding mask 在所有的 scaled dot-product attention 裡面都需要用到，而 sequence mask 只有在 decoder 的 self-attention 裡面用到。
  - **padding mask**: 因為每個批次輸入序列長度都是不一樣的也就是說，我們要對輸入序列進行對齊。具體來說，就是給在較短的序列後面填滿 0。但是如果輸入的序列太長，則是截取左邊的內容，把多餘的直接捨棄。因為這些填滿的位置，其實是沒什麼意義的，所以我們的attention機制不應該把注意力放在這些位置上，所以我們需要進行一些處理。具體的做法是，把這些位置的值加上一個非常大的負數(負無窮)，這樣的話，經過 softmax，這些位置的機率就會接近0。
  - **Sequence mask**: sequence mask 是為了讓 decoder 不能看見未來的資訊。也就是對於一個序列，在 time_step 為 t 的時刻，我們的解碼輸出應該只能依賴 t 時刻之前的輸出，而不能依賴 t 之後的輸出。因此我們需要想一個辦法，把 t 之後的訊息給隱藏起來。
- **Decoder Masked Attention**
  - 遮罩注意力機制的**主要目的是在訓練階段防止解碼器（Decoder）利用未來位置的資訊**，確保模型僅基於當前及之前的輸入生成輸出，模擬實際推理時逐步預測的過程。
  - 為遮蔽未來位置，在計算 softmax 前，將注意力分數矩陣的上三角部分（未來位置）設為 `-inf`，（Upper Triangular Matrix），迫使模型忽略未來資訊
  - $$
    \text{MaskedAttention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}} + M_{\text{mask}}\right)V
    $$
  - 假設序列長度為 3，遮罩矩陣為：
    $$
    M_{\text{mask}} = 
    \begin{bmatrix}
    0 & -\infty & -\infty \\
    0 & 0 & -\infty \\
    0 & 0 & 0
    \end{bmatrix}
    $$
    此矩陣確保位置 $i$ 僅能關注位置 $j \leq i$。
  - 解碼器的遮罩多頭注意力（Masked Multi-Head Attention）
  - 以翻譯任務為例，目標序列為 **"貓坐在墊子上"**：
    - **訓練階段**：  
      解碼器輸入為 `<BOS> 貓 坐 在 墊子`，遮罩確保預測 "坐" 時僅使用 `<BOS> 貓` 的資訊。
    - **推理階段**：  
      模型逐步生成輸出（如 `貓` → `坐` → `在`），每一步僅依賴已生成的序列。
  - 遮罩注意力機制是Transformer解碼器的核心設計之一，**透過數學上的遮罩操作與架構上的因果性約束**，實現高效且準確的序列生成，在機器翻譯、文本生成等任務中表現卓越。
- **Residual Network**
  - 殘差網絡是深度學習中重要概念。在神經網絡可以收斂的前提下，隨著網絡深度的增加，網絡表現先是逐漸增加至飽和，然後迅速下降，這就是我們經常討論的網絡退化問題。
- **Output: The Final Linear and Softmax Layer**
  - 線性變換層是一個簡單的全連接神經網絡，它可以把解碼組件產生的向量投射到一個比它大得多的、被稱作對數幾率（logits）的向量裡。接下來的Softmax 層便會把那些分數變成機率（都是正數、上限1.0）。機率最高的單元格被選中，並且它對應的單字被作為這個時間步的輸出。

{% note primary %}
- 這裡不得不提一點，雖然論文的名字叫《Attention is All your Need》，但是實際上， FFN and ResNet are also your need。
- 研究人員發現FFN 和ResNet 的Skip Connection 無論去掉哪一個，模型都會變得不可用。
- 所以說 Attention, FFN, ResNet 可以認為是 Transformers 架構的三駕馬車，缺一不可。
- 後續雖然有許多 Transformers 架構的魔改，但真正取得不錯的效果且落地的例子就是修改 FFN 的 MoE 架構。剩下的兩輛馬車，ResNet 幾乎沒辦法修改了，Attention 則在效率和效果之間尋求平衡。
{% endnote %}

{% note primary %}
- MoE : Transformer Feed-forward Layers are Mixtures of Experts 這是劉知遠團隊的論文，其實一直以來，神經網路就存在稀疏活化的現象，也就是在推理的時候，其實只有極小一部分參數參與了計算。這篇論文則透過MoE 的想法來將FFN 層拆分成了多個專家，並且新增了一個路由模組來確定推理的時候來掛哪個專家的門診：）這麼做之後，在提升推理速度的同時，效果仍能維持原來的95%以上。挺有價值的工作，大模型上也可以這麼做一把。
{% endnote %}

{% gi 12 2-2-2-2-2-2 %}
![](https://i.imgur.com/T5yqKkc.gif)
![](https://i.imgur.com/cpdMrRR.gif)
![](https://i.imgur.com/0jAGUu9.png)
![](https://i.imgur.com/iAPvqC1.png)
{% endgi %}

- After finishing the encoding phase, we begin the decoding phase. Each step in the decoding phase outputs an element from the output sequence (the English translation sentence in this case).
- The following steps repeat the process until a special symbol is reached indicating the transformer decoder has completed its output.
- The output of each step is fed to the bottom decoder in the next time step, and the decoders bubble up their decoding results just like the encoders did. 
- And just like we did with the encoder inputs, we embed and add positional encoding to those decoder inputs to indicate the position of each word.

## Linear Classifier and Final Softmax for Output Probabilitiesr
  - The decoder stack outputs a vector of floats. How do we turn that into a word? That’s the job of the final Linear layer which is followed by a Softmax Layer.
  - The Linear layer is a simple fully connected neural network that projects the vector produced by the stack of decoders, into a much, much larger vector called a logits vector.
  - Let’s assume that our model knows **10,000 unique English words (our model’s “output vocabulary”)** that it’s learned from its training dataset. This would make the **logits vector 10,000 cells wide** – each cell corresponding to the score of a unique word. That is how we interpret the output of the model followed by the Linear layer.
  - The softmax layer then turns those scores into probabilities (all positive, all add up to 1.0). The cell with the highest probability is chosen, and the word associated with it is produced as the output for this time step.

{% gi 12 2-2-2-2-2-2 %}
![](https://i.imgur.com/c2XOwSF.png)
![](https://i.imgur.com/7Q5Offp.png)
{% endgi %}

<iframe width="560" height="315" src="https://www.youtube.com/embed/zxQyTK8quyY" title="Transformer Neural Networks, ChatGPT&#39;s foundation, Clearly Explained!!!" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/bQ5BoolX9Ag" title="Decoder-Only Transformers, ChatGPTs specific Transformer, Clearly Explained!!!" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

# Input as vectors for a Transformer model

To prepare input as vectors for a Transformer model, the following steps are typically followed:

## 1. **Tokenization**
The first step involves breaking down the input text into smaller units called tokens. This can be done using various tokenization strategies, such as word-level, subword-level (like Byte Pair Encoding), or character-level tokenization. Each token corresponds to a unique identifier in the vocabulary.

## 2. **Embedding**
Once the text is tokenized, each token is converted into a dense vector representation through an embedding layer. This process maps tokens to continuous vector spaces, allowing the model to capture semantic relationships between words. The embedding can be learned during training or pre-trained embeddings (like Word2Vec or GloVe) can be used.

- **Learned Embeddings**: In the original Transformer paper, Vaswani et al. (2017) proposed using learned embeddings, meaning that the model learns to represent tokens as vectors during training.

## 3. **Positional Encoding**
Since Transformers do not inherently understand the order of tokens due to their parallel processing capability, positional encodings are added to the embeddings to provide information about the position of each token in the sequence. The positional encoding is typically a fixed function based on sine and cosine functions of different frequencies.

- The positional encoding is added to the input embeddings, resulting in a combined representation that retains both the content and positional information.

## Summary of Input Preparation Steps
1. **Tokenization**: Split text into tokens.
2. **Embedding**: Convert tokens into dense vectors.
3. **Positional Encoding**: Add positional information to embeddings.
4. **Feed into Encoder**: Use combined vectors as input for the Transformer encoder.

![](https://i.imgur.com/WQyQ5Pe.png){width=600}

# 自注意力機制 (Self-attention)

## Core Components

In self-attention, we define three matrices:

- **Query (Q)**: Represents the elements for which we want to compute attention scores.
- **Key (K)**: Represents the elements that will be compared against the queries.
- **Value (V)**: Represents the actual information we want to aggregate based on the attention scores.

To compute these matrices from an input embedding matrix $X$ (of shape $L \times D$, where $L$ is the number of words and $D$ is the embedding dimension), we use learned weight matrices $W_Q$, $W_K$, and $W_V$:

$$
Q = X W_Q
$$
$$
K = X W_K
$$
$$
V = X W_V
$$

## Attention Score Calculation
The self-attention mechanism computes attention scores using the following steps:

1. **Dot Product**: Calculate the dot product of the query and key matrices:
$$
\text{Attention Scores} = QK^T
$$
This results in a matrix where each entry indicates the similarity between queries and keys.

2. **Scaling**: To stabilize gradients during training, we scale the dot product by the square root of the dimension of the key vectors $d_k$:
$$
S = \frac{QK^T}{\sqrt{d_k}}
$$

3. **Softmax**: Apply the softmax function to convert these scores into probabilities:
$$
A = \text{softmax}(S)
$$
This ensures that the attention scores sum to 1 across each row, effectively normalizing them.

4. **Weighted Sum**: Finally, compute the output by taking a weighted sum of the value vectors based on the attention scores:
$$
O = AV
$$
Here, $O$ is the output matrix representing aggregated information from $V$ based on how much focus each query gives to each value.

### Complete Self-Attention Equation
Combining these steps, we can express self-attention as:
$$
O = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V
$$

## Multi-Head Attention
In practice, transformers often use multi-head attention, which allows the model to jointly attend to information from different representation subspaces. The output for each head is computed independently and then concatenated:
$$
O = W_o [H_1; H_2; ...; H_M]
$$
where each head $H_i$ is calculated as:
$$
H_i = \text{softmax}\left(\frac{W_k^{(i)}Q(W_q^{(i)})^T}{\sqrt{d_k}}\right)W_v^{(i)}V
$$

Here, $W_q^{(i)}, W_k^{(i)}, W_v^{(i)}$ are learned weight matrices for each head, and $W_o$ is a final linear transformation applied after concatenating all heads.

{% gi 12 3-3-3-3 %}
![](https://i.imgur.com/m673yGz.png)
![](https://i.imgur.com/1xy5F9F.png)
![](https://i.imgur.com/d6LS6YQ.png)
![](https://i.imgur.com/L8t2LFA.png)
![](https://i.imgur.com/JL92Jfv.png)
![](https://i.imgur.com/uNcguro.png)
![](https://i.imgur.com/9oxhFT8.png)
![](https://i.imgur.com/AzOhXvb.png)
![](https://i.imgur.com/36lKeEY.png)
![](https://i.imgur.com/RxJGBEk.png)
![](https://i.imgur.com/vu4JQUO.png)
{% endgi %}

<iframe width="560" height="315" src="https://www.youtube.com/embed/hYdO9CscNes" title="【機器學習2021】自注意力機制 (Self-attention) (上)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/gmsMY5kc-zw" title="【機器學習2021】自注意力機制 (Self-attention) (下)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

# Cross-Attention

{% note primary %}
在 cross attention 的基礎上, 提出了 self-attention
{% endnote %}

{% gi 12 3-3-3-3 %}
![](https://i.imgur.com/kUDX4jY.png)
![](https://i.imgur.com/LfWgMKb.png)
{% endgi %}

# References

1. [为什么我还是无法理解transformer？ - 看图学的回答 - 知乎 ( **推薦 ** )](https://www.zhihu.com/question/596771388/answer/3459193587)
2. [The Illustrated Transformer ( **推薦 ** )](https://jalammar.github.io/illustrated-transformer/)
3. [Transformers Laid Out | Pramod's Blog ( **推薦 ** )](https://goyalpramod.github.io/blogs/Transformers_laid_out/)
4. [transformer的细节到底是怎么样的？ - 亚东的回答 - 知乎](https://www.zhihu.com/question/362131975/answer/3360076979)
5. [transformer的细节到底是怎么样的？ - Hao Bai的回答 - 知乎](https://www.zhihu.com/question/362131975/answer/3039107481)
6. [Transformer - Attention is all you need](https://zhuanlan.zhihu.com/p/311156298)
7. [论文解读:Attention is All you need](https://zhuanlan.zhihu.com/p/46990010)
8. [Visualizing and Explaining Transformer Models From the Ground Up](https://deepgram.com/learn/visualizing-and-explaining-transformer-models-from-the-ground-up)
9. [【機器學習2021】Transformer (上)](https://www.youtube.com/watch?v=n9TlOhRjYoc&ab_channel=Hung-yiLee)
10. [【機器學習2021】Transformer (下)](https://www.youtube.com/watch?v=N6aRv06iv2g&ab_channel=Hung-yiLee)