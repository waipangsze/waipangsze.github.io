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
index_img: https://i.imgur.com/dPlkBuU.png
banner_img: https://i.imgur.com/dPlkBuU.png
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

- `./download_grib.sh 2024120400 3 384 1>log.04 2>&1 &`

{% fold info @download_grib.sh %}
```sh
#!/bin/bash

set -o pipefail
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate herbie-data

export HERBIE_SAVE_DIR="/home/wpsze/cpas/IFS/herbie/"

# Check if all required arguments are provided
if [ -z "$1" ] || [ -z "$2" ] || [ -z "$3" ]; then
    echo "Usage: $0 <yyyymmddhh> <time_interval> <hour_periods>"
    echo "Example: $0 2025101000 6 48"
    echo ""
    echo "  <yyyymmddhh> : IFS run date and hour (e.g., 2025101000)"
    echo "  <time_interval>: Interval between fxx hours (e.g., 6 for 0,6,12...)"
    echo "  <hour_periods> : Maximum forecast hour (e.g., 48)"
    exit 1
fi

DATETIME_INPUT=$1
TIME_INTERVAL=$2  # e.g., 6
HOUR_PERIODS=$3   # e.g., 48
yyyymm=${DATETIME_INPUT:0:6}
yyyymmdd=${DATETIME_INPUT:0:8}

# --- Configuration ---
PYTHON_SCRIPT="download_ifs_herbie.py"

# Generate the comma-separated list of forecast hours (FXX_RANGE)
# 'seq' generates the sequence: start at 0, step by TIME_INTERVAL, up to HOUR_PERIODS.
# '-s ,' specifies the comma separator.
FXX_RANGE=$(seq -s ',' 0 "${TIME_INTERVAL}" "${HOUR_PERIODS}")

echo "Running IFS GRIB Download..."
echo "Requested Run Time (yyyymmddhh): ${DATETIME_INPUT}"
echo "Time Interval: ${TIME_INTERVAL} hours"
echo "Max Period: ${HOUR_PERIODS} hours"
echo "Generated Forecast Periods (fxx): ${FXX_RANGE}"
echo "--------------------------------------------------------"

# Execute the Python script, passing the datetime and generated fxx range
# Ensure you have 'herbie' and 'python3' installed in your environment.
python3 "${PYTHON_SCRIPT}" --datetime "${DATETIME_INPUT}" --fxx "${FXX_RANGE}"

# Check the exit status of the python script
if [ $? -eq 0 ]; then
    echo "--------------------------------------------------------"
    echo "Download process completed successfully."
else
    echo "--------------------------------------------------------"
    echo "Download process finished, but some errors occurred (check logs above)."
fi
```
{% endfold %}

{% fold info @download_ifs_herbie.py %}
```python
#!/bin/python
# -*- coding: utf-8 -*-

import argparse
import sys
from herbie import Herbie
from datetime import datetime

def download_ifs_data(datetime_str: str, fxx_periods: str):
    """
    Downloads IFS GRIB files using Herbie for a specified run time and list of forecast hours.

    Args:
        datetime_str (str): Run datetime in YYYYMMDDHH format (e.g., '2025101000').
        fxx_periods (str): Comma-separated string of forecast hours (fxx) (e.g., '0,6,12,24').
    """
    if len(datetime_str) != 10:
        print(f"Error: Datetime must be in YYYYMMDDHH format (10 characters). Received: {datetime_str}", file=sys.stderr)
        sys.exit(1)

    try:
        # Convert YYYYMMDDHH to the required Herbie format (YYYY-MM-DD HH:00:00)
        run_date_str = f"{datetime_str[:4]}-{datetime_str[4:6]}-{datetime_str[6:8]} {datetime_str[8:10]}:00:00"
        
        # Parse the comma-separated fxx periods into a list of integers
        fxx_list = [int(fxx.strip()) for fxx in fxx_periods.split(',')]
        
    except ValueError as e:
        print(f"Error parsing input: {e}", file=sys.stderr)
        sys.exit(1)
    
    print(f"--- Starting Download Process ---")
    print(f"IFS Run Time (Init): {run_date_str}")
    print(f"Forecast Hours (fxx): {fxx_list}")
    print("---------------------------------")
    
    # Herbie Setup: model="ifs", product="oper"
    # We create a Herbie object for each fxx period to ensure the correct valid time is queried.
    
    for fxx in fxx_list:
        print(f"\n[INFO] Attempting download for fxx={fxx}...")
        
        try:
            # Initialize Herbie for the specific run and forecast hour
            H = Herbie(
                run_date_str,
                model="ifs",
                product="oper",
                fxx=fxx,
                # Example of specifying a search string for faster downloads
                # If left unspecified, it will download all GRIB messages found
                # searchString=':(U|V|T|Z|G)GRD:10 m above ground|500 hPa:', 
            )
            
            # --- Optional: Inventory Check ---
            # print(f"[DEBUG] Inventory for fxx={fxx}:")
            # print(H.inventory())
            
            # --- Download ---
            H.download()
            print(f"[SUCCESS] Download complete for fxx={fxx}.")

        except Exception as e:
            print(f"[ERROR] Failed to download fxx={fxx}. Reason: {e}", file=sys.stderr)
            # Continue to the next forecast hour if one fails
            continue 


if __name__ == "__main__":
    parser = argparse.ArgumentParser(
        description="Download IFS GRIB files using Herbie, iterating over forecast periods."
    )
    parser.add_argument(
        "--datetime",
        type=str,
        required=True,
        help="Run datetime in YYYYMMDDHH format (e.g., 2025101000)."
    )
    parser.add_argument(
        "--fxx",
        type=str,
        default="0,6,12,18,24,36",
        help="Comma-separated list of forecast hours (fxx) to download (e.g., 0,6,12,24). Default is 0,6,12,18,24,36."
    )
    
    args = parser.parse_args()
    download_ifs_data(args.datetime, args.fxx)
```
{% endfold %}

