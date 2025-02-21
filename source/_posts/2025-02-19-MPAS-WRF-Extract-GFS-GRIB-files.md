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

# Read GFS grib2 files

To process a CSV file containing station information (latitude and longitude) and extract weather data from GFS GRIB files, you can follow the steps below. This example assumes you have a CSV file named `stations.csv` with columns for station names, latitude, and longitude.

### Step 1: Prepare Your CSV File

Ensure your `stations.csv` has the following structure:

```
station,latitude,longitude
Station1,40.7128,-74.0060
Station2,34.0522,-118.2437
...
```

### Step 2: Code to Extract Data and Save to CSV

Here's a complete Python script that reads the station information, extracts the required data from the GRIB files, and writes the results to a new CSV file.

```python
import pandas as pd
import pygrib
import numpy as np

# Load station data from CSV
stations_df = pd.read_csv('stations.csv')

# Open the GRIB file
grbs = pygrib.open('path_to_your_file.grib2')  # Change the path to your GRIB file

# Prepare a list to store the results
results = []

# Function to find the closest grid point
def get_closest_grid_point(grbs, lat, lon):
    closest_grb = None
    min_distance = float('inf')
    
    for grb in grbs:
        latitudes, longitudes = grb.latlons()
        distances = np.sqrt((latitudes - lat)**2 + (longitudes - lon)**2)
        index = np.unravel_index(np.argmin(distances), distances.shape)
        
        distance = distances[index]
        if distance < min_distance:
            min_distance = distance
            closest_grb = (grb, index)

    return closest_grb

# Loop through each station
for index, row in stations_df.iterrows():
    station_name = row['station']
    station_lat = row['latitude']
    station_lon = row['longitude']
    
    closest_grb, index = get_closest_grid_point(grbs, station_lat, station_lon)

    # Extract required data
    t2m = closest_grb[0].data()[index][0]  # Temperature at 2m
    u10 = closest_grb[0].data()[index][1]  # U-component of wind at 10m
    v10 = closest_grb[0].data()[index][2]  # V-component of wind at 10m
    rh2m = closest_grb[0].data()[index][3] # Relative humidity at 2m

    # Append to results
    results.append({
        'station': station_name,
        't2m': t2m,
        'u10': u10,
        'v10': v10,
        'rh2m': rh2m
    })

# Create a DataFrame from the results
results_df = pd.DataFrame(results)

# Save results to a new CSV file
results_df.to_csv('weather_data.csv', index=False)

# Close the GRIB file
grbs.close()

print("Data extraction completed and saved to weather_data.csv.")
```

### Step 3: Run the Script

1. Make sure to replace `'path_to_your_file.grib2'` with the actual path to your GRIB file.
2. Save the script to a `.py` file and run it using Python.

### Output

The output will be a CSV file named `weather_data.csv` with the following columns:

- `station`
- `t2m` (Temperature at 2m)
- `u10` (U-component of wind at 10m)
- `v10` (V-component of wind at 10m)
- `rh2m` (Relative humidity at 2m)

### Note

- Ensure that the indices for `t2m`, `u10`, `v10`, and `rh2m` are correctly set according to how your GRIB data is structured.
- You may need to adjust the extraction logic based on the specific GRIB messages you are working with.