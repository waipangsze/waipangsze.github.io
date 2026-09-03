---
layout: post
title: MPAS | UXarray
categories: [MPAS]
tags: [MPAS, UXarray]
author: wpsze
date: 2025-01-17 11:25:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/EWzShPE.png
banner_img: https://i.imgur.com/EWzShPE.png
---

# UXarray

Xarray extension for **unstructured climate and global weather data analysis and visualization** written around the UGRID conventions.

在 GitHub 的 [UXARRAY/uxarray](https://github.com/UXARRAY/uxarray) 專案及其核心生態系中，針對非結構化網格（Unstructured Grid）的互動式視覺化渲染（Interactive Rendering），主要透過繼承與整合 HoloViz 生態系（尤其是 hvPlot、Holoviews 與 Datashader）來實現。 

以下根據 GitHub 官方庫、說明文件與 [Project Pythia 視覺化指南](https://github.com/ProjectPythia/unstructured-grid-viz-cookbook) 整理的核心架構、程式碼範例與技術重點： 

## 一、 核心渲染架構
UXarray 透過建立基於 `UGRID` 規範的統一拓撲結構，讓以往難以直接處理的非結構化數據（如 **MPAS** 、ICON、CAM-SE 等網格），在**不需要插值回結構化經緯度網格（No Regridding）**的前提下進行直接渲染： 

* uxarray.plot 模組：提供與 Xarray/hvPlot 風格高度一致的 API。
* 資料流向：UXarray 物件抽取拓撲與連接性（Connectivity）轉換成 HoloViews 的多邊形（Polygons）或網格對象（QuadMesh）。
* 動態動態重採樣：結合 Datashader。當網格節點達到百萬或千萬級別時，透過 Datashader 進行後端畫素級別的動態聚合，保存在瀏覽器中互動（放大、縮小、平移）時不會崩潰。

## 二、 互動式渲染：快速起步代碼
要在 **Jupyter Notebook** 中實現互動式渲染，請參考以下標準的工作流程範例：

```python
import xarray as xrimport uxarray as uximport hvplot.uxarray  # 載入 UXarray 的 hvPlot 互動式擴充套件
# 1. 讀取非結構化數據與網格檔案 (以 MPAS 網格為例)# UXarray 會自動將其識別並轉為符合 UGRID 規範的拓撲ux_ds = ux.open_dataset("grid_mesh.nc", "data_variables.nc")
# 2. 選擇你要互動檢視的變數 (例如：表面溫度 Tair)# 假設資料含有時間維度，我們篩選特定時間點data_slice = ux_ds["Tair"].isel(time=0)
# 3. 呼叫互動式渲染介面 (使用聚合成的多邊形繪製)# 這會返回一個 HoloViews/hvPlot 的互動式圖表，內建放大、平移、Hover 提示工具interactive_plot = data_slice.plot(
    engine="hvplot", 
    backend="bokeh", 
    cmap="viridis", 
    colorbar=True
)
# 4. 在 Jupyter 中直接顯示
interactive_plot
```

## 三、 關鍵互動渲染方法與參數

根據 [UXarray 繪圖使用者指南](https://uxarray.readthedocs.io/en/v2024.07.1/user-guide/plotting.html)，你可以控制以下兩種核心的圖形呈現： [1] 

## Why?

UXarray aims to address the geoscience community need for tools that enable standard data analysis techniques to operate directly on unstructured grids. It extends upon and inherits from the commonly used Xarray Python package to provide a powerful and familiar interface for working with unstructured grids in Python. UXarray provides Xarray styled functions to better read in and use unstructured grid datasets that follow standard conventions, including UGRID, MPAS, SCRIP, and Exodus formats.

The “U” in UXarray stands for “Unstructured Grids”. 

- <https://uxarray.readthedocs.io/en/latest/>

# Installation

```console
micromamba env create -n uxarray
micromamba activate uxarray
micromamba install conda-forge::uxarray==2024.11.1
micromamba install scipy
micromamba install conda-forge::netcdf4
micromamba install conda-forge::wrf-python
micromamba install conda-forge::cartopy
micromamba install conda-forge::basemap
```

where `uxarray==2024.11.1` can be changed. 

## Jupyter Notebook

Use Jupyter Notebook instead of JupyterLab, as the latter does not display the variable mesh correctly when zoomed in.

- trick
  - convert JupyterLab to Jupyter Notebook on broswer
  - on http path, remove `tree` and enter.

# Plot: Simple Start

```JupyterNotebook
import uxarray as ux
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import colors

file_dir="/home/wpsze/MPAS-A/meshes/x1.40962_120km/"

grid_path = f"{file_dir}/static.nc"
data_path = f"{file_dir}/static.nc"
print(grid_path)
print(data_path)
uxds = ux.open_dataset(grid_path, data_path)

# Plot ivgtyp
uxds["ivgtyp"].plot(coastline="10m", cmap="jet", clim=(1, 20), dynamic=True)

# make a color map of fixed colors
cmap = colors.ListedColormap(['white', 'green', 'red', 'blue', 'white'])
uxds["ivgtyp"].plot(coastline="10m", cmap=cmap, clim=(11, 15), dynamic=True)

# wind speed from u10 and v10
tmp = uxds["u10"]
tmp["u10"] = np.sqrt(uxds["u10"]**2 + uxds["v10"]**2)
tmin, tmax = int(tmp["u10"].min().values), int(tmp["u10"].max().values)
tmp["u10"].isel(Time=0).plot(coastline="10m", cmap="jet", clim=(tmin, tmax), dynamic=True)
```

{% gi 5 2-2-1 %}
![](https://i.imgur.com/JuNIoOk.png)
![](https://i.imgur.com/qV0CrXV.png)
![](https://i.imgur.com/EWzShPE.png)
![](https://i.imgur.com/WjlzFsd.png)
{% endgi %}

# Plot: Example

- The Hover Tooltip: When you point your mouse cursor over any cell in the interactive map, the popup bubble will dynamically compute and print the exact geographical location along with your real cell identification 
  - tag:text
    - Longitude: -105.27
    - Latitude: 40.01
    - indexToCellID: 481052

```python
#--- jupyter notebook
import uxarray as ux
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import colors
from bokeh.models import HoverTool
import holoviews as hv
from bokeh.models import HoverTool

import os
import pyproj
# Automatically finds and points PROJ to the correct database directory
os.environ["PROJ_LIB"] = pyproj.datadir.get_data_dir()

grid_path = f"./static.nc"
data_path = f"./static.nc"
print(grid_path)
print(data_path)

uxds = ux.open_dataset(grid_path, data_path)

uxds

#====================== Check ==================================
# 1. 检查数据变量
print("Data variables:", list(uxds.data_vars))

# 2. 检查网格属性
grid = uxds.uxgrid
print("Grid attributes:", dir(grid))

# 3. 查看网格节点坐标
print("Node coordinates shape:", grid.node_lon.shape)
print("First 5 nodes:", grid.node_lon[:5], grid.node_lat[:5])

# 4. 检查是否有indexToCellID
if hasattr(grid, 'indexToCellID'):
    
    print("indexToCellID exists:", grid.indexToCellID[:10])
else:
    print("indexToCellID not found. Available:", [attr for attr in dir(grid) if not attr.startswith('_')])

#====================== 可用字段 ==================================
# 绘制多边形图
temp_plot = uxds["ivgtyp"].plot.polygons(backend="bokeh")

# HoloViews对象的正确访问方式
# 方法1: 转换为Bokeh对象后获取
bokeh_plot = hv.render(temp_plot, backend='bokeh')
renderer = bokeh_plot.renderers[0]
source = renderer.data_source
print("可用字段:", source.column_names)
# 可用字段: ['x', 'y', 'dw', 'dh', 'image']

#======================= New method ==================================
#===================================== Renew ================================================
data_var = uxds["ivgtyp"]
data_var = data_var.assign_coords(
    isltyp=(data_var.dims, uxds["isltyp"].values),
)
data_var

interactive_map = data_var.plot.polygons(
    backend="bokeh",
    cmap="jet",
    colorbar=True,
    rasterize=False, # 關鍵 1：強行排除 rasterize 引擎（不壓縮為圖片，保留向量多邊形屬性）
    width=800,
    height=500,
    periodic_elements="exclude"  # 避免跨越換日線時拉伸變形
)

# 1. Print all available column keys that Bokeh/HoloViews sent to the canvas
print("Available hover names:", interactive_map.vdims)

# 2. Alternatively, drill directly into the underlying dictionary structure:
# This extracts the exact pandas/spatialpandas data columns
print("Data columns:", interactive_map.data.columns.tolist())

# Available hover names: [Dimension('ivgtyp')]
# Data columns: ['geometry', 'ivgtyp']

#===================================== Renew ================================================
# 方法一：直接修改 GeoDataFrame 容器（最推薦、最穩健
# 3. 核心黑魔法：直接對已經生成的圖表容器「強制注入」欄位數據
# 這樣做能繞過 UXarray 所有的過濾與重構機制
total_cells = len(interactive_map.data)  # 獲取實際繪製的多邊形數量
interactive_map.data["indexToCellID"] = uxds["indexToCellID"].values[:total_cells]
interactive_map.data["isltyp"] = uxds["isltyp"].values[:total_cells] # 安全截取長度

# 4. 手動覆寫圖表的 vdims (視覺維度)，告訴 Bokeh 這些新欄位合法可用
from holoviews import Dimension
interactive_map.vdims.append(Dimension("indexToCellID"))
interactive_map.vdims.append(Dimension("isltyp"))

# 5. 重新配置 HoverTool 並載入到圖表中
hover = HoverTool(
    tooltips=[
        ("Cell ID", "@indexToCellID"),
        ("Veg Type (ivgtyp)", "@ivgtyp"),
        ("Soil Type (isltyp)", "@isltyp"),
        ("Lon/Lat", "$x, $y")
    ]
)
interactive_map = interactive_map.opts(tools=[hover])

# 6. 再次驗證，此時這兩個欄位絕對會出現在清單中！
print("")
print("After renew ......")
print("Available hover names:", interactive_map.vdims)
print("Data columns:", interactive_map.data.columns.tolist())

# After renew ......
# Available hover names: [Dimension('ivgtyp'), Dimension('indexToCellID'), Dimension('isltyp')]
# Data columns: ['geometry', 'ivgtyp', 'indexToCellID', 'isltyp']

# 7. 顯示地圖
interactive_map
#===================================== Renew ================================================
```

![](https://i.imgur.com/8qCybVq.png)

- `可用字段: ['x', 'y', 'dw', 'dh', 'image']`
  - 看到的可用欄位只有 ['x', 'y', 'dw', 'dh', 'image']，這是一個非常關鍵的線索！這代表 UXarray 目前在底層自動啟用了 `rasterize=True`（動態點陣化/Datashader 降採樣）。
  - `rasterize=False`：強制停用影像像素壓縮。此時哪怕是全球全網格， `UXarray` 都會將這 36,002（或更多）個儲存格全部作為獨立多邊形物件送進網頁網格。這是能解除 `['x', 'y', 'dw', 'dh', 'image']` 的唯一方法。
  - 為什麼會這樣？
    - **當非結構化網格資料較大時，UXarray 為了防止瀏覽器卡死**，繪圖引擎會自動調用 Datashader。Datashader 的運作原理是把地圖上的幾何多邊形（Polygons）轉化為一張動態縮放的點陣圖片（Image）傳給瀏覽器。在這種模式下，Bokeh 看到的不再是獨立的儲存格，而是一整張圖片。因此，網格的真實座標、Cell ID 和附加變數都已經從地圖容器中消失了，只剩下圖片的邊界屬性：x、y（位置）、dw、dh（圖片寬高）和 image（像素色彩值）。
  - 如果您必須看到精確的 **Cell ID、ivgtyp 和 isltyp**，您需要強行**關閉點陣化（rasterize）**，改為純多邊形（Vector Polygons）渲染。此外，最新版 `hvPlot` 提供了一個專屬參數 `hover_tooltips` ，可以直接定義氣泡，不再需要手動實例化 `Bokeh` 的 `HoverTool。`
- `Data columns: ['geometry', 'ivgtyp']`
  - 當 `print(interactive_map.vdims)` 只顯示 `[Dimension('ivgtyp')]` 且 `Data columns` 只剩下 `['geometry', 'ivgtyp']` 時，代表：在**多邊形幾何重構**的過程中，我們之前使用 `assign_coords` 所指派的副座標（ `indexToCellID` 和 `isltyp` ）在轉換為多邊形時，被後端的幾何對齊引擎無情地濾除了。
  - 儘管我們關閉了 rasterize=False 並使用了 assign_coords，UXarray 的多邊形重構器（Polygons Reconstructer）在後端將網格拓撲結構轉換為向量多邊形時，依然會強制過濾並重構資料，導致所有的 Xarray coords 與 vdims 欄位直接丟失。
  - **方法一：直接修改 GeoDataFrame 容器（最推薦、最穩健**）
    - 由於 data_var.plot.polygons() 回傳的是一個 HoloViews 物件，在 rasterize=False 模式下，它的 .data 本質上是一個 GeoPandas 或 SpatialPandas 的 DataFrame 表格。我們不要在繪圖「前」塞座標，而是在繪圖「當下/後」直接強行把 indexToCellID 和 isltyp 寫入它的底層資料容器中。

## Error

- `ERROR 1: PROJ: proj_create_from_database: Open of /uxarray/share/proj failed`
  - This error occurs because the `GDAL/PROJ` geospatial libraries cannot locate the mandatory `proj.db` database file at the hardcoded or environment-specified path `/uxarray/share/proj`. This usually happens when utilizing geospatial tools inside isolated environments (like Conda or Docker) without properly initializing the underlying library paths.

```sh
import uxarray as ux
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import colors

import os
import pyproj
# Automatically finds and points PROJ to the correct database directory
os.environ["PROJ_LIB"] = pyproj.datadir.get_data_dir()
```

# Plot: subset of map 切片

- `ux_ds.uxgrid.subset.bounding_box`：這是 `UXarray` 核心提供的非結構化網格專用切片工具。它在切下特定地理方框的同時，會重新計算並重構網格的拓撲連接性（Connectivity Matrix），回傳一個全新的、自適應變小且完全對齊的 UxDataset 物件。
- 由於資料變數與網格同步縮小，不再有 {0, 36002} 的維度衝突，並且總儲存格數量顯著下降，此時開起 `rasterize=False` 能兼顧「不卡頓」與「彈出精確 Cell ID 與多變數氣泡」兩大核心需求。

```python
# jupyter notebook
import uxarray as ux
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import colors
from bokeh.models import HoverTool
import holoviews as hv
from bokeh.models import HoverTool

import os
import pyproj
# Automatically finds and points PROJ to the correct database directory
os.environ["PROJ_LIB"] = pyproj.datadir.get_data_dir()

grid_path = f"./static.nc"
data_path = f"./static.nc"
print(grid_path)
print(data_path)

uxds_main = ux.open_dataset(grid_path, data_path)

# 2. 進行空間切片（經緯度範圍）
lon_bounds = (110.0, 130.0)
lat_bounds = (20.0, 40.0)

# AttributeError: 'UxDataset' object has no attribute 'subset'
# 根據 UXarray 官方文件 API 規範，.subset 存取器（Accessor）只存在於 Grid 物件（ux_ds.uxgrid）以及 
# UxDataArray（單一變數）物件上，整個 UxDataset 物件本身確實沒有這個屬性。
# 最完美且符合官方範例的解法是：直接對單一主變數 ux_ds["ivgtyp"] 呼叫 .subset.bounding_box。
uxds = uxds_main["ivgtyp"].subset.bounding_box(
    lon_bounds=lon_bounds, 
    lat_bounds=lat_bounds
)

uxds

# 2. 检查网格属性
grid = uxds.uxgrid
print("Grid attributes:", dir(grid))

# 3. 查看网格节点坐标
print("Node coordinates shape:", grid.node_lon.shape)
print("First 5 nodes:", grid.node_lon[:5], grid.node_lat[:5])

# 4. 检查是否有indexToCellID
if hasattr(grid, 'indexToCellID'):
    
    print("indexToCellID exists:", grid.indexToCellID[:10])
else:
    print("indexToCellID not found. Available:", [attr for attr in dir(grid) if not attr.startswith('_')])


interactive_map = data_var.plot.polygons(
    backend="bokeh",
    cmap="jet",
    colorbar=True,
    rasterize=False, # 關鍵 1：強行排除 rasterize 引擎（不壓縮為圖片，保留向量多邊形屬性）
    width=800,
    height=500,
    periodic_elements="exclude"  # 避免跨越換日線時拉伸變形
)

# 2. Print all available column keys that Bokeh/HoloViews sent to the canvas
print("Available hover names:", interactive_map.vdims)

# 3. Alternatively, drill directly into the underlying dictionary structure:
# This extracts the exact pandas/spatialpandas data columns
print("Data columns:", interactive_map.data.columns.tolist())

#===================================== Renew ================================================
uxds_isltyp = uxds_main["isltyp"].subset.bounding_box(
    lon_bounds=lon_bounds, 
    lat_bounds=lat_bounds
)

uxds_indexToCellID = uxds_main["indexToCellID"].subset.bounding_box(
    lon_bounds=lon_bounds, 
    lat_bounds=lat_bounds
)

# 3. 核心黑魔法：直接對已經生成的圖表容器「強制注入」欄位數據
# 這樣做能繞過 UXarray 所有的過濾與重構機制
total_cells = len(interactive_map.data)  # 獲取實際繪製的多邊形數量
interactive_map.data["indexToCellID"] = uxds_indexToCellID.values[:total_cells]
interactive_map.data["isltyp"] = uxds_isltyp.values[:total_cells] # 安全截取長度

# 4. 手動覆寫圖表的 vdims (視覺維度)，告訴 Bokeh 這些新欄位合法可用
from holoviews import Dimension
interactive_map.vdims.append(Dimension("indexToCellID"))
interactive_map.vdims.append(Dimension("isltyp"))

# 5. 重新配置 HoverTool 並載入到圖表中
hover = HoverTool(
    tooltips=[
        ("Cell ID", "@indexToCellID"),
        ("Veg Type (ivgtyp)", "@ivgtyp"),
        ("Soil Type (isltyp)", "@isltyp"),
        ("Lon/Lat", "$x, $y")
    ]
)
interactive_map = interactive_map.opts(tools=[hover])

# 6. 再次驗證，此時這兩個欄位絕對會出現在清單中！
print("")
print("After renew ......")
print("Available hover names:", interactive_map.vdims)
print("Data columns:", interactive_map.data.columns.tolist())

# 7. 顯示地圖
interactive_map
```

![](https://i.imgur.com/7mdsanw.png)

# Plot vorticity_500hPa

- **vorticity_500hPa**
  - **dimensions="nVertices Time"**

```xml
<var name="vorticity_500hPa" type="real" dimensions="nVertices Time" units="s^{-1}"
      description="Relative vorticity vertically interpolated to 500 hPa"/>
```

```python
uxds["vorticity_500hPa"].plot(backend='matplotlib', rasterize=True)
```

# Grid Topology Visualization

- take a bit longer time to process

```JupyterNotebook
uxgrid = uxds.uxgrid
uxgrid.plot(title="Grid Plot Accessor")
uxgrid.plot(coastline="10m", title="Grid Plot Accessor")
uxgrid.plot.edges(color="black", title="Grid Edge Plot")

(
    uxgrid.plot.edges(color="black")
    * uxgrid.plot.nodes(marker="o", size=150).relabel("Corner Nodes")
    * uxgrid.plot.face_centers(marker="s", size=150).relabel("Face Centers")
    * uxgrid.plot.edge_centers(marker="^", size=150).relabel("Edge Centers")
).opts(title="Grid Coordinates", legend_position="top_right")
```

{% gi 5 2-2-1 %}
![](https://i.imgur.com/GatMAmn.png)
![](https://i.imgur.com/AOoyabH.png)
![](https://i.imgur.com/7QJkxL7.png)
![](https://i.imgur.com/tJahcAo.png)
{% endgi %}

# Geographic Projections & Features

```JupyterNotebook
import cartopy.crs as ccrs
import geoviews.feature as gf

central_longitude = 114

uxds["ivgtyp"].plot.polygons(
    rasterize=True,
    projection=ccrs.Orthographic(central_longitude=central_longitude),
    cmap=ux.cmaps.sequential_blue,
    title="Projected Polygon Plot (Centered about 114 degrees longitude)",
) * gf.coastline(projection=ccrs.Orthographic(central_longitude=central_longitude))
```

![](https://i.imgur.com/a5RvPaz.png){width=500}