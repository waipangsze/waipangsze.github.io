---
layout: post
title: MPAS | Long Term Run study
categories: [MPAS]
tags: [NWP, MPAS, WRF, Climate]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-06-20 14:09:00
index_img: https://i.imgur.com/YIH5GKa.png
banner_img: https://i.imgur.com/YIH5GKa.png
---

# Long run study

## SST udpated

I will analyze the experimental design involving a 1-year NWP simulation using MPAS-A initialized with ERA5 data, comparing a control run with initial conditions to an experimental run with periodically updated SST every 7 days, emphasizing the scientific rationale, expected impacts on model performance, and potential challenges in maintaining consistency and accuracy over such a long-term simulation.

### Setup

- MPASv8.2.2
- Mesh = 120km unifrom MAPS mesh
- Mesoscale Reference
- IC=ERA5, 2023-01-01 UTC
- Ouput interval = 12 hours
- If SST updated, the updated interval = 7 days

### Control Run Configuration

Your control experiment uses ERA5 initial conditions with fixed boundary conditions throughout the one-year simulation. This approach represents the traditional "initial value problem" paradigm in NWP, where atmospheric evolution depends solely on the initial atmospheric state without external forcing updates.

The control run serves as a baseline to isolate the impact of SST variability by maintaining all other boundary conditions constant. This design allows for clear attribution of differences between the control and experimental runs to SST updates specifically.

### Experimental Run with SST Updates
The experimental configuration introduces SST updates at 7-day intervals, representing a **more realistic representation of ocean-atmosphere coupling**. This approach acknowledges that SST plays a crucial role in atmospheric predictability, particularly for extended-range forecasting.

Seven-day SST update intervals represent a practical compromise between:

- Computational efficiency
- Capturing significant SST variability that affects atmospheric processes
- Maintaining realistic air-sea interaction timescales

### Scientific Rationale

#### Ocean-Atmosphere Coupling Importance

Sea surface temperatures significantly influence atmospheric circulation through several mechanisms:

- Energy Exchange: SST controls latent and sensible heat fluxes between ocean and atmosphere
- Boundary Layer Dynamics: SST gradients drive atmospheric pressure patterns and wind systems
- Teleconnections: SST anomalies can trigger large-scale atmospheric responses affecting global weather patterns

### Results

