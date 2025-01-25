---
layout: post
title: ML | AI for Numerical Weather Prediction (NWP)
categories: ML
tags: [NWP, ML, AI]
author: wpsze
date: 2024-10-09 21:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://lh3.googleusercontent.com/PIGlxJLhF3Eit7xSXvVPmm3ZnOYspa9a8RcRkzfEdSnhy2rVubJmIondGQGiKF3TbHTIUOi6w_8xAej5UJ--c7o_8OukH_bDi4gYuEaQ0N6d_BXRQw=w2140-rw
banner_img: https://lh3.googleusercontent.com/PIGlxJLhF3Eit7xSXvVPmm3ZnOYspa9a8RcRkzfEdSnhy2rVubJmIondGQGiKF3TbHTIUOi6w_8xAej5UJ--c7o_8OukH_bDi4gYuEaQ0N6d_BXRQw=w2140-rw
---

# Introduction to AI in Numerical Weather Prediction

Artificial Intelligence (AI) is transforming the field of Numerical Weather Prediction (NWP), significantly enhancing the accuracy and efficiency of weather forecasting. Traditional NWP relies on complex physical models to simulate atmospheric processes, but the integration of AI and machine learning (ML) is introducing new methodologies that leverage vast datasets to improve predictions.

AI technologies, particularly machine learning algorithms, excel at analyzing large volumes of meteorological data from diverse sources, including satellites, radar systems, and surface sensors. These algorithms can identify intricate patterns and correlations that may be overlooked by conventional methods, leading to more precise forecasts. For instance, models like Google DeepMind's GraphCast can predict weather conditions up to ten days in advance in less than a minute, outperforming traditional systems that require hours on supercomputers.

