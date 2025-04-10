---
layout: post
title: NWP | Herbie | Download NWP model output (grib2) | MPAS | WRF
categories: NWP
tags: [NWP, WRF, MPAS, GFS, GEFS, IFS, AIFS, grib, grib2, WPS, ungrib]
author: wpsze
date: 2025-04-10 11:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

#  Herbie

Herbie is a python package that **downloads recent and archived numerical weather prediction (NWP) model output from different cloud archive sources**. NWP data is distributed in GRIB2 format which Herbie reads using xarray+cfgrib. Herbie also provides some extra features to help visualize and extract data.

- <https://pypi.org/project/herbie-data/>

Herbie helps you discover, download, and read data from:

- High Resolution Rapid Refresh (HRRR) | HRRR-Alaska
- Rapid Refresh (RAP)
- **Global Forecast System (GFS)**
- **Global Ensemble Forecast System (GEFS)**
- **ECMWF Open Data Forecasts (IFS and AIFS)**
- **Navy Global Environmental Model (NAVGEM)**
- North American Mesoscale Model (NAM)
- National Blend of Models (NBM)
- Rapid Refresh Forecast System (RRFS) prototype
- Real-Time/Un-Restricted Mesoscale Analysis (RTMA/URMA)
- Hurricane Analysis And Forecast System (HAFS)
- High Resolution Deterministic Prediction System (HRDPS)
- Climate Forecast System (CFS)

