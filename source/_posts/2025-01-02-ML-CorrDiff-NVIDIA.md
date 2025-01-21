---
layout: post
title: ML | CorrDiff (NVIDIA)
categories: [ML]
tags: [ML, AI, pytorch, NVIDIA]
author: wpsze
date: 2025-01-02 10:01:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---


# News

- [生成式AI與傳統數值天氣預報的結合NVIDIA CorrDiff天氣降尺度模型 | 科學月刊 | 2024-07-15](https://www.scimonth.com.tw/archives/10997)
  - 輝達（NVIDIA）執行長黃仁勳今（2024）年3月在美國舉辦的GTC（GPU technology conference）大會及6月在臺灣舉辦的臺北國際電腦展（COMPUTEX）上，皆展示了一項稱為「CorrDiff」（Corrector Diffusion）的生成式人工智慧（generative artificial intelligence, generative AI）技術，並表示它可以應用於區域高解析度天氣預報上。
  - 對於氣象模擬與預報來說，縮小網格解析度一直是一大挑戰。傳統物理模式受限於運行時所需的龐大運算量，當今執行全球預報的最高解析度模式為ECMWF的九公里解析度全球模式。
  - 氣象署發展的「RWRF」（Radar WRF）高解析度極短期劇烈天氣預報系統（延伸閱讀2），可提升對臺灣區域劇烈天氣的預報能力。此系統奠基於美國國家大氣研究中心（National Center for Atmospheric Research）的「WRF」（Weather Research & Forecasting）數值天氣預報模式與它的資料同化系統（WRFDA），再由氣象署針對臺灣本地需求修改發展，自2016年起開始作業運作。RWRF系統的模式解析度為兩公里，涵蓋範圍如圖一所示。它能運行區域高解析度的**快速更新循環資料同化**，將高解析度的即時觀測資料運用於區域模式中，藉此得到區域範圍內更準確的分析與短期預報資料，比單純的動力降尺度預報更為複雜。**其中，資料同化使臺灣區域的氣象雷達觀測資料每半小時進入此模式中、地面氣象站觀測資料（包含人工和自動氣象站）每一小時進入模式中。RWRF系統以每小時的更新頻率提供13小時長度的預報資料，** 作為署內預報中心與民航局等相關政府單位的參考。
  - 我們可以知道此模型在訓練過程中採用的輸入資料為ERA5全球約25公里解析度再分析資料（僅擷取與RWRF範圍相近的臺灣鄰近範圍）；而採用的輸出資料則為氣象署RWRF系統的兩公里解析度歷史分析資料，包含垂直最大雷達回波（radar reflectivity）、兩公尺高氣溫、十公尺高東西向與南北向風速等四個單層變數。
  - 在氣象署提供的四年餘的RWRF資料中，前三年被用作為訓練資料，而後面的一年四個月則是驗證資料。
  - 由此設計可知，CorrDiff模型的目標是將較低解析度（約25公里）的全球天氣預報模式資料，提升解析度成為高解析度（兩公里）的資料，並在過程中獲得對區域劇烈天氣預報重要的氣象變數──最大雷達回波、地面氣溫與風速。
  - 就使用長期歷史資料建立模型而論，CorrDiff可說是一種進階（AI）版的統計降尺度；但它能得到高解析度網格資料而非觀測點上的資料，以目標與效果而言比較近似於動力降尺度技術。
  - **CorrDiff模型採用兩步驟來完成降尺度的計算**：
    - **第一步驟**是先以常應用於影像處理、能有效獲取影像特徵的UNet回歸架構，進行資料樣本平均值的修正；
    - **第二步驟**則利用生成式AI中的擴散（diffusion）演算法，強化資料的高解析度細節與極值。擴散演算法透過將圖像加入雜訊，再去除雜訊的循環過程來執行，過去常應用於圖片的去噪（denoising）、修補或超解析度成像等。
  - 最後，CorrDiff模型的運算速度遠遠快於RWRF系統的數值天氣預報模式，後者需要在高速運算電腦上使用大量中央處理器（central processing unit, CPU）核心進行運算，而前者在單一圖形處理器（graphics processing unit, GPU）上即可用更短的時間完成運算，兩者的能源使用效率有天壤之別。
  - 參考預印本論文中所提，未來此技術可能有以下幾種不同的應用方式
    - 一、由傳統全球模式數值天氣預報資料，降尺度成為高解析度預報。
    - 二、由AI全球天氣預報模式的預報資料，降尺度成為高解析度預報。
    - 三、氣候模擬研究（例如氣候變遷推估）上的降尺度應用，特別是用於評估未來極端天氣事件發生的機率。
    - 四、用於與RWRF系統截然不同的其他地理區域的降尺度（可能仍需要取得對應區域的高解析度氣象資料用於模型訓練）。
      - 其中，第三點與第四點的應用方式尚未直接有研究結果，仍為潛在應用，有待未來更多研究來證明與展示它的能力。
- 輝達執行長黃仁勳皆提到與中央氣象署合作開發的生成式AI模型「CorrDiff」，能透過AI將氣象資料解析度從25公里降至2公里，與傳統模式相比，更可讓單次推理速度提高1,000倍、能耗減少3,000倍。[link: 2024 年 09 月 28 日](https://technews.tw/2024/09/28/cwa-corrdiff-nvidia/)
  - 用CorrDiff來「降尺度」（也就是提高解析度）的重要性由此而生。
  - 以CorrDiff來說，便是由氣象署提供四年份、以每小時計的2公里氣象資料，共含四個變數，而輝達則拿出25公里氣象資料，並負責演算法及提供超級電腦運算資源，採「監督式學習」（Supervised learning）進行訓練，共同打磨出這款模型。
  - 「輝達在做CorrDiff之前，其實已經在全球天氣預報有非常好的成果，所以他接著就要找一個小尺度、天氣最困難最複雜的地方，」陳柏孚分析，台灣處在最大季風區、有複雜地形，又有颱風、梅雨，本身就夠挑戰、夠有特色，雙方合作是雙贏。

# Paper

- [Mardani, Morteza, et al. "Residual corrective diffusion modeling for km-scale atmospheric downscaling." arXiv preprint arXiv:2309.15214 (2023).](https://arxiv.org/abs/2309.15214)

Consider a specific region on Earth, mapped onto a two-dimensional grid. Our input $\mathbf{y} \in \mathbb{R} ^{c_\mathrm{in}\times m\times n}$ is a low-resolution meteorological data taken from a 25-km global reanalysis, or weather forecasting model (e.g., FourCastNet [46,36,7], or the Global Forecast System (GFS) [44]). Here, **$c_\mathrm{in}$ represents the number of input channels and $m,n$ represent the dimensions of a 2D subset of the globe**. **Our targets $\mathbf{x} \in \mathbb{R} ^{c_\mathrm{out}\times p\times q}$ come from corresponding data aligned in time $c_\mathrm{out}$ but having higher resolution, i.e., $p>m$ and $q>n.$**

In our proof of concept we use the **ERA5 reanalysis as input**, over a subregion surrounding Taiwan, with $m=n=36,c_{in}=12$ and $c_\mathrm{out}=4.$ See Table S2 for details about the inputs and outputs. The target $\begin{array}{c}\text{data are 12.5 times higher resolution }(p=q=448)\end{array}$and **were produced using a radar-assimilating Weather Research and Forecasting (WRF) physical simulator [48] provided by the Central Weather Administration of Taiwan (CWA) [15] (i.e., CWA-WRF)**, which employs a dynamical downscaling approach. Though imperfect, WRF is a SOTA model for km-scale weather simulations and is used operationally by several national weather agencies.

$$
\begin{align*}
\mbox{CorrDiff} : \mathbf{y} \in \mathbb{R} ^{c_\mathrm{in}\times m\times n} \longrightarrow \mathbf{x} \in \mathbb{R} ^{c_\mathrm{out}\times p\times q} \\
p>m , q>n
\end{align*}
$$

![](https://i.imgur.com/LgX4aZr.png){width=800}
![](https://i.imgur.com/e9PX9n8.png){width=800}

CorrDiff employs a two-step approach to effectively manage the complexities of multi-scale atmospheric data:

- **UNet Prediction**: The first step involves using a UNet architecture to predict the *conditional mean of the atmospheric variables based on lower-resolution data*.
- **Diffusion Correction**: The second step utilizes a correction diffusion model to predict the residuals, effectively refining the output by learning from the differences between the predicted mean and actual observations.
- This method is **like to Reynolds decomposition in fluid dynamics**, allowing for a more manageable learning process by isolating generative learning to stochastic scales. 

Importantly, **兩步驟來先後訓練**

- **CorrDiff模型採用兩步驟來完成降尺度的計算**：
  - **第一步驟**是先以常應用於影像處理、能有效獲取影像特徵的UNet回歸架構，進行資料樣本平均值的修正；
  - **第二步驟**則利用生成式AI中的擴散（diffusion）演算法，強化資料的高解析度細節與極值。擴散演算法透過將圖像加入雜訊，再去除雜訊的循環過程來執行，過去常應用於圖片的去噪（denoising）、修補或超解析度成像等。

# Getting started

To build custom CorrDiff versions, you can get started by training the **“Mini” version of CorrDiff**, which uses smaller training samples and a smaller network to reduce training costs from thousands of GPU hours to **around 10 hours on A100 GPUs** while still producing reasonable results. It also includes a simple data loader that can be used as a baseline for training CorrDiff on custom datasets.


