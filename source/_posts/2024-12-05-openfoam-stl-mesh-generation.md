---
layout: post
title: "CFD | OpenFOAM | STL file | 網格生成"
categories: [CFD]
tags: [OpenFOAM,HPC]
author: wpsze
date: 2024-12-05 16:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/fFT42Yk.png
banner_img: https://i.imgur.com/fFT42Yk.png
---

# OpenFOAM三維網格生成器

snappyHexMesh（SHM）是OpenFOAM軟體的三維結構化網格生成模組，功能比較強大，設定靈活，表面處理貼合功能非常強健並且可以並行運算。
SHM能夠透過使用者自訂參數（如細化參數castellatedMeshControls、切合參數snapControls等）對預先載入的幾何體檔案（通常為'.stl'檔案）自動實現三維網格生成、加密(castellatedMesh)、切合（snap ）和新增邊界層（addLayers）等操作。

SHM產生OpenFOAM網格流程如圖1所示:

![圖1 基於snappyHexMesh的OpenFOAM網格生成流程圖](https://i.imgur.com/oggvctr.png){width=600}

需提供以下三個文件：

1. STL檔案格式的幾何檔案（binary或ascii格式，儲存在 `constant/triSurface` 子字典中）；
2. 背景六面體網格（用於定義計算域範圍和背景網格，一般由 `blockMesh` 生成，也可由外部網格生成器生成）；
3. `snappyHexMeshDict` 字典（提供生成網格的必要信息，位於 `system` 子資料夾下）。

## 什麼是 STL 檔案？

STL 是一種常用於 3D 列印和電腦輔助設計 (CAD) 的檔案格式。縮寫名稱 STL 代表「光固化立體成型」(**Stereolithography**)，一種熱門的 3D 列印技術。此外，這種檔案也稱作「標準三角語言」(**Standard Triangle Language**) 或「標準曲面細分語言」(**Standard Tessellation Language**)。 STL 檔案是由一系列相連的三角形構成，這些三角形可用於描繪 3D 模型或物件的表面幾何圖形。設計越複雜，使用的三角形數量就越多，解析度也隨之提高。您可以透過副檔名 .stl 以及缺乏色彩與紋理的特點來辨識 STL 格式影像。

無論列印 3D 模型的目的是純粹好玩還是製作實務原型，您都必須建立 STL 檔案來儲存設計。請先在 CAD 軟體程式中繪製模型，然後將完成的設計以 STL 檔案格式匯出並儲存至電腦。

- [STL 檔案資料結構](https://vr.me.ncku.edu.tw/courses/cg110/sites/vr.me.ncku.edu.tw.courses.cg110/files/STL.pdf)
- [幾個常用的修改STL模型軟件](https://3dlabstore.com.hk/blog/3d-software/popular-stl-editiors?srsltid=AfmBOoqL1Z-brQRzPgIC-kvSjG7SpBiql9WLuug-r5hypwAlAFvT-CNE)
  - **MeshLab**
    - MeshLab是一個開源、便攜且可擴展的系統，用於處理和編輯3D三角形網格。這個系統於2005年年底發佈，旨在幫助用戶進行3D掃描、編輯、清理、修復、檢查、呈現和轉換等操作。MeshLab用途廣泛，它可以用於各種學術和研究中，如文化遺產、表面重建、古生物學等。涉及到3D網格模型處理的，都可以用這款軟件來處理。
  - MeshMixer
    - MeshMixer適合許多需要修改STL模型的使用者，初學者或是經驗豐富的設計人員皆適用。
  - ZBrushCoreMini
    - ZBrushCoreMini適合對雕刻方面感興趣的人群，此款軟件操作時類似使用畫筆一樣去編輯。

### 3D打印的STL和OBJ格式有什麼區別？修改起來一樣麼？

STL文件格式是由3D SYSTEMS 公司於1988 年制定的一種爲快速原型製造技術服務的三維圖形文件格式。STL文件不同於其他一些基於特徵的實體模型，STL用三角形網格來表現3D CAD模型，只能描述三維物體的幾何信息，不支持顏色材質等信息。正因爲數據簡化，格式簡單，STL普及很快應用廣泛。

OBJ文件是Alias|Wavefront公司爲它的一套基於工作站的3D建模和動畫軟件”Advanced Visualizer”開發的一種標準3D模型文件格式，很適合用於3D軟件模型之間的數據交換，能支持顏色等信息。

兩者在修改上相差不多，都是需要導入到相關的模型修改軟件中進行。

# Applications

- [SnappyHexMesh網格劃分實例詳解](https://ppfocus.com/0/ed40c27be.html)
  - 使用surfaceCheck檢查proeller.stl, `surfaceCheck constant/propeller.stl`

# Blender GIS to stl

- <https://github.com/domlysz/BlenderGIS>

{% note danger %}
GIS datafile import : Import in Blender most commons GIS data format : `Shapefile vector`, `raster image`, `geotiff DEM`, `OpenStreetMap xml`.
{% endnote %}

## Google GIS (SRTM 30m) to stl

- Note : Since 2022, the OpenTopography web service requires an API key. Please register to opentopography.org and request a key. This service is still free.

{% gi 5 3-2 %}
![1](https://i.imgur.com/s3CBmtM.png)
![2](https://i.imgur.com/Sgvffp8.png)
![3](https://i.imgur.com/1EBJ21x.png)
![4](https://i.imgur.com/Sk7Oeev.png)
![5](https://i.imgur.com/11gtygg.png)
{% endgi %}

## OpenStreetMap xml to stl

### Download osm file 

![](https://i.imgur.com/POleA9W.png){width=400}

### Import osm file

![](https://i.imgur.com/POvvxOu.png){width=400}

### export stl file

- meshlab

![Open by **meshlab**](https://i.imgur.com/bvsHeZV.png){width=400}

- admesh

![Check by **admesh**](https://i.imgur.com/SVTBUsh.png){width=400}

# OSM

**OpenStreetMap** 是一个庞大的地理信息数据库，而且是完全开放和免费的。

- <https://www.openstreetmap.org/#map=13/22.2868/114.2492>
- export --> `map.osm`

`.osm` 文件格式是 OpenStreetMap 所特有的。你不会在其他地方遇到它。如果你曾经使用 JOSM 下载过数据并将其保存为文件，你可能已经注意到文件的扩展名是 `.osm` 。如果你是一个GIS用户，你可能也注意到，使用QGIS等软件打开这些文件并不容易。

![](https://i.imgur.com/lqqINf9.png){width=600}

# Shapefile

**shapefile** 是一种广泛使用的矢量地理数据存储格式。它是由 ESRI 公司开发的，该公司制造了 ArcGIS ，这是一套流行的地理信息系统应用程序。

Shapefile 实际上是几个不同文件的集合。例如，一个包含建筑数据的shapefile可能有以下扩展名的文件。

- buildings.shp
- buildings.shx
- buildings.dbf

shapefiles 通常也会有包含其他信息的附加文件。

一个 shapefile 只能容纳一种类型的特征（点、线或多边形），每个特征的属性都包含在一个表中。与 OpenStreetMap 系统不同的是，在 OpenStreetMap 系统中，每个对象都可以拥有无限数量的标签，shapefile 中的特征属性必须适合 shapefile 定义的表格结构。

OpenStreetMap数据可以转换为shapefiles。各种网站提供了从OSM数据转换而来的shapefiles。

# References

1. [Mesh generation using snappyHexMesh (**recommend**)](https://www.wolfdynamics.com/wiki/meshing_OF_SHM.pdf)
2. [解锁新技能：OpenFOAM三维网格生成器](https://www.fangzhenxiu.com/post/7273888/?uri=473_83g6eo3r4s1)
3. [STL 檔案](https://www.adobe.com/hk_zh/creativecloud/file-types/image/vector/stl-file.html)