{% fold info @symlink_herbie.sh %}
```sh
#!/bin/bash
for filename in "herbie/ifs"/*/*"oper-fc.grib2"; do
	yyyymm="${filename:11:6}"
	yyyymmdd="${filename:11:8}"
	hh="${filename:28:2}"
    echo ${filename}, ${yyyymm}, ${yyyymmdd}, ${hh}
	mkdir -p /home/wpsze/IFS/${yyyymm}/${yyyymmdd}/
    ln -s /home/wpsze/IFS/${filename} /home/wpsze/IFS/${yyyymm}/${yyyymmdd}/
done

for filename in "herbie/ifs"/*/*"scda-fc.grib2"; do
	yyyymm="${filename:11:6}"
	yyyymmdd="${filename:11:8}"
	hh="${filename:28:2}"
    echo ${filename}, ${yyyymm}, ${yyyymmdd}, ${hh}
	mkdir -p /home/wpsze/IFS/${yyyymm}/${yyyymmdd}/
    ln -s /home/wpsze/IFS/${filename} /home/wpsze/IFS/${yyyymm}/${yyyymmdd}/${yyyymmdd}${hh}0000-0h-oper-fc.grib2
done

#-- search all symlink and delete if need
# find ./20* -type l -ls
# find ./20* -type l -ls -delete 
```
{% endfold %}

![](https://i.imgur.com/o1xSqZN.png)
![](https://i.imgur.com/dPlkBuU.png)
![](https://i.imgur.com/vCWDYAP.png)

{% fold info @H.search_help %}
```log
Use regular expression to search for lines in the index file.
Here are some examples you can use for the ecCodes-style `search`

Look at the ECMWF GRIB Parameter Database
https://apps.ecmwf.int/codes/grib/param-db


product=`oper` or `enfo`
======================== ==============================================
search=                  GRIB messages that will be downloaded
======================== ==============================================
":2t:"                   2-m temperature
":2d:"                   2-m dew point temperature
":10u:"                  10-m u wind vector
":10v:"                  10-m v wind vector
":10[uv]:                10-m u and 10-m v wind
":[tuvr]:"               Temp, u/v wind, RH (all levels)
":500:"                  All variables on the 500 hPa level
":gh:500"                Geopotential height only at 500 hPa
":gh:"                   Geopotential height (all pressure levels)
":t:"                    Temperature (all pressure levels)
":q:"                    Specific Humidity (all pressure levels)
":r:"                    Relative humidity (all pressure levels)
":v:"                    v wind vector (all pressure levels)
":u:"                    u wind vector (all pressure levels)
":w:"                    Vertical velocity (Pascals per second)
":lsm:"                  Land-sea mask
":ttr:"                  Top net long-wave (thermal) radiation
":ssrd:"                 Surface short-wave (solar) radiation downwards
":ssr:"                  Surface net short-wave (solar) radiation
":strd:"                 Surface long-wave (thermal) radiation downwards
":str:"                  Surface net long-wave (thermal) radiation
":swvl1:"                Volumetric soil water layer 1 (depth 0 meters)
":swvl2:"                Volumetric soil water layer 2 (depth 7 meters)
":swvl3:"                Volumetric soil water layer 3 (depth 28 meters)
":swvl4:"                Volumetric soil water layer 4 (depth 100 meters)
":skt:"                  Skin (surface) temperature
":d:"                    Divergence (all levels)
":st:"                   Soil temperature
":stl2:"                 Soil temperature level 2 (depth 7 meters)
":tp:"                   Total precipitation
":ro:"                   Run-off
":asn:"                  Snow albedo
":msl:"                  Mean sea level pressure
":sp:"                   Surface pressure
":cape:"                 CAPE
":tcwv:"                 Total column vertically integrated water vapor
":vo:"                   Relative vorticity

product=`wave` or `waef`
======================== ==============================================
search=                  GRIB messages that will be downloaded
======================== ==============================================
":swh:"                  Significant height of wind waves + swell
":mwp:"                  Mean wave period
":mwd:"                  Mean wave direction
":pp1d:"                 Peak wave period
":mp2:"                  Mean zero-crossing wave period

If you need help with regular expression, search the web or look at
this cheatsheet: https://www.petefreitag.com/cheatsheets/regex/.

Here is an example: https://regex101.com/r/niNjwp/1
```
{% endfold %}

### Problem

- The grib files from `ecmwf.ini` and from `aws` are different. It will make different/debug on the `ungrib` step and `init_atmosphere_model` step.
  - Not all different.

It is because,

- [ECMWF Near-Realtime IFS Atmospheric Forecasts | Earth Engine Data Catalog](https://developers.google.com/earth-engine/datasets/catalog/ECMWF_NRT_FORECAST_IFS_OPER?hl=zh-TW)
  - **資料集可用性 2024-11-12T12:00:00Z–2025-04-16T12:00:00Z**
  - **自 2024/11/12 推出Cycle 49r1 以來**，Earth Engine 就會提供產品；早期產品則不包含在內。
    - old one: 145 items
    - new one: 160 items

![IFS:2024-04-26_00.nc](https://i.imgur.com/Ox9xzuB.png)
![IFS:2025-03-26_00.nc](https://i.imgur.com/nr6Nkjg.png)

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