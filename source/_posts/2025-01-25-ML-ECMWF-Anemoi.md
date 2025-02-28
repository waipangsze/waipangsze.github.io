---
layout: post
title: ML | ECMWF | Anemoi 
categories: ML
tags: [NWP, ML, AI, ECMWF, Anemoi]
author: wpsze
date: 2025-01-25 14:59:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/iZNjOUN.png
banner_img: https://i.imgur.com/iZNjOUN.png
---

# Anemoi

[Anemoi](https://anemoi.ecmwf.int/) is a collaborative and open-source framework for developing machine learning weather forecasting models. Named after the Greek gods of the winds, the goal of Anemoi is to provide the key building blocks to train state‑of-the‑art data-driven weather forecasting models and run them in an operational context. As a framework it seeks to handle many of the complexities that meteorological organisations will share, allowing them to easily train models from existing recipes but with their own data.

- [AIFS: a data-driven probabilistic forecasting system | Slides | November 13, 2024](https://eurocc-austria.at/fileadmin/eurocc-main/events/Webinar_Weather_Climate_13.11.2024/4_Mariana_Clare_AIFS_a_data-driven_probabilistic_weather_forecasting_system_-2.pdf)
- [AIFS - ECMWF’s data-driven forecasting system | arXiv | 03 Jun 2024](https://arxiv.org/abs/2406.01465v1)
- [Experimental: AIFS, Experimental: Machine learning models | Charts](https://charts.ecmwf.int/?facets=%7B%22Product%20type%22%3A%5B%22Experimental%3A%20AIFS%22%2C%22Experimental%3A%20Machine%20learning%20models%22%5D%7D)
- [https://huggingface.co/ecmwf | ecmwf/aifs-single](https://huggingface.co/ecmwf)
  - To generate a new forecast using AIFS, you can use `anemoi-inference`. In the following notebook, a step-by-step workflow is specified to run the AIFS using the HuggingFace model:

## AIFS
**AIFS** is based on a **graph neural network (GNN) encoder and decoder**, and a sliding window transformer processor, and is trained on ECMWF’s ERA5 re-analysis and ECMWF’s operational numerical weather prediction (NWP) analyses.

- AIFS training data, [AIFS: a new ECMWF forecasting system](https://www.ecmwf.int/en/newsletter/178/news/aifs-new-ecmwf-forecasting-system)
- [First update to the AIFS | 16 January 2024](https://www.ecmwf.int/en/about/media-centre/aifs-blog/2024/first-update-aifs)
- [Bringing together users, researchers and coders in ECMWF’s machine learning efforts | 15 May 2024](https://www.ecmwf.int/en/about/media-centre/news/2024/bringing-together-users-researchers-and-coders-ecmwfs-machine)
  - “Because of the code adaptation we’ve done, we can easily expand parts of the AIFS and Anemoi to regional systems,” 

{% gi 2 2 %}
![](https://i.imgur.com/GBcuV9S.png){width=600}
![](https://i.imgur.com/zHcuWYK.png){width=600}
{% endgi %}

## Webinar: Anemoi - a framework for building the AIFS and other data-driven weather forecasting models | Virtual | April 30, 2024

- <https://events.ecmwf.int/event/409/>
- Description
  - Last year ECMWF introduced the AIFS, building on a series of innovative papers which demonstrated the potential of machine learning in creating data-driven weather forecasting models.
  - Machine learning models typically build on **open-source frameworks, such as Tensorflow, PyTorch and JAX** which allow communities to work together and build on one another's work. Weather forecasting brings its own challenges for machine learning, with large datasets and spatial-temporal problems.
  - In this webinar, we will introduce Anemoi, the toolbox from which the AIFS is trained. 
  - We have been developing this toolbox with a view to many users both inside and outside of ECMWF to **use the toolbox to train both global and local weather forecasting models**. The toolbox aims to allow users to bring their own datasets and customise their ML models.
  - We will introduce the toolbox, talk through the steps needed to train a data-driven model and explain how Anemoi helps with these tasks. Finally we will outline a roadmap for future Anemoi development.
- Additional utility tools exist in supporting repositories beyond these core modules. Anemoi builds on top of ECMWF’s Artificial Intelligence Forecasting System (AIFS), expanding that codebase to enable wider functionality for a greater range of users. **Future AIFS models will be trained using Anemoi**.

## Discover Anemoi: Webinar Series | Online | 13-30 January 2025

- <https://events.ecmwf.int/event/446/>

Discover Anemoi will feature six webinars, demonstrating the use of key components of the Anemoi ecosystem. The webinars will cover:

- An introduction to Anemoi
- Creating datasets
- Building graphs
- Training models
- Inference
- Contributing as a developer


# High resolution inside the Nordic

The AIFS is a global system, but work has also been going on in **Anemoi to create regional (limited-area) ML systems**. For example, *MET Norway* has created a regional model for Scandinavia ([Nipen et al., 2024, arXiv:2409.02891](https://arxiv.org/abs/2409.02891v1)).

The plot shows a 7-day forecast of 10 m wind speed (shading) and sea-level pressure (contours). The model has learned to forecast at high resolution (here ~10 km) inside the Nordic region, and at low resolution (here ~100 km) outside of this domain. The model successfully creates a higher-resolution structure over the Nordics.

- [Nipen, T. N., Haugen, H. H., Ingstad, M. S., Nordhagen, E. M., Salihi, A. F. S., Tedesco, P., ... & Chantry, M. (2024). Regional data-driven weather modeling with a global stretched-grid. arXiv preprint arXiv:2409.02891.](https://arxiv.org/pdf/2409.02891v1)
  - A **data-driven model (DDM)** suitable for regional weather forecasting applications is presented. The model extends the Artificial Intelligence Forecasting System by introducing a stretched-grid architecture that dedicates higher resolution over a regional area of interest and maintains a lower resolution elsewhere on the globe. The model is based on graph neural networks, which naturally affords arbitrary multi-resolution grid configurations.
  - The model is pre-trained on 43 years of global **ERA5** data at 31 km resolution and is further refined using 3.3 years of **2.5 km resolution operational analyses from the MetCoOp Ensemble Prediction System (MEPS)**.

The DDM was trained on compute nodes equipped with four AMD Instinct MI250X GPUs, each with 128 GB memory. To speed up training, we used **data parallelism across 32 model instances for stages A–C** and **16 for stage D**. For stages B–D, we used model parallelism to run one instance of the model on each node. This splits the nodes and edges of the graphs across multiple GPUs and is necessary for fitting the 31 km global and 2.5 km regional stretched-grid model within the available GPU memory

{% gi 5 3-2 %}
![](https://i.imgur.com/xHsB08i.png)
![](https://i.imgur.com/yDB2LwN.png)
![](https://i.imgur.com/i8uLpch.png)
![](https://i.imgur.com/20hdCXS.png)
![](https://i.imgur.com/zzSAUKE.png)
{% endgi %}

# Build Anemoi

**Anemoi** is a framework for developing machine learning weather forecasting models. It comprises of components or packages for 

- preparing training datasets, 
- conducting ML model training and 
- a registry for datasets and trained models. 

Anemoi provides tools for operational inference, including interfacing to verification software. As a framework it seeks to handle many of the complexities that meteorological organisations will share, allowing them to easily train models from existing recipes but with their own data.


- <https://anemoi.ecmwf.int/>

![](https://i.imgur.com/LqJLGkK.png){width=500}

```yml
name: anemoi
channels:
  - conda-forge
dependencies:
  - python==3.10.15
  - cuda==12.4.1
  - kerchunk
  - h5py
  - s3fs
  - pip:
    - anemoi-utils[all]
    - anemoi-transform[all]
    - anemoi-datasets[create]
    - anemoi-models
    - anemoi-graphs
    - anemoi-training
    - anemoi-inference
    - anemoi-registry[all]
```

## Dependencies

- Arrows indicate dependencies between packages. 
- Dashed lines indicate optional dependencies.

![](https://i.imgur.com/PIR32YC.png){width=300}

## Flow of data in inference

The schema above is a high-level overview of the data flow in the **inference process** (click on the image for a larger version).

Several key concepts are introduced:

Input:

- Prognostic variables:
- Diagnostic variables:
  
Output:

- Constant forcings:
- Dynamic forcings:
- Computed constants:
- Computed forcings:

![](https://i.imgur.com/69OpDZv.png){width=700}

## Hugging Face | AIFS-single via anemoi

- [Download the Model's Checkpoint](https://huggingface.co/ecmwf/aifs-single/tree/main) from [Hugging Face](https://huggingface.co/ecmwf/aifs-single)
  - Note the aifs_single_v0.2.1.ckpt checkpoint just contains the model’s weights. That file does not contain any information about the optimizer states, lr-scheduler states, etc.
  - Training Procedure
  - Training Hyperparameters
  - Speeds, Sizes, Times	
  - Evaluation
  - Known limitations
  - Technical Specifications
    - Hardware
    - Software
      - The model was developed and trained using the AnemoI framework.
- [https://huggingface.co/ecmwf/aifs-single/blob/main/run_AIFS_v0_2_1.ipynb](https://huggingface.co/ecmwf/aifs-single/blob/main/run_AIFS_v0_2_1.ipynb)
  - DATE = OpendataClient().latest()	

![](https://i.imgur.com/rOuLVhs.png){width=400}

### save input_state

These functions are all that you need for saving and loading almost any object:

```python
import pickle 

with open('saved_dictionary.pkl', 'wb') as f:
    pickle.dump(dictionary, f)
        
with open('saved_dictionary.pkl', 'rb') as f:
    loaded_dict = pickle.load(f)
```

# Discover Anemoi: Webinar Series

- <https://events.ecmwf.int/event/446/timetable/>

<iframe width="560" height="315" src="https://www.youtube.com/embed/AiO-GCIPcM4" title="Discover Anemoi: Introduction to Anemoi" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/S91TGYrKg00" title="Discover Anemoi: Anemoi Datasets" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/tIuyeQgGEG4" title="Discover Anemoi: Anemoi Graphs" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/hbzgAsxDMX4" title="Discover Anemoi: Anemoi Training" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/CEXqlpSuNPo" title="Discover Anemoi: Inference" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>



# References

1. [AIFS: a new ECMWF forecasting system | ECMWF | January 2024](https://www.ecmwf.int/en/newsletter/178/news/aifs-new-ecmwf-forecasting-system)
2. [Bringing together users, researchers and coders in ECMWF’s machine learning efforts | 15 May 2024](https://www.ecmwf.int/en/about/media-centre/news/2024/bringing-together-users-researchers-and-coders-ecmwfs-machine)
3. [Machine learning to play growing role in weather forecasting, says DG | ECMWF | 9 September 2024](https://www.ecmwf.int/en/about/media-centre/news/2024/machine-learning-play-growing-role-weather-forecasting-says-dg)
   1. Machine learning is well suited to weather forecasting due to the vast amounts of data that are used to determine the best possible initial conditions and to produce global forecasts.	
4. [Machine learning ensembles | ECMWF | October 2024](https://www.ecmwf.int/en/newsletter/181/editorial/machine-learning-ensembles)
5. [欧洲中期天气预报中心联合多国共推人工智能天气预报计划 | CMA](https://data.cma.cn/site/article/id/42435.html)
   1. 目前，Anemoi吸引了西班牙国家气象局、丹麦气象研究所、德国气象局、芬兰气象研究所、意大利空军气象局、荷兰皇家气象研究所、挪威气象局、法国气象局、瑞士气象局和比利时皇家气象研究所的参与。一些国家基于Anemoi在机器学习模型的创建上取得了进展，如挪威气象局为斯堪的纳维亚创建了区域模型，德国气象局正利用其全球数值预报模式(ICON)数据开发一个名为AICON的数据驱动天气预报模型。




