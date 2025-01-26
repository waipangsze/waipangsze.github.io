---
layout: post
title: ML | VAE Variational Auto-Encoder
categories: ML
tags: [NWP, ML, AI, VAE, Auto-Encoder, Generative AI]
author: wpsze
date: 2025-01-26 00:07:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/JzSVy2C.png
banner_img: https://i.imgur.com/JzSVy2C.png
---

# 基礎知識回顧

- Latent Variable
- Variations
- Gaussian Mixture Model
  - **Gaussian Mixture Model**，即高斯混合模型。生成模型比較主流的三個模型為：隱馬可夫模型HMM、樸素貝葉斯模型NB、高斯混合模型GMM。這裡我們主要為大家介紹GMM。
    - 混合模型是一個可以用來表示在總體分佈中含有N個子分佈的機率模型，它表示了觀測資料在總體中的機率分佈。利用混合模型計算總體分佈機率時我們並不需要知道原始觀測資料中子分佈的資訊。由 Fourier Theory 可得，任一隨時間做週期性變化的波，都可以分解為一系列不同頻率、不同振幅、不同相位的正弦波。同樣地，我們也可以用多個常態分佈的疊加去逼近任一個分佈。
- Conditional Probability
  - **Conditional Probability**：條件機率。定義兩個事件A和時間B，求A和B同時發生的機率：
  - $P(A, B) = P(B) P(A|B) = P(A) P(B|A)$
- KL divergence
  - KL divergence：KL散度又稱為KL距離或相對熵，用於衡量兩個機率分佈之間的距離。給定真實分佈 $P(x)$ 和理論分佈 $Q(x)$，我們將它們之間的KL散度公式定義為：
  - $KL (P||Q) = \sum P(x) \log (\dfrac{P(x)}{Q(x)}) = \int P(x) \log (\dfrac{P(x)}{Q(x)}) dx$
  - 此外，關於 KL 散度的一些性質如下：
    - **KL散度是不對稱的**：因為P到Q的距離不等於Q到P的距離，即 $KL(P||Q)≠KL(Q||P)$ 。這很容易造成 model collapse 即模式坍縮－模型傾向於產生一些比較容易騙過判別器的樣本，加快模型的收斂，從而導致生成的多樣性變差，生成出來的效果也比較差，相當於走捷徑。
    - 當且僅當兩個分佈完全一致時，KL散度等於0。
- Maximum Likelihood Estimate
  - Maximum Likelihood Estimate，MLE：極大似然估計。要理解什麼是極大似然估計，我們要先理解什麼是“似然”，它同一般的機率事件又有啥區別？給定一個函數 $P(x|\theta)$， $x$ 代表樣本點， $\theta$ 表示參數：
    - 當 $\theta$ 為常數， $x$ 為變數時，我們稱 為關於 的**機率函數**；
    - 當 $x$ 為常數， $\theta$ 為變數時，我們稱 為關於 的**似然函數**
  - 極大似然估計中樣本點的取樣都必須滿足 i.i.d.，它尋找的是使得樣本點 $x$ 能夠以最大機率發生的 $\theta$ 的取值

# Applications

Auto-Encoders are a specialized type of neural network primarily used for **unsupervised learning tasks**. They function by compressing input data into a lower-dimensional representation and then reconstructing the original data from this compressed form. Below are some key applications and functionalities of autoencoders.

自動編碼器（Autoencoder）在多種情境下表現出色，特別是在無監督學習和數據處理方面。以下是一些自動編碼器最有效的應用場景：

## 自動編碼器的有效情境

1. **特徵降維**：
   - 自動編碼器能夠將高維數據壓縮到低維空間，提取出數據中的關鍵特徵，這一過程類似於主成分分析（PCA），但通常能捕捉更複雜的非線性結構.

2. **數據去噪**：
   - 去噪自動編碼器（Denoising Autoencoder）專門設計用來從受損或帶噪聲的數據中恢復原始信號，這在圖像處理和音頻信號處理中非常有效.

