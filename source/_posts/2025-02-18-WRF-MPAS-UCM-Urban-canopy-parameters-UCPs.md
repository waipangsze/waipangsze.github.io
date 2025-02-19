---
layout: post
title: WRF | MPAS | UCM Urban canopy parameters (UCPs)
categories: [WRF]
tags: [WRF, MPAS, HPC, NWP, UCM, LCZs, UCPs]
author: wpsze
date: 2025-02-18 16:22:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/FqQXJNT.png
banner_img: https://i.imgur.com/FqQXJNT.png
---

# Urban canopy parameters (UCPs)

UCPs指的是都市地表參數，包括像地表粗糙度、植被覆蓋、建築密度等指標。這些參數在NWP模型中非常重要，因為它們會影響天氣模型對城市區域的天氣狀況模擬，例如溫度、濕度、風速等。

接下來，我得解釋UCPs如何影響NWP模擬。這可能包括以下幾個面向：地表粗糙度會影響大氣層的摩擦，進而影響風的傳播和速度。植被覆蓋會影響輻射的吸收，改變熱平衡，進而影響溫度分佈。建築密度和形狀可能影響熱島效應，導致溫度升高等。

此外，UCPs的準確性對模擬結果也很關鍵。如果UCPs的數據不準確或不完整，模型可能會低估或高估城市區域的某些天氣變量，導致預測誤差。

Urban canopy parameters (UCPs) are variables used in numerical weather prediction (NWP) models to describe the characteristics of the urban surface, such as roughness, vegetation, and building density. These parameters influence how the atmosphere interacts with urban areas, affecting weather phenomena like temperature, humidity, and wind. They help the models better simulate urban microclimates, which are crucial for accurate weather forecasting in cities.

# Literature Review 

Here are some papers and datasets related to **Global Urban Canopy Parameters (UCPs)**, particularly focusing on the **GloUCP** dataset:

1. **GloUCP: A Global 1 km Spatially Continuous Urban Canopy Parameters for the WRF Model**:
   - This paper introduces the GloUCP dataset, which provides a comprehensive global urban canopy parameter dataset at a 1 km resolution. The dataset includes building heights, footprints, and other urban morphology parameters essential for improving urban climate simulations. It highlights the dataset's superior accuracy and spatial coverage compared to existing datasets, especially in key urban regions like the Guangdong-Hong Kong-Macao Greater Bay Area.
   - Liao, Weilin, et al. "GloUCP: A global 1 km spatially continuous urban canopy parameters for the WRF model." Earth System Science Data Discussions **2024** (2024): 1-21.
   - [Read the paper here](https://essd.copernicus.org/preprints/essd-2024-408/) or [access the PDF](https://essd.copernicus.org/preprints/essd-2024-408/essd-2024-408.pdf).
   - The GloUCP dataset, converted to WRF binary file format, is available for download at <https://doi.org/10.6084/m9.figshare.27011491>

2. **GLObal Building Heights for Urban Studies (UT-GLOBUS)**:
   - This dataset includes building heights and urban canopy parameters for over 1,200 cities worldwide. It utilizes machine learning models combined with spaceborne altimetry data to estimate building-level information. The dataset is designed for modeling applications and includes UCPs in a format compatible with WRF.
   - Kamath, Harsh G., et al. "GLObal Building heights for Urban Studies (UT-GLOBUS) for city-and street-scale urban simulations: development and first applications." Scientific Data 11.1 (**2024**): 886.
   - More details can be found [here](https://gee-community-catalog.org/projects/utglobus/).
   - Earth Engine App: <https://sat-io.earthengine.app/view/ut-globus> 

3. **High Resolution 1m Global Canopy Height Maps**:
   - Although primarily focused on tree canopy heights, this dataset provides insights into vegetation structure that can complement urban canopy studies. It offers detailed information on tree canopy presence and height derived from satellite imagery.
   - [Access the dataset](https://gee-community-catalog.org/projects/meta_trees/).

4. **Global 30m Landsat Tree Canopy Version 4**:
   - This version of the tree canopy cover product offers improved estimates of tree cover globally, which may be useful in conjunction with UCPs for understanding urban vegetation dynamics.
   - [More information is available](https://lcluc.umd.edu/metadata/global-30m-landsat-tree-canopy-version-4).

---