{% note warning %}
[ECMWF | AIFS Blog](https://www.ecmwf.int/en/about/media-centre/aifs-blog)
{% endnote %}

- [Scores of forecasts of upper-air parameters by AIFS - experimental machine learning model](https://charts.ecmwf.int/products/plwww_3m_fc_aifs_wp_mean?area=Northern%20Extra-tropics&parameter=Geopotential%20500hPa&score=Root%20mean%20square%20error)
<!-- - [Scores of forecasts of upper-air parameters by experimental machine learning models](https://charts.ecmwf.int/streaming/20241009-0750/81/ps2png-worker-commands-76f744c54b-4xnd6-6fe5cac1a363ec1525f54343b6cc9fd8-zisedq81.png)

![ECMWF | Scores of forecasts of upper-air parameters by experimental machine learning models **(Higher is better)**](https://charts.ecmwf.int/streaming/20241009-0750/81/ps2png-worker-commands-76f744c54b-4xnd6-6fe5cac1a363ec1525f54343b6cc9fd8-zisedq81.png)

![ECMWF | Scores of forecasts of upper-air parameters by experimental machine learning models **(Lower is better)**](https://charts.ecmwf.int/streaming/20241009-0710/e3/ps2png-worker-commands-76f744c54b-rk8tn-6fe5cac1a363ec1525f54343b6cc9fd8-ue79gh2y.png) -->

# Run AI models yourself from ECMWF open data

26 September 2024\
The AIFS team

- <https://www.ecmwf.int/en/about/media-centre/aifs-blog/2024/run-ai-models-yourself-ecmwf-open-data>
- To announce that users can now run AI models using ECMWF's open data themselves. This breakthrough democratizes access to cutting-edge weather forecasting technology and opens new possibilities for research, education, and application development.
- success of machine learning models like FourCastNet, Pangu-Weather, GraphCast and FuXi, ECMWF is now empowering users to harness these AI tools directly. 

## The power of open data and AI

By combining ECMWF's high-quality open data with state-of-the-art AI models, users can:

- generate rapid forecasts on their own computer
- explore multi-model ensemble forecasting techniques with AI
- conduct research on model performance and intercomparison without any accounts/licences.
- **Plugins already exist for Pangu-Weather, FourCastNet (versions 1 and 2), GraphCast, FuXi and, most recently, Aurora.**
- <https://github.com/ecmwf-lab/ai-models>

```sh
pip install ai-models 

pip install ai-models-panguweather # Or another model 
pip install ai-models-fourcastnet
pip install ai-models-graphcast  # Install details at https://github.com/ecmwf-lab/ai-models-graphcast
pip install ai-models-fourcastnetv2 

ai-models panguweather --input ecmwf-open-data 
```

See 

- [ai-models-panguweather](https://github.com/ecmwf-lab/ai-models-panguweather), 
- [ai-models-fourcastnet](ai-models-fourcastnet), 
- [ai-models-fourcastnetv2](ai-models-fourcastnetv2) and 
- [ai-models-graphcast](ai-models-graphcast)

for more details about these models.

{% note warning %}
To fix ONNX or version issue, we suggest that you **install ai-models in a conda environment and install the CUDA libraries in that environment**.
{% endnote %}

```sh
conda create -n ai-models python=3.10
conda activate ai-models
conda install cudatoolkit
pip install ai-models
...
```

### Running the models

To run model, make sure it has been installed, then simply run:

{% note warning %}
By default, the model will be run for **a 10-day lead time (240 hours)**, using **yesterday's 12Z analysis** from ECMWF's MARS archive.
{% endnote %}

```sh
ai-models <model-name>

# To produce a 15 days forecast, use the --lead-time HOURS option:
ai-models --lead-time 360 <model-name>
```

### Performances Considerations

The AI models can run on a CPU; however, they perform significantly better on a GPU. 

{% note warning %}
**A 10-day forecast can take several hours on a CPU but only around one minute on a modern GPU.**
{% endnote %}

{% note warning %}
**We strongly recommend running these models on a computer equipped with a GPU for optimal performance.**
{% endnote %}

It you see the following message when running a model, it means that the **ONNX runtime was not able to find a the CUDA libraries on your system**:

> [W:onnxruntime:Default, onnxruntime_pybind_state.cc:541 CreateExecutionProviderInstance] Failed to create CUDAExecutionProvider. Please reference https://onnxruntime.ai/docs/reference/execution-providers/CUDA-ExecutionProvider.html#requirements to ensure all dependencies are met.

### Assets
The AI models rely on weights and other assets created during training. The first time you run a model, you will need to download the **trained weights** and **any additional required assets**.

```sh
# To download the assets before running a model, use the following command:
ai-models --download-assets <model-name>

# The assets will be downloaded if needed and stored in the current directory. You can provide a different directory to store the assets:
ai-models --download-assets --assets <some-directory> <model-name>

# Then, for running, later on, simply use:
ai-models --assets <some-directory>  <model-name>
# or 
export AI_MODELS_ASSETS=<some-directory>
ai-models <model-name>
```

For better organisation of the assets directory, you can use the **--assets-sub-directory** option. This option will store the assets of each model in its own subdirectory within the specified assets directory.

### Input data
The models require input data (initial conditions) to run. You can provide the input data using different sources, as described below:

#### From MARS

{% note warning %}
By default, **ai-models use yesterday's 12Z analysis from ECMWF**, fetched from the Centre's MARS archive using the [ECMWF WebAPI](https://www.ecmwf.int/en/computing/software/ecmwf-web-api). **You will need an ECMWF account to access that service**.
{% endnote %}

```sh
# To change the date or time, use the --date and --time options, respectively:

ai-models --date YYYYMMDD --time HHMM <model-name>
```

#### From the CDS (ERA5)

You can start the models **using ERA5 (ECMWF Reanalysis version 5) data** for the [Copernicus Climate Data Store (CDS)](https://cds.climate.copernicus.eu/). You will need to create an account on the CDS. The data will be downloaded using the [CDS API](https://cds.climate.copernicus.eu/api-how-to)).

To access the CDS, simply add **--input cds** on the command line. **Please note that ERA5 data is added to the CDS with a delay**, so you will also have to provide a date with --date YYYYMMDD.

```sh
ai-models --input cds --date 20230110 --time 0000 <model-name>
```

#### From a GRIB file

If you have input data in the GRIB format, you can provide the file using the **--file** option:

```sh
ai-models --file <some-grib-file> <model-name>
```

{% note warning %}
The GRIB file can contain more fields than the ones required by the model. The ai-models command will automatically select the necessary fields from the file.
{% endnote %}

{% note warning %}
GFS grib files?
{% endnote %}

To find out the list of fields needed by a specific model as initial conditions, use the following command:

```sh
ai-models --fields <model-name>
```

### Output

By default, the model output will be written in GRIB format in a file called **\<model-name\>.grib**. You can change the file name with the option **--path <file-name>**. If the path you specify contains placeholders between { and }, multiple files will be created based on the [eccodes](https://confluence.ecmwf.int/display/ECC) keys. For example:

```sh
ai-models --path 'out-{step}.grib' <model-name>
```

**This command will create a file for each forecasted time step.**

If you want to disable writing the output to a file, use the **--output none** option.

### Command line options

It has the following options:

- `--help`: Displays this help message.
- `--models`: Lists all installed models.
- `--debug`: Turns on debug mode. This will print additional information to the console.

#### Input

- `--input INPUT`: The input source for the model. This can be a `mars`, `cds` or `file`.
- `--file FILE`: The specific file to use as input. This option will set `--source` to `file`.

- `--date DATE`: The analysis date for the model. This defaults to yesterday.
- `--time TIME`: The analysis time for the model. This defaults to 1200.

#### Output

- `--output OUTPUT`: The output destination for the model. Values are `file` or `none`.
- `--path PATH`: The path to write the output of the model.

#### Run

- `--lead-time HOURS`: The number of hours to forecast. The default is 240 (10 days).

#### Assets management

- `--assets ASSETS`: Specifies the path to the directory containing the model assets. The default is the current directory, but you can override it by setting the `$AI_MODELS_ASSETS` environment variable.
- `--assets-sub-directory`: Enables organising assets in `<assets-directory>/<model-name>` subdirectories.
- `--download-assets`: Downloads the assets if they do not exist.

#### Misc. options

- `--fields`: Print the list of fields needed by a model as initial conditions.
- `--expver EXPVER`: The experiment version of the model output.
- `--class CLASS`: The 'class' metadata of the model output.
- `--metadata KEY=VALUE`: Additional metadata metadata in the model output

# Available products
The output of these ML models are forecasts with 6-hourly time steps out to 10 days initialised from the ECMWF operational analysis. All forecasts are produced on a 0.25 x 0.25-degree grid.

## FourCastNetv2-small:
Upper-air fields are z (geopotential), r (relative humidity), t (temperature), u (U component of wind), and v (V component of wind) on the following pressure levels: 1000hPa, 925hPa, 850hPa, 700hPa, 600hPa, 500hPa, 400hP, 300hPa, 250hPa, 200hPa, 150hPa, 100hPa and 50hPa

The single-level fields are msl (mean sea level pressure), sp (surface pressure), 10u (10 metre U wind component), 10v (10 metre U wind component), 100u (100 metre U wind component), 100v (100 metre V wind component, 2t (2 metre temperature) and tcwv (total column vertically-integrated water vapour).

- **NVIDIA**
- [Pathak, J., Subramanian, S., Harrington, P., Raja, S., Chattopadhyay, A., Mardani, M., ... & Anandkumar, A. (2022). Fourcastnet: A global data-driven high-resolution weather model using adaptive fourier neural operators. arXiv preprint arXiv:2202.11214.](https://arxiv.org/pdf/2202.11214.pdf?trk=public_post_comment-text)
- [Bonev, B., Kurth, T., Hundt, C., Pathak, J., Baust, M., Kashinath, K., & Anandkumar, A. (2023, July). Spherical fourier neural operators: Learning stable dynamics on the sphere. In International conference on machine learning (pp. 2806-2823). PMLR.](https://proceedings.mlr.press/v202/bonev23a/bonev23a.pdf)
- **Fourier Neural Operators (FNOs)** have proven to be an efficient and effective method for **resolution independent operator learning** in a broad variety of application areas across scientific machine learning.
- [智能模式系列｜一文读懂FourCastNet：首个全球高分辨率人工智能天气预报模型 (**推薦**)](https://zhuanlan.zhihu.com/p/669083609)
- FourCastNet 的网络模型使用了 AFNO 网络，该网络此前常用于图像分割任务。这个网络通过 FNO 弥补了 ViT 网络的缺点，使用傅立叶变换完成不同 token 信息交互，显著减少了高分辨率下 ViT 中 self-attention 的计算量。
- 由于完整复现 FourCastNet 需要 5TB+ 的存储空间和 64 卡的 GPU 资源，需要的存储资源比较多 [link](https://paddlescience-docs.readthedocs.io/zh-cn/latest/zh/examples/fourcastnet/#5)

{% gi 2 2 %}
![](https://i.imgur.com/5I6TxUo.png){width=600}
![](https://i.imgur.com/8KnDz1x.png){width=600}
{% endgi %}

## Graphcast:
Upper-air fields are z (geopotential), q (specific humidity), t (temperature), u (U component of wind), v (V component of wind), w (vertical wind component) on the following pressure levels: 1000hPa, 925hPa, 850hPa, 700hPa, 600hPa, 500hPa, 400hP, 300hPa, 250hPa, 200hPa, 150hPa, 100hPa and 50hPa

The single-level fields are msl (mean sea level pressure), 10u (10 metre U wind component), 10v (10 metre U wind component), and 2t (2 metre temperature). 

- [Lam, R., Sanchez-Gonzalez, A., Willson, M., Wirnsberger, P., Fortunato, M., Alet, F., ... & Battaglia, P. (2023). Learning skillful medium-range global weather forecasting. Science, 382(6677), 1416-1421.](https://www.science.org/stoken/author-tokens/ST-1550/full)
- GraphCast 利用所謂 **圖神經網路(Graph Neural Network，GNN)** 的機器學習架構，以超過 40 年的 ECMWF 天氣歷史資料訓練模型。它能處理當下和 6 小時前全球大氣狀態，並在 1 分鐘內以搭載 TPU v4 的雲端電腦產生 10 天天氣預報。

![](https://i.imgur.com/ysa2HNE.png){width=600}

## Pangu-Weather:
Upper-air fields are z (geopotential), q (specific humidity), t (temperature), u (U component of wind), and v (V component of wind) on the following pressure levels: 1000hPa, 925hPa, 850hPa, 700hPa, 600hPa, 500hPa, 400hPa, 300hPa, 250hPa, 200hPa, 150hPa, 100hPa and 50hPa

The single-level fields are msl (mean sea level pressure), 10u (10 metre U wind component), 10v (10 metre U wind component) and 2t (2 metre temperature).

华为云盘古气象大模型

- [Bi, K., Xie, L., Zhang, H., Chen, X., Gu, X., & Tian, Q. (2023). Accurate medium-range global weather forecasting with 3D neural networks. Nature, 619(7970), 533-538.](https://www.nature.com/articles/s41586-023-06185-3)
- **2023年11月中旬**發表在《自然》（Nature）上的研究顯示，由人工智慧驅動的伏羲氣候氣象大模型可以提前15天預測全球天氣變化，比如氣溫、風速和氣壓等天氣參數。該模型的預報精度優於被譽為「黃金標準」的歐洲中期天氣預報中心（ECMWF），生成結果的速度也比傳統模型快千倍。
- 华为云盘古大模型研发团队发现，AI气象预报模型的精度不足主要有两个原因：
  - 第一，原有的AI气象预报模型都是基于2D神经网络，**无法很好地处理不均匀的3D气象数据**；
  - 第二，**AI方法缺少数学物理机理约束**，因此在迭代的过程中会不断积累迭代误差。为此，团队创造性地提出了适应地球坐标系统的 **三维神经网络（3D Earth-Specific Transformer）** 来处理复杂的不均匀3D气象数据，并且使用层次化时域聚合策略来减少预报迭代次数，从而减少迭代误差。通过在43年的全球天气数据上训练深度神经网络，盘古气象大模型在精度和速度方面超越传统数值预测方法。
  - 该网络由8个编码器和8个解码器的结构构成，每个编码器-解码器层是一个3DEST块，利用了transformer的变体（swin transformer）来继承窗口注意力机制
- [华为云和深圳市气象局发布人工智能区域预报模型“智霁”1.0](https://www.huawei.com/cn/news/2024/3/pangu-weather)
  - 华为云联合深圳气象局率先开展人工智能区域预报模型联创研发
  - “智霁”区域模型以华为云盘古气象大模型为基础，融合区域高质量气象数据集，可快速得到**未来5天深圳及周边地区空间分辨率为3公里**
  - 克服了三个技术难题：
    - 一是对多源和多尺度数据的融合性处理，包含3公里及25公里不同尺度再分析数据以及多源观测数据等作为输入；
    - 二是联合创新了全球模型与区域模型的融合架构，可以更好地处理区域边界，充分考虑区域外有效信息对区域内预测结果的影响；
    - 三是利用3DEST神经网络自监督学习特征，将全量数据集合预训练，提升预报精度。
    - 由中国气象局广东省区域数值天气预报重点实验室研发，**广州超算中心支撑广东省气象局构建的“9-3-1”高分辨率天气模式系统**，预报能力已进入国际领先行列，推动了精细化天气预报技术的深入发展。该模式系统多年来，在**广州超算中心“天河二号”**超算系统上业务级稳定运行，实现了关键核心技术的自主可控，为台风、暴雨和强对流等天气的精准预报提供了科技支撑，也为城市防灾减灾、区域生态文明建设、大气污染联防联控提供了科学决策支持。

![](https://i.imgur.com/Ql7iIb9.png){width=600}

## Fuxi:
### [Addition of “Fuxi” AI Model Forecast Products on the “Earth Weather” Webpage](https://www.hko.gov.hk/en/Whats-New/108763/Addition-of-Fuxi-AI-Model-Forecast-Products-on-the-Earth-Weather-Webpage)

- [Chen, L., Zhong, X., Zhang, F., Cheng, Y., Xu, Y., Qi, Y., & Li, H. (2023). FuXi: A cascade machine learning forecasting system for 15-day global weather forecast. npj Climate and Atmospheric Science, 6(1), 190.](https://www.nature.com/articles/s41612-023-00512-1)
- 复旦大学人工智能创新与产业研究院、上海科学智能研究院李昊研究员与漆远教授团队携手中国气象局气候研究开放实验室陆波研究员团队在国际权威综合性期刊《Nature Communications》杂志发表题为“A machine learning model that outperforms conventional global subseasonal forecast models”的研究论文，详细介绍 **“伏羲”次季节-季节预测模型 (FuXi-S2S)**。
- 由**上海科学智能研究院、复旦大学、中国国家气候中心**联手打造的“伏羲”次季节大模型，在技术难题上实现大突破，将对气候变化风险应对，起到至关重要的作用
- 伏羲電腦模式 由**復旦大學人工智能創新與產業研究院**提供。
- 2023年11月中旬發表在《自然》（Nature）上的研究顯示，由人工智慧驅動的伏羲氣候氣象大模型可以提前15天預測全球天氣變化，比如氣溫、風速和氣壓等天氣參數。該模型的預報精度優於被譽為「黃金標準」的歐洲中期天氣預報中心（ECMWF），生成結果的速度也比傳統模型快千倍。**在此基礎上，研究團隊進一步優化伏羲氣候氣象大模型，終於推出預測周期長達45天之久的伏羲次季節大模型**。相較於伏羲中短期模型，三倍的預測周期時長充滿變數。於是，伏羲大模型先把隨機採樣引入與ChatGPT類似的**Transformer架構**，再通過**集合預報**反映次季節預測的不確定性。

{% gi 2 2 %}
![](https://i.imgur.com/pCL58bw.png)
![](https://i.imgur.com/g65NHvJ.png)
{% endgi %}

# Hands-on

# ECMWF: Anemoi
## GNN for Glogal-regional or Limited Area Modeling

- Oskarsson, J., Landelius, T., & Lindsten, F. (2023). Graph-based Neural Weather Prediction for Limited Area Modeling. [arXiv preprint arXiv:2309.17370](https://arxiv.org/pdf/2309.17370).
  - <https://github.com/mllam/neural-lam>
  - adapt the graph-based NeurWP approach to the limited area setting and propose a multi-scale hierarchical model extension
  - utilize Graph Neural Networks (GNNs) to produce forecasts 
  - Global: GraphCast model
  - Hierarchical Graph Neural Networks (MultiScale MeshGraphNets: <https://arxiv.org/pdf/2210.00612>)

# References

1. [ECMWF | AIFS Blog](https://www.ecmwf.int/en/about/media-centre/aifs-blog)
2. [ECMWF | 2024 Run AI models yourself from ECMWF open data](https://www.ecmwf.int/en/about/media-centre/aifs-blog/2024/run-ai-models-yourself-ecmwf-open-data)
3. [ECMWF | 2024 Data-driven regional modelling](https://www.ecmwf.int/en/about/media-centre/aifs-blog/2024/data-driven-regional-modelling)
   - some great work for limited-area modelling with Neural-LAM (Oskarsson et al. 2023).
   - In contrast to Neural-LAM, the approach we are currently testing keeps a global model. Over the domain where high-resolution data are available, here the Nordics, a finer-resolution graph is used in the model, to be able to ingest and output data from high-resolution regional analysis, reanalysis or forecast datasets.
4. [ECMWF | 2024 Enter the ensembles](https://www.ecmwf.int/en/about/media-centre/aifs-blog/2024/enter-ensembles)
5. [ECMWF | 2023 Machine Learning model data](https://www.ecmwf.int/en/forecasts/dataset/machine-learning-model-data)
6. [AI预报天气很行，但是也没那么行 | 中科院物理所 | 2024-12-27 12:21](https://news.qq.com/rain/a/20241227A04YGV00)
   1. 除了上文提到的进入《Nature》正刊的华为盘古大模型、谷歌GraphCast模型以外，还有英伟达FourCastNet模型、微软ClimaX模型、复旦大学伏羲大模型、上海人工智能实验室“风乌”GHR大模型、清华大学和中国气象局的NowcastNet预报大模型等等。而华为的盘古大模型已经在欧洲中期天气预报中心（ECMWF）实现业务运行，此外作为全世界最先进的天气预报机构，ECMWF也发布了大模型AIFS。