{% gi 11 1-2-2-2-2-2 %}
![](https://i.imgur.com/YIH5GKa.png)
![](https://i.imgur.com/inCARE8.png)
![](https://i.imgur.com/bPSQgY6.png)
![](https://i.imgur.com/N4EC6tI.png)
![](https://i.imgur.com/Ws13Bir.png)
![](https://i.imgur.com/tnM6qxH.png)
![](https://i.imgur.com/C0uIoIv.png)
![](https://i.imgur.com/2m3kEug.png)
![](https://i.imgur.com/5rxcIzl.png)
![](https://i.imgur.com/6fNRPtx.png)
![](https://i.imgur.com/mqQITNg.png)
{% endgi %}

## Script

{% fold info @plot %}
```python
%matplotlib inline
import os.path
import xarray as xr
import numpy as np
import pandas as pd
import matplotlib.tri as tri
import matplotlib.pyplot as plt

import os, math, time, sys, glob, logging
from scipy import stats as st

SMALL_SIZE = 8
MEDIUM_SIZE = 10
BIGGER_SIZE = 24

plt.rcParams["figure.figsize"] = (10,8)
plt.rc('font', size=BIGGER_SIZE)          # controls default text sizes
plt.rc('axes', titlesize=BIGGER_SIZE)     # fontsize of the axes title
plt.rc('axes', labelsize=BIGGER_SIZE)    # fontsize of the x and y labels
plt.rc('xtick', labelsize=BIGGER_SIZE)    # fontsize of the tick labels
plt.rc('ytick', labelsize=BIGGER_SIZE)    # fontsize of the tick labels
plt.rc('legend', fontsize=BIGGER_SIZE)    # legend fontsize
plt.rc('figure', titlesize=BIGGER_SIZE)  # fontsize of the figure title

rsim_path = "/home/wpsze/projects/MPAS-120km-MPASv822/jobs/x1.40962-ERA5/rsim/2023010100"

in_static = xr.open_dataset(f"{rsim_path}/init/static.nc")

dataset_base_atm = xr.open_mfdataset(f"{rsim_path}/base_atm/diag*.nc", combine="nested",concat_dim="time")
dataset_atm = xr.open_mfdataset(f"{rsim_path}/atm/diag*.nc", combine="nested",concat_dim="time")

datetime = []
tmp_datetime = dataset_base_atm["xtime"].values.astype(str)
for px in range(tmp_datetime.shape[0]):
    datetime.append(tmp_datetime[px][0].strip())
    
datetime = pd.to_datetime(datetime, format="%Y-%m-%d_%H:%M:%S")
datetime


print(in_static)
latCell = in_static["latCell"][:].values #*57.2957795
lonCell = in_static["lonCell"][:].values #*57.2957795
nCells  = latCell.shape[0]
dcEdge = in_static["dcEdge"][:].values # meter
edgesOnCell = in_static["edgesOnCell"][:].values
nEdges = dcEdge.shape[0]
maxEdges = edgesOnCell.shape[1]
ivgtyp = in_static["ivgtyp"][:].values

print("="*40)
print("CPAS")
print("nCells, nEdges, maxEdges = ", nCells, nEdges, maxEdges)
print("latCell     = ", latCell.shape)
print("lonCell     = ", lonCell.shape)
print("dcEdge      = ", dcEdge.shape)
print("edgesOnCell = ", edgesOnCell.shape)
print("ivgtyp      = ", ivgtyp.shape)
print("min/max     = ", np.min(latCell), np.max(latCell), np.min(lonCell), np.max(lonCell))


var = np.zeros((2, 731, 40962))
var[0, :, :] = dataset_base_atm["t2m"][:,0,:]
var[1, :, :] = dataset_atm["t2m"][:,0,:]

mean_var = np.nanmean(var, axis=2)
print(mean_var.shape)

# Apply the 'ggplot' style
plt.style.use('ggplot')

# Creates a figure with a width of 10 inches and a height of 5 inches
plt.figure(figsize=(16, 8))

plt.plot(datetime, mean_var[0, :] - 273.15, 'ro-', label="default")
plt.plot(datetime, mean_var[1, :].T - 273.15, 'bo-', label="SST updated")

# Plot with default style
plt.title('t2m [degC]', fontsize=BIGGER_SIZE)
plt.xlabel('Date Time', fontsize=BIGGER_SIZE)
plt.ylabel('t2m [degC]', fontsize=BIGGER_SIZE)
plt.legend()
plt.show()
plt.close()

def plotting(name, t_name):
    var = np.zeros((2, 731, 40962))
    var[0, :, :] = dataset_base_atm[name][:,0,:]
    var[1, :, :] = dataset_atm[name][:,0,:]
    
    mean_var = np.nanmean(var, axis=2)
    print(mean_var.shape)
    
    # Apply the 'ggplot' style
    plt.style.use('ggplot')
    
    # Creates a figure with a width of 10 inches and a height of 5 inches
    plt.figure(figsize=(16, 8))
    
    plt.plot(datetime[2:], mean_var[0, 2:], 'ro-', label="default")
    plt.plot(datetime[2:], mean_var[1, 2:], 'bo-', label="SST updated")
    
    # Plot with default style
    plt.title(t_name, fontsize=BIGGER_SIZE)
    plt.xlabel('Date Time', fontsize=BIGGER_SIZE)
    plt.ylabel(t_name, fontsize=BIGGER_SIZE)
    plt.legend()
    plt.show()
    plt.close()

plotting("mslp", "mslp [Pa]")

plotting("wspd", "Wind speed at lowest level [ms^-1]")

plotting("swdnb", "all clear short wave radiation[W/m2]")

plotting("q2", "2m specific humidity [kg kg^{-1}]")

plotting("height_500hPa", "Geometric height interpolated to 250 hPa [m]")

plotting("temperature_500hPa", "Temperature vertically interpolated to 500 hPa [K]")
```
{% endfold %}