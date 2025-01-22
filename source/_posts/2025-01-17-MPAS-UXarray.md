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