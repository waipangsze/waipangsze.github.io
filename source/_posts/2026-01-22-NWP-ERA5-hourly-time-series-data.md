---
layout: post
title: NWP | ERA5 hourly time-series data 
categories: [NWP]
tags: [MPAS, NWP, WRF, ERA5, time-series]
author: wpsze
date: 2026-01-22 06:35:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/IvH5kTx.png
banner_img: https://i.imgur.com/IvH5kTx.png
---

- [ERA5 dataset](https://waipangsze.github.io/2023/04/30/ERA5_dataset/)
- [ERA5 ERA5T expver dimension issue](https://waipangsze.github.io/2023/05/07/ERA5-expver-issue/)
- [ERA5 dataset (2) contains invariant variables](https://waipangsze.github.io/2023/05/11/ERA5-2-invariant-variables/)
- [MPAS | ERA5| Update SST and sea-ice fraction](https://waipangsze.github.io/2025/02/14/MPAS-ERA5-Update-SST-and-sea-ice-fraction/)
- [NWP | ERA5 | GRIB1 and GRIB2](https://waipangsze.github.io/2025/02/05/NWP-ERA5-GRIB1-GRIB2/)
- [WRF | ERA5 IC | SMOIS issue](https://waipangsze.github.io/2025/11/06/WRF-ERA5-IC-SMOIS-issue/)

--- 

- [ERA5 Variables](https://ecmwf-models.readthedocs.io/en/latest/variables_era5.html)

---

# ERA5

ERA5 is the fifth generation ECMWF atmospheric reanalysis of the global climate covering the period from January 1940 to present. ERA5 is produced by the Copernicus Climate Change Service (C3S) at ECMWF.

ERA5 provides hourly estimates of a large number of atmospheric, land and oceanic climate variables. The data cover the Earth on a 31km grid and resolve the atmosphere using 137 levels from the surface up to a height of 80km. ERA5 includes information about uncertainties for all variables at reduced spatial and temporal resolutions.

**ERA5 data has a typical latency of about 5 days (ERA5T)** for near real-time data, providing preliminary hourly information before it's fully quality-controlled and finalized as the main ERA5 dataset, which can take months to fully consolidate. This means you can access recent data for about the last five days with a slight delay, suitable for detailed analysis of recent conditions, but be aware it's subject to revision. 

- Key points about ERA5 latency:
  - **ERA5T (T for 'T'emporary/Preliminary)**: This is the initial release, available up to about 5 days before the present, and is used for applications needing recent data.
  - **Final ERA5**: The full, verified dataset is released later, often with a delay of a couple of months from the current date.

# ERA5 hourly time-series data

- 17 Mar 2025
  - Please be aware that the generation of this dataset is using an alternative source for the ERA5 data and may be subject to changes over time (e.g. file format, data file structure, deprecation etc). This dataset should therefore be regarded as **“experimental”** and is not recommended for use in a production environment.
  - Notification of changes via this catalogue entry banner and/or in the [Forum](https://forum.ecmwf.int/) will be provided on best efforts.

# ERA5 hourly time-series data: Product User Guide

- [ERA5 hourly time-series data: Product User Guide](https://confluence.ecmwf.int/pages/viewpage.action?pageId=505390919)
  - ERA5 is the fifth generation ECMWF reanalysis for the global climate and weather and currently covers the past 80 years. It covers the period from January 1940 to the present with hourly sampling and continues to be extended forward with 5 days behind real time.
  - This dataset presented here is a subset of some parameters of the full CDS ERA5 single levels hourly dataset on 0.25 degrees resolution and is stored in Analysis Ready Cloud Optimised (ARCO) format, which has been implemented for retrieving long time-series for a single point in a efficient way. It is this source of ERA5 data that is used by the ERA-Explorer to ensure response times required for the interactive web-application.

## Spatial grid

The atmospheric parameters have a grid of 0.25 degrees as the wave parameters have a grid of 0.5 degrees.

> Please be aware when you select a "Location" on the form, the latitude and longitude values are rounded to the closest neighborhood point of the 0.25 degrees grid.

## Temporal frequency

Temporal frequency of the data is **hourly and every day has 24 hourly steps**. 

## Data format

In the CDS, there are the options of retrieving the data in **netCDF format** or **CSV format**.

## Data update frequency

- ERA5 is updated on a daily basis. New daily data in ERA5 becomes available in ERA5 Time Series in a few hours.
- Variations in delivery times may occur due to the non-operational nature of this CDS service, as issues may arise which cause delays.

# Scripts

- [ERA5 hourly time-series data on single levels from 1940 to present](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels-timeseries?tab=download)

{% fold info @run_downloand_ERA5_time_series.sh %}
```sh
#!/bin/bash

#------------------------------------------------#
# Author:         wpsze
# Email:          
# File:           run_downloand_ERA5_time_series.sh
# Date:           
# Version:        0.0 
# Description:    The purpose of the script
# Copyright (C):  2026 All rights reserved
#------------------------------------------------#

#set -e # stop the shell on first error
#set -u # fail when using an undefined variable
#set -x # echo script lines as they are executed
set -o pipefail

source /home/wpsze/micromamba/bin/activate venv #micromamba activate venv
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

# Get the system date in yyyy-mm-dd format
DEFAULT_DATE=$(date -d"-5 day" +%Y-%m-%d)

echo "Shell Wrapper: Preparing to download data for $DEFAULT_DATE"

# Create folder
export target_dir=${DEFAULT_DATE:0:7}/${DEFAULT_DATE}
mkdir -p ${target_dir}
cd ${target_dir}

# Check if the file exists
if [ -f "${DEFAULT_DATE}.csv" ]; then
    echo "Check failed: ${DEFAULT_DATE}.csv already exists."
    echo "Exiting to avoid duplicate download."
    exit 0
fi

# Execute the python script
python3 ${SCRIPT_DIR}/download_ERA5_time_series.py "$DEFAULT_DATE"

# unzip file
unzip ${DEFAULT_DATE}.zip

# rename file
mv reanalysis-era5* ${DEFAULT_DATE}.csv

# remove zip fle
rm ${DEFAULT_DATE}.zip
```
{% endfold %}

{% fold info @download_ERA5_time_series.py %}
```python
import cdsapi
import argparse
import sys

def main():
    # Setup argument parser
    parser = argparse.ArgumentParser(description="Download ERA5 data for a specific date.")
    parser.add_argument(
        "date", 
        help="Date in yyyy-mm-dd format"
    )
    
    args = parser.parse_args()
    date_str = args.date
    target_file = f"{date_str}.zip"

    print(f"Requesting data for: {date_str}...")

    client = cdsapi.Client()

    dataset = "reanalysis-era5-single-levels-timeseries"
    request = {
        "variable": [
            "2m_dewpoint_temperature",
            "10m_wind_gust_since_previous_post_processing",
            "mean_sea_level_pressure",
            "skin_temperature",
            "surface_pressure",
            "surface_solar_radiation_downwards",
            "sea_surface_temperature",
            "surface_thermal_radiation_downwards",
            "2m_temperature",
            "total_precipitation",
            "10m_u_component_of_wind",
            "10m_v_component_of_wind",
            "100m_u_component_of_wind",
            "100m_v_component_of_wind",
            #"mean_wave_direction",
            #"mean_wave_period",
            #"significant_height_of_combined_wind_waves_and_swell"
        ],
        "location": {"longitude": 117, "latitude": 36.5},
        "date": [date_str],
        "data_format": "csv"
    }

    try:
        client.retrieve(dataset, request, target_file)
        print(f"Successfully saved to {target_file}")
    except Exception as e:
        print(f"An error occurred: {e}")
        sys.exit(1)

if __name__ == "__main__":
    main()
```
{% endfold %}