3. **異常檢測**：
   - 自動編碼器可以用於識別異常數據點。通過訓練模型重建正常數據，當輸入異常數據時，重建誤差會顯著增加，從而標識出異常.

4. **生成模型**：
   - 變分自動編碼器（Variational Autoencoder, VAE）和對抗自動編碼器（Adversarial Autoencoder, AAE）等擴展版本可用於生成新樣本，如圖像或時間序列數據，這在創意應用中非常有用.

5. **特徵提取**：
   - 自動編碼器可以作為特徵提取器，將學習到的隱含表示用於後續的有監督學習任務，提升模型性能.

6. **圖像重建與修復**：
   - 在計算機視覺領域，自動編碼器可以用於圖像重建和修復，例如從模糊或部分損壞的圖像中重建清晰的圖像.

Input = image, text

{% gi 4 3-3 %}
![](https://i.imgur.com/0EJzfPK.png)
![](https://i.imgur.com/i8javUE.png)
{% endgi %}

# Auto-Encoder

Auto-Encoder自編碼器是1986年由 Rumelhart 提出，可用於高維複雜資料的處理, 它促進了神經網路的發展。自編碼神經網路是一種**無監督學習演算法**（訓練範例未標註），它使用了BP反向傳播演算法，致力於使輸出與輸入越接近越好。

## Structure of Auto-Encoder

An Auto-Encoder typically consists of three main components:

- **Encoder**: Compresses the input data into a lower-dimensional representation.
- **Bottleneck (Code)**: Contains the compressed representation of the input.
- **Decoder**: Reconstructs the original input from the compressed representation.  
- 
{% gi 4 2-2 %}
![](https://i.imgur.com/shu8T3B.png)
![](https://i.imgur.com/Qx6HPqW.png)
![](https://i.imgur.com/OmfCUi2.png)
![](https://i.imgur.com/yewPTO0.png)
{% endgi %}

## 自編碼器的限制在哪裡？

透過AE建構出一個比PCA更清晰的自編碼器模型，**但這並不是真正意義上的生成模型**。對於一個特定的生成模型，它一般應該滿足以下兩點：

- 編碼器和解碼器是可以獨立分割的（類比GAN的Generator和Discriminator）
- 固定維度下任意取樣出來的編碼，都應該能透過解碼器產生一張清晰且真實的圖片
  
這裡解釋下第二點。如下圖所示，我們用一張全月圖和一張半月圖去訓練一個AE，經過訓練，模型能夠很好地還原出這兩張圖片。接下來，我們在latent code上中間一點，即兩張圖片編碼點中間處任取一點，將這點交給解碼器進行解碼，直覺上我們會得到一張介於全月圖和半月圖之間的圖片（如陰影面積覆蓋3/4的樣子）。然而，實際上當你那這個點去decode的時候你會發現AE還原出來的圖片不僅模糊而且還是亂碼的。為什麼會出現這種現象？一個直觀上的解釋是AE的Encoder和Decoder都使用了 DNN，**DNN是一個非線性的變換過程，因此在latent space上點與點之間transform往往沒有規律可循**。

如何解決這個問題呢？**一個想法就是引入噪聲**，擴大圖片的編碼區域，從而能夠覆蓋到失真的空白編碼區。其實說白了就是透過增加輸入的多樣性來增強輸出的魯棒性。當我們將輸入圖片編碼之前引入一點噪聲，使得每張圖片的編碼點出現在綠色箭頭範圍內，這樣一來所得到的latent space就能覆蓋到更多的編碼點。此時我們再從中間點抽取去還原便可以得到一個我們比較希望得到的輸出

雖然我們為輸入圖片增添了一些噪音使得latent space能夠覆蓋到比較多的區域，但是還是有不少地方沒有被覆蓋到，比如上圖右邊黃色的部分因為離得比較遠所以就沒編碼到。因此，我們是否可以嘗試利用更多的噪音，使得對於每一個輸入樣本，它的編碼都能夠覆蓋到整個編碼空間？只不過這裡我們需要保證的是，對於源編碼附近的編碼我們應該給定一個高的機率值，而對於距離原編碼點距離較遠的，我們應該給定一個低的機率值。沒錯，整體來說，**我們就是要將原先一個單點拉伸到整個編碼空間，即將離散的編碼點引申為一條連續的接近常態分佈的編碼曲線**。

{% gi 3 3 %}
![](https://i.imgur.com/WcjuQGm.png)
![](https://i.imgur.com/Ahr59Ji.png)
![](https://i.imgur.com/9bIChjc.png)
{% endgi %}

到這裡，我們已經不知不覺到來到了 **變分自編碼器 VAE 的核心思想腹地**。以下我們將詳細敘述VAE的模型架構。

# Variational Auto-Encoder

早在2013年，Kingma和Welling就推出了變分自動編碼器（VAE），簡而言之，VAE的想法是訓練具有正則化潛在空間的自動編碼器。然後，正則化編碼器被迫將資料編碼為接近高斯的分佈，而解碼器則從潛在空間重建資料。

變分法便是用來求泛函數的極值。下面就不展開了，有興趣的可以自行查閱相關資料。這裡主要說一點的就是 VAE 中 V 是怎麼來的，認為應該只是計算的過程中用到了變分法的思想去求解，所以就取名叫 VAE。

- [Kingma, Diederik P. "Auto-encoding variational bayes." arXiv preprint arXiv:1312.6114 (2013).](https://arxiv.org/abs/1312.6114)
- [An Introduction to Variational Autoencoders (2019)](https://arxiv.org/abs/1906.02691)

{% gi 9 3-3-3 %}
![](https://i.imgur.com/y99QAWW.png)
![](https://i.imgur.com/8KvMH5H.png)
![](https://i.imgur.com/rO6O6rt.png)
![](https://i.imgur.com/YGNSOKR.png)
![](https://i.imgur.com/7IAIlyS.png)
![](https://i.imgur.com/mavnxry.png)
![](https://i.imgur.com/v9pXyAv.png)
![](https://i.imgur.com/0BY1Ikf.png)
![](https://i.imgur.com/WY36In6.png)
{% endgi %}

最後，再讓我們一起來討論下VAE的限制。雖然VAE比普通的AE模型訓練出來的效果要好得多，但是訓練過VAE模型的人都知道，它生成出來的圖片相對GANs那種直接利用對抗學習的方式會比較模糊，這是由於它是通過直接計算生成圖片和原始圖片之間的均方誤差，所以得到的是一張「平均圖像」。

# Sampling

結構：VAE也是由編碼器和解碼器組成，**但它在編碼階段引入了一個機率模型**。編碼器輸出的是潛在空間的參數（平均值和變異數），而不是直接的潛在表示。解碼器則從這個潛在分佈中取樣產生資料。

目的：VAE的目標不僅是重構輸入數據，**還希望潛在空間的表示具有良好的性質，例如連續性和可解釋性。這使得 VAE 適合產生新數據**。

應用：VAE廣泛應用於生成建模，如影像生成、資料插值和缺失資料填充

![](https://i.imgur.com/JzSVy2C.png){width=800}

## 編碼器與機率分佈

編碼器與機率分佈：在 VAE 中，編碼器不是直接輸出潛在變數 $z$ 的值，而是輸出 $z$ 的參數。這通常是潛在空間的均值 $\mu$ 和 方差 $\sigma$。透過這些參數，我們可以定義一個常態分佈：

$$
z \sim N(\mu(x), \sigma(x))
$$

{% note primary %}
這裡，$\mu(x)$ 和 $\sigma(x)$ 是由編碼器網路產生的。這樣，$z$ 的值就是在**這個分佈中進行取樣**的。
{% endnote %}

## 重參數化技巧 (Reparameterization trick)

重參數化技巧：**為了使得 VAE 能夠進行有效的反向傳播**，使用了重參數化技巧。具體來說，我們將 $z$ 表達為：

$$
z = \mu(x) + \sigma(x) \cdot \epsilon
$$

其中 $\epsilon \sim N(0,1)$ 。

{% note primary %}
透過這種方式，**我們可以將隨機性從網路的前向傳播中分離出來，使得我們能夠透過標準的反向傳播演算法來訓練模型**。
{% endnote %}

## VAE生成的影像品質並不好

目前在影像生成領域，除了VAE，還有GAN，Diffusion模型等，相較之下，VAE生成的影像品質並不好，通常比較模糊。但為什麼在StableDiffusion中，採用了VAE+diffusion的結構，效果就變得非常好了？

過高的壓縮率、重建損失只有平方誤差、完整的KL散度項，都是傳統VAE模糊的原因。而對於傳統VAE來說，能改進的只有重構損失，其實如果你把傳統VAE的重構損失也換成“平方誤差 + perceptual損失 + 對抗損失”，也能明顯減少VAE的模糊。

但是，改進VAE的重構損失只能解決清晰度問題，解決不了壓縮率問題，將一張 256x256 甚至 1024x1024 的圖片壓縮為單一向量，必然會有嚴重的資訊損失，只能重構出一些比較宏觀的語義，細節層面肯定沒辦法更好保留。

相較之下，LDM的VAE只用來壓縮圖片大小，它不負責成為一個生成模型（交給diffusion來做），所以能騰出的空間更多。例如，將壓縮為單一向量改為保留a*b大小的feature map，單這一點就可以讓重構結果清晰不少，然後弱化KL散度項（或者VQ-VAE的VQ作為正則，都差不多，這是為了防止latent的變異數過大）也能讓結果更清晰，更不用說改進重構損失了。

說穿了，LDM的VAE只負責重構出清晰的結果（外加一點正則），它的核心作用的AE而不是VAE，生成能力強是因為Diffusion強。

所以 (latent diffusion model) LDM的VAE可以當做AE來理解，本質上是因為圖片和視訊資訊實在太稀疏了，搞個AE壓縮一下，方便後面diffusion訓練。 KL散度就是懲罰項，讓latent的分佈集中一下，別太分散了，方便後面學習，不考慮 z 一定要用常態分佈採樣。

{% fold info @pytorch VAE %}
```python
import torch
import torchvision
import torch.nn as nn
import torch.optim as optim
from torchvision import datasets, transforms
from torch.utils.data import DataLoader
import matplotlib.pyplot as plt

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
device = "cpu"
print("device = ", device)
print("torch.__version__ = ", torch.__version__)
print("torch.cuda.is_available() = ", torch.cuda.is_available())
print("torch.cuda.device_count() = ", torch.cuda.device_count())
print("torch.cuda.current_device() =", torch.cuda.current_device())
print("torch.cuda.device(0) = ", torch.cuda.device(0))
print("torch.cuda.get_device_name(0) = ", torch.cuda.get_device_name(0))

# 超参数
latent_dim = 20
batch_size = 128
learning_rate = 1e-3
num_epochs = 10

# 数据准备
transform = transforms.Compose([
    transforms.ToTensor(),
    # transforms.Normalize((0.5,), (0.5,))
])
train_dataset = datasets.MNIST(root='./data', train=True, download=True, transform=transform)
train_loader = DataLoader(dataset=train_dataset, batch_size=batch_size, shuffle=True)

# VAE模型定义
class VAE(nn.Module):
    def __init__(self, latent_dim):
        super(VAE, self).__init__()
        # 编码器
        self.encoder = nn.Sequential(
            nn.Flatten(),
            nn.Linear(28*28, 400),
            nn.ReLU(),
            nn.Linear(400, 2 * latent_dim)  # 输出均值和对数方差
        )
        # 解码器
        self.decoder = nn.Sequential(
            nn.Linear(latent_dim, 400),
            nn.ReLU(),
            nn.Linear(400, 28*28),
            nn.Sigmoid()  # 输出范围在[0, 1]之间
        )

    def reparameterize(self, mu, logvar):
        std = torch.exp(0.5 * logvar)
        eps = torch.randn_like(std)
        return mu + eps * std

    def forward(self, x):
        # 编码
        mu_logvar = self.encoder(x)
        mu, logvar = mu_logvar[:, :latent_dim], mu_logvar[:, latent_dim:]
        z = self.reparameterize(mu, logvar)
        # 解码
        return self.decoder(z), mu, logvar

# 损失函数
def loss_function(recon_x, x, mu, logvar):
    BCE = nn.functional.binary_cross_entropy(recon_x, x.view(-1, 28*28), reduction='sum')
    KLD = -0.5 * torch.sum(1 + logvar - mu.pow(2) - logvar.exp())
    return BCE + KLD

# 初始化模型和优化器
model = VAE(latent_dim)
model.to(device)

optimizer = optim.Adam(model.parameters(), lr=learning_rate)

# 训练模型
model.train()
for epoch in range(num_epochs):
    train_loss = 0
    for batch_idx, (data, _) in enumerate(train_loader):
        optimizer.zero_grad()
        data = data.to(device)
        recon_batch, mu, logvar = model(data)
        loss = loss_function(recon_batch, data, mu, logvar)
        loss.backward()
        train_loss += loss.item()
        optimizer.step()
    print(f'Epoch {epoch + 1}, Loss: {train_loss / len(train_loader.dataset):.4f}')

print("Training finished!")

# 生成图像
model.eval()
with torch.no_grad():
    sample = torch.randn(64, latent_dim)  # 生成64个潜在样本
    sample = sample.to(device)
    generated_images = model.decoder(sample).view(-1, 1, 28, 28)  # 解码为图像

# 可视化生成的图像
grid_img = torchvision.utils.make_grid(generated_images, nrow=8, normalize=True)
grid_img = grid_img.cpu()
plt.figure(figsize=(10, 10))
plt.imshow(grid_img.permute(1, 2, 0).numpy())
plt.axis('off')
#plt.show()
plt.savefig('test.png')
```
{% endfold %}

- real time是時脈走過的時間，user time 是程式在使用者狀態的cpu時間，sys time 為程式在內核態的cpu時間
- `%cpu_usage = (user_time + sys_time)/real_time * 100%`

GPU-3090 (only ~7% utility)
```console
real	1m16.440s
user	1m23.738s
sys	0m32.552s
```

CPU (all 20cores are used)
```console
real	1m56.233s
user	11m45.820s
sys	25m28.952s
```

# Literautre Review

The marginal likelihood is composed of a sum over the marginal likelihoods of individual datapoints
$$
\log p_{\boldsymbol{\theta}}(\mathbf{x}^{(1)},\cdots,\mathbf{x}^{(N)})=\sum_{i=1}^{N}\log p_{\boldsymbol{\theta}}(\mathbf{x}^{(i)})
$$
, which can each be rewritten as:
$$
\log p_{\boldsymbol{\theta}}(\mathbf{x}^{(i)})=D_{KL}(q_{\boldsymbol{\phi}}(\mathbf{z}|\mathbf{x}^{(i)})||p_{\boldsymbol{\theta}}(\mathbf{z}|\mathbf{x}^{(i)}))+\mathcal{L}(\boldsymbol{\theta},\boldsymbol{\phi};\mathbf{x}^{(i)}) 
$$

The fırst RHS term is the KL divergence of the approximate from the true posterior. Since this **KL- divergence is non- negative**, the second RHS term,  $\mathcal{L} ( \boldsymbol{\theta }, \boldsymbol{\phi }; \mathbf{x} ^{( i) })$ is called the **(variational) lower bound** on the marginal likelihood of datapoint $i$, and can be written as:

$$\log p_{\boldsymbol{\theta}}(\mathbf{x}^{(i)})\geq\mathcal{L}(\boldsymbol{\theta},\boldsymbol{\phi};\mathbf{x}^{(i)})=\mathbb{E}_{q_{\boldsymbol{\phi}}(\mathbf{z}|\mathbf{x})}\left[-\log q_{\boldsymbol{\phi}}(\mathbf{z}|\mathbf{x})+\log p_{\boldsymbol{\theta}}(\mathbf{x},\mathbf{z})\right]$$

which can also be written as:

$$\mathcal{L}(\boldsymbol{\theta},\boldsymbol{\phi};\mathbf{x}^{(i)})=-D_{KL}(q_{\boldsymbol{\phi}}(\mathbf{z}|\mathbf{x}^{(i)})||p_{\boldsymbol{\theta}}(\mathbf{z}))+\mathbb{E}_{q_{\boldsymbol{\phi}}(\mathbf{z}|\mathbf{x}^{(i)})}\left[\log p_{\boldsymbol{\theta}}(\mathbf{x}^{(i)}|\mathbf{z})\right]$$
We want to differentiate and optimize the lower bound $\mathcal{L}(\boldsymbol{\theta},\boldsymbol{\phi};\mathbf{x}^{(i)})$ w.r.t. both the variational parameters $\phi$ and generative parameters $\theta.$ However, the gradient of the lower bound w.r.t. $\phi$ is a bit problematic. The usual (naïve) Monte Carlo gradient estimator for this type of problem is: 
$$
\nabla _{\boldsymbol{\phi }}\mathbb{E} _{q_{\boldsymbol{\phi }( \mathbf{z} ) }}\left [ f( \mathbf{z} ) \right ] = \mathbb{E} _{q_{\boldsymbol{\phi }( \mathbf{z} ) }}\left [ f( \mathbf{z} ) \nabla _{q_{\boldsymbol{\phi }( \mathbf{z} ) }}\log q_{\boldsymbol{\phi }}( \mathbf{z} ) \right ] \simeq \frac 1L\sum _{l= 1}^Lf( \mathbf{z} ) \nabla _{q_{\boldsymbol{\phi }( \mathbf{z} ^{( l) }) }}\log q_{\boldsymbol{\phi }}( \mathbf{z} ^{( l) })
$$ 
where $\mathbf{z}^{(l)}\sim q_{\boldsymbol{\phi}}(\mathbf{z}|\mathbf{x}^{(i)}).$ 
This gradient estimator exhibits exhibits very high variance (see.g. [BJP12]) and is impractical for our purposes.

# References

1. [Variational Autoencoders | Generative AI Animated](https://www.youtube.com/watch?v=qJeaCHQ1k2w)
2. [台大資訊 深度學習之應用 | ADL 10.3: Variational Auto-Encoder (VAE) 控制特徵的分布以生成用](https://www.youtube.com/watch?v=Dg0YcABQ_aU)
3. [深度理解变分自编码器(VAE) | 从入门到精通](https://mp.weixin.qq.com/s/LwtwaPEkrhFMwazY3PfRew)
4. [VAE原理及示例代码](https://mp.weixin.qq.com/s/ZjAK_Z7jvrPMGslKiYca_Q)
5. [机器学习方法—优雅的模型（一）：变分自编码器（VAE）](https://zhuanlan.zhihu.com/p/348498294?utm_psn=1866895402294468608)
6. [Autoencoders for Image Reconstruction in Python and Keras](https://stackabuse.com/autoencoders-for-image-reconstruction-in-python-and-keras/)
7. [Diederik P. Kingma and Max Welling (2019), “An Introduction to Variational Autoencoders”, Foundations and Trends in Machine Learning: Vol. xx, No. xx, pp 1–18. DOI: 10.1561/XXXXXXXXX](https://arxiv.org/pdf/1906.02691)