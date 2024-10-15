---
layout: post
title: Global map of Local Climate Zones (LCZ) 
categories: [NWP]
tags: [WRF, MPAS, Urban, UCM]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/5tUhQAG.png
banner_img: https://i.imgur.com/5tUhQAG.png 
---

# Global map of Local Climate Zones

- <https://zenodo.org/records/8419340>

## Description

![A global 100 m spatial resolution Local Climate Zone (LCZ) map](https://i.imgur.com/46uEI6Z.png)

A global 100 m spatial resolution Local Climate Zone (LCZ) map, derived from multiple earth observation datasets and expert LCZ class labels.

The LCZ map is based on the LCZ typology (Stewart and Oke, 2012) that distinguish urban surfaces accounting for their typical combination of micro-scale land-covers and associated physical properties. The LCZ scheme is distinguished from other land use / land cover schemes by its focus on urban and rural landscape types, which can be described by any of the 17 classes in the LCZ scheme.

Out of the 17 LCZ classes, 10 reflect the 'built' environment, and each LCZ type is associated with generic numerical descriptions of key urban canopy parameters critical to model atmospheric responses to urbanisation. In addition, since LCZs were originally designed as a new framework for urban heat island studies (Stewart and Oke, 2012), they also contain a limited set (7) of 'natural' land-cover classes that can be used as 'control' or 'natural reference' areas. As these seven natural classes in the LCZ scheme can not capture the heterogeneity of the world’s existing natural ecosystems, we advise users - if required - to combine the built LCZ classes with any other land-cover product that provides a wider range of natural land-cover classes.

- Stewart ID, Oke TR. (2012). Local Climate Zones for Urban Temperature Studies. Bull Am Meteorol Soc. 93(12):1879-1900. doi:10.1175/BAMS-D-11-00019.1

## Versions

If there are there multiple versions of the dataset, list the file updated, when and why update was made:

- _v1: initial release of the data (20220222)
- _v2: add LCZ map of Iceland, consistent with the global LCZ map procedure (20221115)
- _v3: Mosaicing two Global Forest Canopy Height products two cover remote areas (eg. islands) (20231008)

## Files and brief description

Files and brief description:

1. **lcz_filter_v3.tif**

{% note primary %}
The recommended global LCZ map, compressed as GeoTIFF, with LCZ classes indicated by numbers 1-17
{% endnote %}

**The recommended global LCZ map, compressed as GeoTIFF, with LCZ classes indicated by numbers 1-17**. LCZ labels are obtained after applying the morphological Gaussian filter described in Demuzere et al. (2020). The official color scheme - as indicated in the table below - is embedded into the GeoTIFF.

![Official class description](https://i.imgur.com/pRByENm.jpeg)

The table below describes,

| LCZ class number  | Official class description  | Official hex color |
|:------------- |:---------------:|:-------------|
LCZ 1		|	Compact highrise	|	'#910613'	
LCZ 2		|	Compact midrise		|	'#D9081C'
LCZ 3		|	Compact lowrise		|	'#FF0A22'
LCZ 4		|	Open highrise		|	'#C54F1E'
LCZ 5		|	Open midrise		|	'#FF6628'
LCZ 6		|	Open lowrise		|	'#FF985E'
LCZ 7		|	Lightweight low-rise	|	'#FDED3F'
LCZ 8		|	Large lowrise		|	'#BBBBBB'
LCZ 9		|	Sparsely built		|	'#FFCBAB'
LCZ 10		|	Heavy Industry		|	'#565656'
LCZ 11 (A)	|	Dense trees		|	'#006A18'
LCZ 12 (B)	|	Scattered trees		|	'#00A926'
LCZ 13 (C)	|	Bush, scrub		|	'#628432'
LCZ 14 (D)	|	Low plants		|	'#B5DA7F'
LCZ 15 (E)	|	Bare rock or paved	|	'#000000'
LCZ 16 (F)	|	Bare soil or sand	|	'#FCF7B1'
LCZ 17 (G)	|	Water			|	'#656BFA'

	
Other characteristics:
- Projection: World Geodetic System 1984 (EPSG:4326)
- Size: 389620, 155995
- Spatial Resolution: 0.000898° (~ 100 m)
- Representative for the nominal year of 2018


1. lcz_v3.tif

Same as 1., but presenting the raw LCZ map, **before applying the morphological Gaussian filter.**


3. lcz_probability_v3.tif

A probability layer (%) that identifies how often the modal LCZ was chosen per pixel (e.g. a probability of 60% means that the modal LCZ class was mapped 30 times out of 50 LCZ models). This is a pixel-based probability, derived from the lcz_v3.tif map.

# Convert tif to netcdf

GDAL is a translator library for raster and vector geospatial data formats that is released under an MIT style Open Source License by the Open Source Geospatial Foundation

https://gdal.org/ 


```sh
micromamba env create -n gdal
micromamba install -c conda-forge gdal=3.6.1
```
and then,

```sh
whereis gdal_translate

# for checking
gdal_translate --long-usage

gdal_translate -of netCDF -co "FOMRAT=NC4" lcz_filter_v3.tif lcz_filter_v3.nc 
```

The detail of file,

```text
netcdf lcz_filter_v3 {
dimensions:
	lon = 389620 ;
	lat = 155995 ;
variables:
	char crs ;
		crs:grid_mapping_name = "latitude_longitude" ;
		crs:long_name = "CRS definition" ;
		crs:longitude_of_prime_meridian = 0. ;
		crs:semi_major_axis = 6378137. ;
		crs:inverse_flattening = 298.257223563 ;
		crs:spatial_ref = "GEOGCS[\"WGS 84\",DATUM[\"WGS_1984\",SPHEROID[\"WGS 84\",6378137,298.257223563,AUTHORITY[\"EPSG\",\"7030\"]],AUTHORITY[\"EPSG\",\"6326\"]],PRIMEM[\"Greenwich\",0,AUTHORITY[\"EPSG\",\"8901\"]],UNIT[\"degree\",0.0174532925199433,AUTHORITY[\"EPSG\",\"9122\"]],AXIS[\"Latitude\",NORTH],AXIS[\"Longitude\",EAST],AUTHORITY[\"EPSG\",\"4326\"]]" ;
		crs:crs_wkt = "GEOGCS[\"WGS 84\",DATUM[\"WGS_1984\",SPHEROID[\"WGS 84\",6378137,298.257223563,AUTHORITY[\"EPSG\",\"7030\"]],AUTHORITY[\"EPSG\",\"6326\"]],PRIMEM[\"Greenwich\",0,AUTHORITY[\"EPSG\",\"8901\"]],UNIT[\"degree\",0.0174532925199433,AUTHORITY[\"EPSG\",\"9122\"]],AXIS[\"Latitude\",NORTH],AXIS[\"Longitude\",EAST],AUTHORITY[\"EPSG\",\"4326\"]]" ;
		crs:GeoTransform = "-170.0007776279147 0.000898315284119518 0 80.03809518448114 0 -0.000898315284119518 " ;
	double lat(lat) ;
		lat:standard_name = "latitude" ;
		lat:long_name = "latitude" ;
		lat:units = "degrees_north" ;
	double lon(lon) ;
		lon:standard_name = "longitude" ;
		lon:long_name = "longitude" ;
		lon:units = "degrees_east" ;
	byte Band1(lat, lon) ;
		Band1:long_name = "GDAL Band Number 1" ;
		Band1:_Unsigned = "true" ;
		Band1:valid_range = 0s, 255s ;
		Band1:_FillValue = 0b ;
		Band1:grid_mapping = "crs" ;

// global attributes:
		:GDAL_AREA_OR_POINT = "Area" ;
		:Conventions = "CF-1.5" ;
		:GDAL = "GDAL 3.6.1, released 2022/12/14" ;
		:history = "Mon Oct 07 12:05:03 2024: GDAL CreateCopy( lcz_filter_v3.nc, ... )" ;
}
```

# Part of LCZ dataset

Global tiff is large, and if we only consider a specific area like GBA/Tokyo/Houston.

Fast and easy Local Climate Zone mapping

https://lcz-generator.rub.de/

- China, People's Republic of - Hong Kong-Macau-Guangdong Greater Bay Area:
  - https://lcz-generator.rub.de/factsheets/c672ca892c88d1e078a81e7a085913396172f81c/c672ca892c88d1e078a81e7a085913396172f81c_factsheet.html 
- Japan - Tokyo:
  - https://lcz-generator.rub.de/factsheets/67a3371158ad3ac12d720fde089622c259e8e400/67a3371158ad3ac12d720fde089622c259e8e400_factsheet.html 
- United States of America - Houston: 
  - https://lcz-generator.rub.de/factsheets/037c80744fc1034b8472f594d829c53ce1cccfa7/037c80744fc1034b8472f594d829c53ce1cccfa7_factsheet.html 


![]()

{% gi 3 1-2 %}
  ![GBA](https://i.imgur.com/4pyAoGS.jpeg)
  ![Tokyo](https://i.imgur.com/rO8Cz9d.jpeg)
  ![Houston](https://i.imgur.com/b3plh7O.jpeg)
{% endgi %}

![ncview](https://i.imgur.com/lGIE5ba.png)


# References

1. Demuzere, M., Kittner, J., & Bechtel, B. (2021). LCZ Generator: a web application to create Local Climate Zone maps. Frontiers in Environmental Science, 9, 637455.
2. Zhao, N., Ma, A., Zhong, Y., Zhao, J., & Cao, L. (2019). Self-training classification framework with spatial-contextual information for local climate zones. Remote Sensing, 11(23), 2828.
3. 杨俊, 任嘉义, 于文博. 局地气候分区视角下城市气候与人居环境研究进展. 生态学报, 2024, 44(11): 4489-4506.
   1. 了解人居环境中的气候问题是主动改善和提升的首要条件。这在传统研究中已有大量成果, 如基于“城-郊”二元框架计算城市热岛强度并探讨城郊热力差异[4—6]；结合土地利用/土地覆被分析城市热力景观时空特征[7—9]；分析长时间序列下极端天气、热浪事件发生频率和持续时间[10—12]；探究空气污染与城市功能区的耦合关系[13—15]；以及定量评价气候变化导致的热风险和生态风险等[16—18]。研究普遍以城市行政边界或人口梯度为基准, 忽视了相似地理单元的空间特性。2012年Stewart和Oke提出将局地气候分区(Local Climate Zones, LCZ)作为城市气候研究的通用框架[19], 填补了传统气候分类在中/微观尺度研究中的局限, 得到广泛运用。
   2. 局地气候分区根据城市形态、地表覆盖、地表结构及材质等差异, 将城市下垫面划分为17个基本区域, 包括10个建筑类型和7个自然类型。每个LCZ都有特定范围的相关气候参数, 不同LCZ类型具有不同的局部气候条件(图 1)。LCZ分类体系提出用两个LCZ之间的温差定义并计算城市热岛强度[19—20], 克服了传统研究中城乡的刻板边界。同时建立了城市气候研究基础框架, 规范了全球城市气候研究范式。目前世界各地研究绘制了丰富的LCZ地图, 本研究将对往期文献中LCZ地图制作数据源、方法和已有LCZ数据集进行全面梳理。
4. 许亘昱, 石玉蓉, & 张宇峰. (2023). 规划控制要素视角下城市热环境的测度与优化——以广州中心城区为例. 热带地理, 43(6), 1070-1082.
   1. 城市下垫面类别和土地开发强度的多样性造成了城市内部热岛强度的差异。Stewart等（2012）在充分论证传统城郊二元法研究城市热岛问题的不足后，提出了局地气候分区（Local Climate Zone，下文简称LCZ）体系。LCZ将城市下垫面分为10种建成类型和7种自然类型（图4），以天空角系数、高宽比、建筑密度、不透水表面积占比、透水表面积占比、粗糙元高度、地形粗糙类型、热导纳、反射率和人为热等的区间为属性。LCZ用定量方法区分城市的土地构成，相较于传统规划领域方法—从土地利用功能角度出发的定性分类方法，其能更好地体现功能相同的土地利用类型在地块的建筑密度、容积率和绿地率等方面的差异，且具有分析比较的普适性（Bechtel et al., 2015），以LCZ为基础的城市热岛强度分类已通过经验和模型得到证实（Fenner et al., 2014; Stewart et al., 2014; Leconte et al., 2015）。
