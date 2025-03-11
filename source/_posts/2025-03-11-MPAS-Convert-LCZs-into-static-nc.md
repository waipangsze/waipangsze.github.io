---
layout: post
title: MPAS | Convert LCZs into static.nc 
categories: [MPAS]
tags: [WRF, MPAS, Urban, UCM, LCZs, UCPs]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/5tUhQAG.png
banner_img: https://i.imgur.com/5tUhQAG.png 
---

- [**Map of Local Climate Zones (LCZs)**](https://waipangsze.github.io/2024/10/07/Global-LCZ-for-Urban/)

# LCZs source

- Fast and easy Local Climate Zone mapping
  - My example: <https://waipangsze.github.io/2024/10/07/Global-LCZ-for-Urban/#some-regional-lcz-datasets>
  - `tiff` file

## Convert tif to netcdf

- My Code: <https://waipangsze.github.io/2024/10/07/Global-LCZ-for-Urban/#convert-tif-to-netcdf>

# Convert LCZs into MPAS

{% fold info @convert2LCZ.py %}
```python
import os, math, time, sys, glob, logging
import xarray as xr
import numpy as np
from scipy import stats as st

logging.basicConfig(level=logging.WARNING) # DEBUG, INFO, WARNING

in_static = xr.open_dataset("in_static.nc")
lcz_dataset = xr.open_dataset("lcz.nc")

print(in_static)
print(lcz_dataset)

latCell = in_static["latCell"][:].values*57.2957795
lonCell = in_static["lonCell"][:].values*57.2957795
nCells  = latCell.shape[0]
dcEdge = in_static["dcEdge"][:].values # meter
edgesOnCell = in_static["edgesOnCell"][:].values
nEdges = dcEdge.shape[0]
maxEdges = edgesOnCell.shape[1]
ivgtyp = in_static["ivgtyp"][:].values

print("="*40)
print("nCells, nEdges, maxEdges = ", nCells, nEdges, maxEdges)
print("latCell     = ", latCell.shape)
print("lonCell     = ", lonCell.shape)
print("dcEdge      = ", dcEdge.shape)
print("edgesOnCell = ", edgesOnCell.shape)
print("ivgtyp      = ", ivgtyp.shape)
print("min/max     = ", np.min(latCell), np.max(latCell), np.min(lonCell), np.max(lonCell))

lat = lcz_dataset["lat"][:].values
lon = lcz_dataset["lon"][:].values
Band2 = lcz_dataset["Band2"][:].values
nlat = lat.shape[0]
nlon = lon.shape[0]

min_lat, max_lat = np.min(lat), np.max(lat)
min_lon, max_lon = np.min(lon), np.max(lon)
dlatlon = np.abs(lat[1] - lat[0])*111 # deg to km

print("="*40)
print("nlat, nlon = ", nlat, nlon)
print("lat        = ", lat.shape)
print("lon        = ", lon.shape)
print("min_lat, max_lat  = ", min_lat, max_lat)
print("min_lon, max_lon  = ", min_lon, max_lon)
print("diff lat/lon = ", lat[1:5]-lat[0:4], lon[1:5]-lon[0:4])
print("Band2      = ", Band2.shape)

print("="*40)
#radius = 1km
tmp_count_urban = 0
tmp_count_urban_change = 0
##
for px in range(nCells): # nCells
	if(px%5000 == 0):
		print(round(px/nCells*100), " %")
	
	if(ivgtyp[px] == 13 or ivgtyp[px] == 32): # 13 is urban or 32 is urban
		tmp_count_urban = tmp_count_urban + 1
		if( (min_lat <= latCell[px]) and (max_lat >= latCell[px]) and (min_lon <= lonCell[px]) and (max_lon >= lonCell[px]) ): # within LCZ dataset
			index_lat = np.argmin(np.abs(lat - latCell[px]))
			index_lon = np.argmin(np.abs(lon - lonCell[px]))
			tmp_info = str(latCell[px] - lat[index_lat]) +" "+ str(lonCell[px] - lon[index_lon]) +" "+ str(Band2[index_lat, index_lon])
			logging.info(tmp_info)
			tmp_count_urban_change = tmp_count_urban_change +1
			
			#
			# LCZ: 1 to 17 
			# Only 1 to 11 are used and corresponding to 31 (LCZ1) to 41 (LCZE = LCZ11, index = 15)
			# LCZ E (LCZ 11): Rock and paved;
			#
			if ( Band2[index_lat, index_lon] < 11 or Band2[index_lat, index_lon] == 15 ):
				#
				# For select from nearest cell,
				# in_static["ivgtyp"][px] = Band2[index_lat, index_lon] + 30
				#
				tmp_dx = np.min(dcEdge[edgesOnCell[px,:]])/1000   # meter to km
				tmp_num_cell = round((tmp_dx/dlatlon)/2)          # count and take half
				
				#
				# For take mode, may check the boundary of Band2
				# 1) remove 11,12,13,14,16,17
				#
				
				tmp_ar1 = Band2[index_lat-tmp_num_cell:index_lat+tmp_num_cell, index_lon-tmp_num_cell:index_lon+tmp_num_cell].flatten()
				tmp_ar2 = tmp_ar1
				
				# only 1-10 and 15(LCZ11) are our needs on LCZ dataset.
				# then, remove the test.
				for py in [11,12,13,14,16,17]:
					if np.where(tmp_ar2 == py)[0].size != 0: # if not empyt array
						tmp_ar2 = np.delete(tmp_ar2, np.where(tmp_ar2 == py)[0][:])
				
				tmp_array = tmp_ar2 + 30
				tmp = st.mode(tmp_array)[0] # stats. mode. Compute the mode (most common value) along an axis of an array.
				
				# To check wehther LCZ dataset is None value?
				# Some LCZ datasets has 0 that is nothing.
				if (len(tmp) == 0):
					continue
			        
				if (int(tmp[0]) == 45):
					in_static["ivgtyp"][px] = 41
				else:	
					in_static["ivgtyp"][px] = int(tmp[0])

			else:
				in_static["ivgtyp"][px] = 32
		else:
			in_static["ivgtyp"][px] = 32
				
print("Total urban in MPAS mesh  = ", tmp_count_urban)
print("Change urban in MPAS mesh = ", tmp_count_urban_change)
#	in_static["ivgtyp"][:] = 31/32/33
in_static.to_netcdf("out_static.nc")
```
{% endfold %}

## Example

A test case:

- GBA
- Tokyo
- Houston

{% fold info @Example %}
```console
├── convert2LCZ.py
├── final_static.nc -> final-process/step3/out_static.nc
├── GBA
│   ├── arcgis_lcz_colormap.clr
│   ├── c672ca892c88d1e078a81e7a085913396172f81c_factsheet.html
│   ├── c672ca892c88d1e078a81e7a085913396172f81c.kmz
│   ├── c672ca892c88d1e078a81e7a085913396172f81c.tif
│   ├── data
│   ├── factsheet_files
│   ├── figures
│   ├── GBA.nc -> lcz_filter_v3.nc
│   ├── lcz_filter_v3.nc
│   ├── lcz_filter_v3.tif -> c672ca892c88d1e078a81e7a085913396172f81c.tif
│   ├── log.log
│   ├── qgis_lcz_colormap.txt
│   └── run_tif2nc.sh -> ../run_tif2nc.sh
├── final-process
│   ├── convert2LCZ.py -> ../convert2LCZ.py
│   ├── lcz_filter_v3.nc -> ../lcz_filter_v3.nc
│   ├── run_nc2LCZ.sh -> ../run_nc2LCZ.sh
│   ├── static.nc -> /home/wpsze/static.nc
│   ├── step1
│   │   ├── convert2LCZ.py -> ../convert2LCZ.py
│   │   ├── GBA.nc -> ../../../GBA/GBA.nc
│   │   ├── in_static.nc -> static.nc
│   │   ├── lcz.nc -> GBA.nc
│   │   ├── out_static.nc
│   │   ├── run_nc2LCZ.sh -> ../run_nc2LCZ.sh
│   │   └── static.nc -> ../static.nc
│   ├── step2
│   │   ├── convert2LCZ.py -> ../../convert2LCZ.py
│   │   ├── in_static.nc -> ../step1/out_static.nc
│   │   ├── lcz.nc -> Tokyo.nc
│   │   ├── out_static.nc
│   │   ├── run_nc2LCZ.sh -> ../run_nc2LCZ.sh
│   │   └── Tokyo.nc -> ../../../Tokyo/Tokyo.nc
│   └── step3
│       ├── convert2LCZ.py -> ../convert2LCZ.py
│       ├── Houston.nc -> ../../../Houston/map/Houston.nc
│       ├── in_static.nc -> ../step2/out_static.nc
│       ├── lcz.nc -> Houston.nc
│       ├── out_static.nc
│       └── run_nc2LCZ.sh -> ../run_nc2LCZ.sh
├── Houston
│   ├── 037c80744fc1034b8472f594d829c53ce1cccfa7.zip
│   ├── 67a3371158ad3ac12d720fde089622c259e8e400.zip
│   └── map
├── lcz_filter_v3.nc
├── lcz_filter_v3.tif
├── run_nc2LCZ.sh
├── run_tif2nc.sh
└── Tokyo
    ├── 67a3371158ad3ac12d720fde089622c259e8e400_factsheet.html
    ├── 67a3371158ad3ac12d720fde089622c259e8e400.kmz
    ├── 67a3371158ad3ac12d720fde089622c259e8e400.tif
    ├── arcgis_lcz_colormap.clr
    ├── data
    ├── factsheet_files
    ├── figures
    ├── lcz_filter_v3.nc
    ├── lcz_filter_v3.tif -> 67a3371158ad3ac12d720fde089622c259e8e400.tif
    ├── log.log
    ├── qgis_lcz_colormap.txt
    ├── run_tif2nc.sh -> ../run_tif2nc.sh
    └── Tokyo.nc -> lcz_filter_v3.nc
```
{% endfold %}

{% fold info @run_tif2nc.sh %}
```
#!/bin/bash

source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate gdal

whereis gdal_translate

# for checking
gdal_translate --long-usage

# gdal_translate -of netCDF -co "FOMRAT=NC4" ia.tif ia.nc

# ln -s lcz_filter_v3.nc output.nc

gdal_translate -of netCDF -co "FOMRAT=NC4" lcz_filter_v3.tif lcz_filter_v3.nc 
```
{% endfold %}