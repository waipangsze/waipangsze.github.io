---
layout: post
title: Jupyter-Notebook
categories: [Jupyter]
tags: [Jupyter, python]
author: wpsze
---

# How to display full output in Jupyter, not only last result?
```python
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = "all"
```

# Sample plot
```sh
import os.path
import xarray as xr
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd

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

# plt.rcParams['font.sans-serif'] = ['SimHei'] 
# plt.rcParams['axes.unicode_minus'] = False

plt.rcParams.update({'font.family':'sans-serif'})
plt.rcParams['font.sans-serif'] = ['DejaVu Sans']

#
#
#

plt.title("diff T2m (UCM-on - UCM-off) of selected urban area")
plt.ylabel("T2m (deg C)")
plt.xlabel("Time (HKT)")

# Add a horizontal line across the Axes.
plt.axhline(y = 0.0, color = 'k', linestyle='--', linewidth=2, label = 'axhline')
plt.axvline(x = 1.0, color = 'b', linestyle='--', linewidth=2, label = 'axvline')

plt.xticks(rotation=45)
plt.grid(linestyle='--')

# Put a legend to the right of the current axis
plt.legend(loc='center left', bbox_to_anchor=(1, 0.5))
plt.tight_layout()
plt.show()
plt.close()
```

