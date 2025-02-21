---
layout: post
title: MPAS | WRF | Extract subset of GFS GRIB files
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS]
author: wpsze
date: 2025-02-19 18:02:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/GO7mmRR.png
banner_img: https://i.imgur.com/GO7mmRR.png
---

# GFS subset download

- <https://rda.ucar.edu/datasets/d084001/dataaccess/#>
  - Sign up with orcid
  - Customizable Data Requests > Subsetting 
  - Waiting for completion
  - csh/python scripts are provided

{% gi 4 1-3 %}
![](https://i.imgur.com/GO7mmRR.png)
![](https://i.imgur.com/gyRwXFJ.png)
![](https://i.imgur.com/HDtKiPl.png)
{% endgi %}

{% fold info @rda-download.csh %}
```sh
#!/usr/bin/env csh
#
# c-shell script to download selected files from rda.ucar.edu using Wget
# NOTE: if you want to run under a different shell, make sure you change
#       the 'set' commands according to your shell's syntax
# after you save the file, don't forget to make it executable
#   i.e. - "chmod 755 <name_of_script>"
#
# Experienced Wget Users: add additional command-line flags to 'opts' here
#   Use the -r (--recursive) option with care
#   Do NOT use the -b (--background) option - simultaneous file downloads
#       can cause your data access to be blocked
set opts = "-N"
#
# Check wget version.  Set the --no-check-certificate option 
# if wget version is 1.10 or higher
set v = `wget -V |grep 'GNU Wget ' | cut -d ' ' -f 3`
set a = `echo $v | cut -d '.' -f 1`
set b = `echo $v | cut -d '.' -f 2`
if(100 * $a + $b > 109) then
  set cert_opt = "--no-check-certificate"
else
  set cert_opt = ""
endif

set filelist= ( \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2024123000.f384-25.2025011200.f195.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025011200.f198-25.2025011618.f135.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025011618.f138-25.2025012100.f360.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012100.f366-25.2025012512.f129.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012512.f132-25.2025012918.f330.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012918.f336-25.2025020306.f105.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025020306.f108-25.2025020712.f228.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025020712.f231-25.2025021200.f003.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025021200.f006-25.2025021606.f084.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025021606.f087-25.2025021712.f384.grib2.tar \
)
while($#filelist > 0)
  set syscmd = "wget $cert_opt $opts $filelist[1]"
  echo "$syscmd ..."
  $syscmd
  shift filelist
end
```
{% endfold %}

{% fold info @rda-download.py %}
```python
#!/usr/bin/env python
""" 
Python script to download selected files from rda.ucar.edu.
After you save the file, don't forget to make it executable
i.e. - "chmod 755 <name_of_script>"
"""
import sys, os
from urllib.request import build_opener

opener = build_opener()

filelist = [
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2024123000.f384-25.2025011200.f195.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025011200.f198-25.2025011618.f135.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025011618.f138-25.2025012100.f360.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012100.f366-25.2025012512.f129.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012512.f132-25.2025012918.f330.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012918.f336-25.2025020306.f105.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025020306.f108-25.2025020712.f228.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025020712.f231-25.2025021200.f003.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025021200.f006-25.2025021606.f084.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025021606.f087-25.2025021712.f384.grib2.tar'
]

for file in filelist:
    ofile = os.path.basename(file)
    sys.stdout.write("downloading " + ofile + " ... ")
    sys.stdout.flush()
    infile = opener.open(file)
    outfile = open(ofile, "wb")
    outfile.write(infile.read())
    outfile.close()
    sys.stdout.write("done\n")
```
{% endfold %}

# Micromamba env setup

```yml
 $ cat list.yml 
name: PGW
channels:
- anaconda
- conda-forge
dependencies:
- conda-forge::cdo
- conda-forge::dask
- matplotlib
- python=3.11
- scipy
- conda-forge::wgrib
```

# Read GFS grib2 files

To process a CSV file containing station information (latitude and longitude) and extract weather data from GFS GRIB files, you can follow the steps below. This example assumes you have a CSV file named `stations.csv` with columns for station names, latitude, and longitude.

```console
 $ grib_ls ./subset-GFS/gfs.0p25.2025021200.f006.grib2
./subset-GFS/gfs.0p25.2025021200.f006.grib2
edition      centre       date         dataType     gridType     typeOfLevel  level        stepRange    shortName    packingType  
2            kwbc         20250212     fc           regular_ll   surface      0            0-6          dswrf        grid_complex_spatial_differencing 
1 of 1 messages in ./subset-GFS/gfs.0p25.2025021200.f006.grib2
```

## Step 1: Prepare Your CSV File

Ensure your `stations.csv` has the following structure:

```
station,latitude,longitude
Station1,22.3,114.2
Station2,22.6,114.6
```

## Step 2: Code to Extract Data and Save to CSV

Here's a complete Python script that reads the station information, extracts the required data from the GRIB files, and writes the results to a new CSV file.

{% fold info @extract_GFS.py %}
```python
#!/bin/python
import xarray as xr
import pandas as pd

# Step 1: Load the station data
stations = pd.read_csv('station.csv')

# Step 2: Initialize an empty list to store results
results = []

# Step 3: Load GFS GRIB files
# Replace 'path_to_grib_files/*.grib' with your actual path to the GRIB files
grib_files = './subset-GFS/gfs.0p25.2025021200.f006.grib2'

# Step 4: Open the GRIB files using xarray
# ds = xr.open_mfdataset(grib_files, engine='cfgrib', combine='by_coords')
ds = xr.open_dataset(grib_files, engine='cfgrib') #,backend_kwargs={'filter_by_keys': {'typeOfLevel': 'isobaricInhPa'}})

# Print the names of the variables
print(ds)

# Step 5: Extract data for each station
for index, row in stations.iterrows():
    lat = row['latitude']
    lon = row['longitude']
    
    # Step 6: Interpolate the data using bilinear interpolation
    # t2m = ds['t2m'].interp(latitude=lat, longitude=lon, method='linear').values
    # u10 = ds['u10'].interp(latitude=lat, longitude=lon, method='linear').values
    # v10 = ds['v10'].interp(latitude=lat, longitude=lon, method='linear').values
    # rh2m = ds['rh2m'].interp(latitude=lat, longitude=lon, method='linear').values
    sdswrf = ds['sdswrf'].interp(latitude=lat, longitude=lon, method='linear').values
    
    # Step 7: Append the results
    results.append({
        'station': row['station'],
        'sdswrf': sdswrf
        # 't2m': t2m,
        # 'u10': u10,
        # 'v10': v10,
        # 'rh2m': rh2m
    })

# Step 8: Create a DataFrame from the results
output_df = pd.DataFrame(results)

# Step 9: Save the results to a CSV file
output_df.to_csv('output.csv', index=False)
```
{% endfold %}

## Step 3: Run the Script

1. Make sure to replace `'path_to_your_file.grib2'` with the actual path to your GRIB file.
2. Save the script to a `.py` file and run it using Python.

## Output

The output will be a CSV file named `output.csv` with the following columns:

- `station`
- `t2m` (Temperature at 2m)
- `u10` (U-component of wind at 10m)
- `v10` (V-component of wind at 10m)
- `rh2m` (Relative humidity at 2m)
- `sdswrf` (The surface downward shortwave radiation flux)

```console
 $ cat output.csv 
station,sdswrf
Station1,52.232960662841684
Station2,47.779839172363275
```

## Note

- Ensure that the indices for `t2m`, `u10`, `v10`, `rh2m`, and `sdswrf` are correctly set according to how your GRIB data is structured.
- You may need to adjust the extraction logic based on the specific GRIB messages you are working with.

# Read Multiple GFS grib files

## Code to Extract Data and Save to CSV

{% fold info @extract_GFS_multiple.py %}
```python
#!/bin/python

#------------------------------------------------#
#Author:         wpsze
#Email：         wpsze
#date:           2025-02-19 14:29:21
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2025 All rights reserved
#------------------------------------------------#

import xarray as xr
import pandas as pd
import glob

# Step 1: Load the station data
stations = pd.read_csv('station.csv')

# Step 2: Get and sort the list of GFS GRIB files
grib_files = sorted(glob.glob('./subset-GFS/gfs.0p25.2025021200.f*.grib2') )  # Adjust the path and pattern

# Step 3: Interpolate data for each station across all GRIB files
results = []

# GFS grib file
# Coordinates:
# * time        (time) datetime64[ns] 824B 2025-02-12 2025-02-12 ... 2025-02-12
# step        (time) timedelta64[ns] 824B 0 days 06:00:00 ... 16 days 00:00:00
# surface     float64 8B 0.0
# * latitude    (latitude) float64 6kB 90.0 89.75 89.5 ... -89.5 -89.75 -90.0
# * longitude   (longitude) float64 12kB 0.0 0.25 0.5 0.75 ... 359.2 359.5 359.8
# valid_time  (time) datetime64[ns] 824B 2025-02-12T06:00:00 ... 2025-02-28


for grib_file in grib_files:
    print(f"Processing: {grib_file}")

    # Step 4: Open the GRIB files using xarray
    # ds = xr.open_mfdataset(grib_files, engine='cfgrib', combine='by_coords') #combine='nested', concat_dim="time") # combine='by_coords')
    ds = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True) #,backend_kwargs={'filter_by_keys': {'typeOfLevel': 'isobaricInhPa'}})

    ## Print the names of the variables
    # print(ds)
    # selected_time = ds.time.values # It is initial time!!
    selected_time = ds.valid_time.values # It is valid time
    ## Assuming selected_time_utc is in numpy datetime64 format
    selected_time_utc = pd.to_datetime(selected_time)  # Convert to pandas datetime
    selected_time_utc_plus_8 = selected_time_utc + pd.Timedelta(hours=8)  # Add 8 hours

    print(selected_time)

    # Step 5: Extract data for each station
    for index, row in stations.iterrows():
        lat = row['latitude']
        lon = row['longitude']
        
        # Step 6: Interpolate the data using bilinear interpolation
        # t2m = ds['t2m'].interp(latitude=lat, longitude=lon, method='linear').values
        # u10 = ds['u10'].interp(latitude=lat, longitude=lon, method='linear').values
        # v10 = ds['v10'].interp(latitude=lat, longitude=lon, method='linear').values
        # rh2m = ds['rh2m'].interp(latitude=lat, longitude=lon, method='linear').values
        sdswrf = ds['sdswrf'].interp(latitude=lat, longitude=lon, method='linear').values
        
        # Step 7: Append the results
        results.append({
            'station': row['station'],
            'datetime': selected_time,
            'sdswrf': sdswrf
            # 't2m': t2m,
            # 'u10': u10,
            # 'v10': v10,
            # 'rh2m': rh2m
        })

# Step 8: Create a DataFrame from the results
output_df = pd.DataFrame(results)

# Step 9: Save the results to a CSV file
output_df.to_csv('output_multiple.csv', index=False)
```
{% endfold %}

## Output

{% fold info @output_multiple.csv %}
```console
station,datetime[UTC],datetime[BJT],sdswrf
Station1,2025-02-12 06:00:00,2025-02-12 14:00:00,52.232960662841684
Station2,2025-02-12 06:00:00,2025-02-12 14:00:00,47.779839172363275
Station1,2025-02-12 09:00:00,2025-02-12 17:00:00,62.86639785766609
Station2,2025-02-12 09:00:00,2025-02-12 17:00:00,40.13679992675776
Station1,2025-02-12 12:00:00,2025-02-12 20:00:00,33.28959899902347
Station2,2025-02-12 12:00:00,2025-02-12 20:00:00,22.780800170898374
Station1,2025-02-12 15:00:00,2025-02-12 23:00:00,0.0
Station2,2025-02-12 15:00:00,2025-02-12 23:00:00,0.0
Station1,2025-02-12 18:00:00,2025-02-13 02:00:00,0.0
Station2,2025-02-12 18:00:00,2025-02-13 02:00:00,0.0
Station1,2025-02-12 21:00:00,2025-02-13 05:00:00,0.0
Station2,2025-02-12 21:00:00,2025-02-13 05:00:00,0.0
Station1,2025-02-13 00:00:00,2025-02-13 08:00:00,2.2220799827575504
Station2,2025-02-13 00:00:00,2025-02-13 08:00:00,3.2377599430084407
Station1,2025-02-13 03:00:00,2025-02-13 11:00:00,146.60479980468722
Station2,2025-02-13 03:00:00,2025-02-13 11:00:00,215.17280273437623
Station1,2025-02-13 06:00:00,2025-02-13 14:00:00,154.97856079101527
Station2,2025-02-13 06:00:00,2025-02-13 14:00:00,259.47327941894736
Station1,2025-02-13 09:00:00,2025-02-13 17:00:00,137.50800354003917
......
```
{% endfold %}

# `xarray`'s `interp` method with the `"linear"` option on a 2D array

When using `xarray`'s `interp` method with the `"linear"` option on a 2D array, the method performs linear interpolation along the specified dimensions. This allows you to compute interpolated values at new coordinate points based on existing data in two dimensions.

### How It Works for 2D Arrays

1. **Data Structure**: A 2D array in `xarray` is typically represented as a `DataArray` or a `Dataset`, where you have two dimensions (e.g., latitude and longitude, or time and another variable).
2. **Linear Interpolation**: The `"linear"` method calculates the interpolated values by creating linear relationships between existing data points. For each new coordinate point specified, it finds the nearest surrounding points in both dimensions and computes the interpolated value based on the linear ratio of the distances.

- For each new coordinate:
  - The method identifies the surrounding points in the original 2D array.
  - It calculates the interpolated value based on the linear relationship between the values at these surrounding points.
  - The interpolation is done separately for each dimension, effectively creating a **bilinear interpolation effect**.
- **2D Linear Interpolation**: The `"linear"` method allows you to compute intermediate values smoothly between known data points in a 2D grid.
- **Applications**: This is useful in various fields such as geospatial analysis, climate modeling, and any scenario where a grid of values needs to be estimated at non-integer coordinates.

Using `xarray`'s `interp` method in this way enables effective handling of multi-dimensional datasets, ensuring accurate and smooth transitions between data points.