and more! Check out the [gallery](https://herbie.readthedocs.io/en/latest/gallery/index.html).

# Installation

```console
conda install -c conda-forge herbie-data
```

# Capabilities

- **Search for model output** from different data sources.
- Download **full GRIB2 files**.
- Download **subset GRIB2 files (by grib field)**.
- **Read data with xarray.**
- Read index file with Pandas.
- Extra features (herbie xarray accessors)
  - Extract data at a point
  - Get Cartopy coordinate references system
  - Plot data with Cartopy (very early development).

# Examples

```python
from herbie import Herbie

# Herbie object for the HRRR model 6-hr surface forecast product
H = Herbie(
  '2021-01-01 12:00',
  model='hrrr',
  product='sfc',
  fxx=6
)

# Look at file contents
H.inventory()

# Download the full GRIB2 file
H.download()

# Download a subset, like all fields at 500 mb
H.download(":500 mb")

# Read subset with xarray, like 2-m temperature.
H.xarray("TMP:2 m")
```

## IFS/AIFS

- <https://herbie.readthedocs.io/en/latest/gallery/ecmwf_models/ecmwf.html>

{% note primary %}
IFS data is only available at 0.4 degree prior to **February 1, 2024. After that date, the IFS is available at 0.25 degree resolution.**
{% endnote %}

ECMWF provides data for two different models

- **model="ifs"** ECMWF Integrated Forecast System
- **model="aifs"** ECMWF Artificial Intelligence Integrated Forecast System

| prioriy 	| Data source            	| Archive Duration      	|
|----------	|------------------------	|-----------------------	|
| "ecmwf"  	| ECMWF Open Data Server 	| last 4 days           	|
| "azure"  	| Microsoft Azure        	| 2022-01-21 to present 	|
| "aws"    	| Amazon Web Services    	| 2023-01-18 to present 	|

| product= 	| Product Description                                                                                 	| Available model runs 	|
|----------	|-----------------------------------------------------------------------------------------------------	|----------------------	|
| "oper"   	| operational high-resolution forecast, atmospheric fields                                            	| 00z, 12z,            	|
| "wave"   	| wave forecasts                                                                                      	| 00z, 12z,            	|
| "scda"   	| short cut-off high-resolution forecast, atmospheric fields (also known a high-frequency products)”, 	| 06z, 18z             	|
| "scwv"   	| short cut-off high-resolution forecast, ocean wave fields (also known a high-frequency products)”,  	| 06z, 18z             	|
| "enfo"   	| ensemble forecast, atmospheric fields                                                               	| 00z, 06z, 12z, 18z   	|
| "waef"   	| ensemble forecast, ocean wave fields,                                                               	| 00z, 06z, 12z, 18z   	|
| "mmsf"   	| multi-model seasonal forecasts fields from the ECMWF model only.                                    	| ?                    	|

```python
from herbie import Herbie

import cartopy.crs as ccrs
import matplotlib.pyplot as plt
import numpy as np

from herbie import paint
from herbie.toolbox import EasyMap, pc

H = Herbie("2024-03-1", model="ifs", product="oper", fxx=12)
H.grib, H.idx

# Show the inventory
H.inventory()
H.inventory().param.unique()

# Show just 10-m U and V wind
H.inventory(":10[u|v]:")

# Download the full GRIB2 file
H.download(save_dir="./")

# Get 2-m temperature as an xarray Dataset
ds = H.xarray(":2t:", verbose=True)
ds
ds.t2m.plot()
```

![](https://i.imgur.com/o1xSqZN.png){width=400}

## GFS

- <https://herbie.readthedocs.io/en/latest/gallery/noaa_models/gfs.html>
  
```python
from herbie import Herbie
from herbie.toolbox import EasyMap, pc, ccrs
from herbie import paint

import matplotlib.pyplot as plt

H = Herbie("2021-07-11", model="gfs", product="pgrb2.0p25")

# Show all available sources
H.SOURCES

# Show all available products
H.PRODUCTS

# Download the full GRIB2 file
H.download()

# Plot t2m
ds = H.xarray(":TMP:2 m above")

ax = EasyMap(crs=ds.herbie.crs, figsize=[10, 8]).ax
p = ax.pcolormesh(
    ds.longitude,
    ds.latitude,
    ds.t2m - 273.15,
    transform=pc,
    **paint.NWSTemperature.kwargs2,
)
plt.colorbar(
    p, ax=ax, orientation="horizontal", pad=0.05, **paint.NWSTemperature.cbar_kwargs2
)

ax.set_title(ds.t2m.GRIB_name, loc="right")
ax.set_title(f"{ds.model.upper()}: {H.product_description}", loc="left")
```

## GFS wave data

- <https://herbie.readthedocs.io/en/latest/gallery/noaa_models/gfs.html#GFS-wave-data>

```python
from herbie import Herbie
from herbie.toolbox import EasyMap, pc, ccrs
from herbie import paint

import matplotlib.pyplot as plt

H = Herbie("2021-07-11", model="gfs_wave")

H.inventory()
```

## GEFS

- <https://herbie.readthedocs.io/en/latest/gallery/noaa_models/gefs.html>

```python
from herbie import Herbie
from herbie.toolbox import EasyMap, pc
from herbie import paint

import matplotlib.pyplot as plt

H = Herbie(
    "2023-01-04 12:00",
    model="gefs",
    product="atmos.5",
    member="mean",
)

H

# Show all the available products (from the model template file)
H.PRODUCTS

df = H.inventory()
df

df.variable.unique()

H.inventory("HGT")

ds = H.xarray("PWAT")
ds

ds2 = H.xarray("HGT:500 mb")
ds2

ax = EasyMap("50m", crs=ds.herbie.crs, figsize=[10, 10]).STATES().BORDERS().ax
p = ax.pcolormesh(
    ds.longitude,
    ds.latitude,
    ds.pwat,
    transform=pc,
    cmap=paint.NWSPrecipitation.cmap,
    vmin=0,
    vmax=80,
)
plt.colorbar(
    p,
    ax=ax,
    orientation="horizontal",
    pad=0.01,
    shrink=0.8,
    label=f"{ds.pwat.GRIB_name}({ds.pwat.GRIB_units})",
)

# Add 500 hPa Geopotential height
ax.contour(
    ds2.longitude,
    ds2.latitude,
    ds2.gh,
    colors="k",
    linewidths=0.5,
    levels=range(0, 10000, 120),
)

ax.set_title(
    f"{ds.model.upper()} ensemble mean\nValid: {ds.valid_time.dt.strftime('%H:%M UTC %d %b %Y').item()}",
    loc="left",
)
ax.set_title(ds.pwat.GRIB_name, loc="right")

# Get a specific member
H5 = Herbie(
    "2022-01-01",
    model="gefs",
    product="atmos.5",
    member=5,
)
H5.xarray("TMP:2 m")

## If a user wants all the members in
## a single Dataframe, I'll let the user
## concat it themselves.
```