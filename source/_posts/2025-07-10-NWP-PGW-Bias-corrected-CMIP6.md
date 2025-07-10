---
layout: post
title: NWP | PGW + Bias-corrected-CMIP6
categories: [NWP]
tags: [MPAS, NWP, WRF, PGW, ERA5, GFS, GRIB, NetCDF, CMIP6]
author: wpsze
date: 2025-07-10 15:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/kcuHfQW.png
banner_img: https://i.imgur.com/kcuHfQW.png
---

- [**Pseudo-Global Warming (PGW) - Future Projection**](https://waipangsze.github.io/2024/09/30/PGW/)
- [**NWP | Pseudo-Global-Warming (PGW) hands-on | ERA5 | GFS**](https://waipangsze.github.io/2025/02/03/NWP-PGW-hands-on-ERA5-GFS/)
- [**NWP | PGW | To prepare the ERA5 GRIB file**](https://waipangsze.github.io/2025/02/05/NWP-PGW-To-prepare-the-ERA5-GRIB-file/)

---

# Bias-corrected CMIP6

- [Bias-corrected CMIP6 global dataset for dynamical downscaling of the Earth’s historical and future climate (1979–2100)](https://www.scidb.cn/en/detail?dataSetId=791587189614968832)
  - The bias-corrected data have an ERA5-based mean climate and interannual variance, but with a non-linear trend from the ensemble mean of the 18 CMIP6 models. The dataset spans the historical time period 1979–2014 and future scenarios (SSP1-2.6, SSP2-4.5 and SSP5-8.5) for 2015–2100 with a horizontal grid spacing of (1.25° × 1.25°) at six-hourly intervals.
  - **The complete dataset is about 1.9 TB in size**.
  - ![](https://i.imgur.com/qtwtIgy.png)

![](https://i.imgur.com/kxyrWyX.png)

# Calculate delta

{% fold info @compute_delta.sh %}
```sh
#!/bin/bash

#------------------------------------------------#
# Author:         wpsze
# Email:          wpsze@gmail.com
# File:           generate-delta.sh
# Date:           2025-07-10 11:58:43
# Version:        0.0 
# Description:    The purpose of the script
# Copyright (C):  2025 All rights reserved
#------------------------------------------------#

set -e # stop the shell on first error
#set -u # fail when using an undefined variable
#set -x # echo script lines as they are executed
set -o pipefail

# ==============================================================================
# Script Name: compute_delta.sh
# Description: Computes the delta between future and historical NetCDF files.
# Author: wpsze 
# Date: 2025-07-10
# Usage: ./compute_delta.sh 
# ==============================================================================

source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate venv
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

# ==============================================================================
# Define directories
# ==============================================================================
HIST_DIR="/home/wpsze/PGW/Bias-corrected-CMIP6/historical/"
FUT_DIR="/home/wpsze/PGW/Bias-corrected-CMIP6/ssp585/"
OUT_DIR="/home/wpsze/PGW/Bias-corrected-CMIP6/calculate-delta/output/"

# Create output and temp directories if they don't exist
mkdir -p "$OUT_DIR"

# ==============================================================================
# Function Definitions
# ==============================================================================

#
# Function to compute delta of atmosphric variables between future and historical NetCDF files
# File name: atm_hist_2013_12.nc4, atm_ssp585_2087_06.nc4
# 
compute_atm_delta(){
    # Extract yyyymm from filename
    filename=$(basename "$hist_file")
    yyyymm=${filename%.nc4}

    yyyy=${yyyymm:9:4}
    mm=${yyyymm:14:2}
    yyyy_add_50=$((${yyyy}+86))

    echo ${yyyy}, ${mm}, ${yyyy_add_50}
    #return 0 # Optional: returns an exit status

    # Check if corresponding future file exists
    fut_file="$FUT_DIR/atm_ssp585_${yyyy_add_50}_${mm}.nc4"
    if [ -f "$fut_file" ]; then
        # Define temporary and output files
        out_file="$OUT_DIR/delta_atm_${yyyy_add_50}_${yyyy}_${mm}.nc"

        if [ -f "$out_file" ]; then
            echo "${out_file} exists. "
        else
            # Compute delta (future - historical)
            # Use the CDO option -b F32 or -b F64 to increase the output precision.
            cdo -b F32 sub "$fut_file" "$hist_file" "$out_file"
            
            # Add metadata
            # cdo -L setattribute,description="Delta (future - historical) for $yyyy" "$out_file" "$out_file.tmp"
            
            echo "Generated delta for ${yyyy_add_50}_${yyyy}_${mm}: $out_file"
        fi
    else
        echo "No matching future file for ${yyyymm} and future ${yyyy_add_50}. "
    fi
}

# 
# Function to compute delta of land variables between future and historical NetCDF files
# File name: lnd.historical.199006.nc, lnd.ssp585.202604.nc
# 
compute_land_delta(){
    # Extract yyyymm from filename
    filename=$(basename "$hist_file")
    yyyymm=${filename%.nc}

    yyyy=${yyyymm:15:4}
    mm=${yyyymm:19:2}
    yyyy_add_50=$((${yyyy}+86))

    echo ${yyyy}, ${mm}, ${yyyy_add_50}
    # return 0 # Optional: returns an exit status

    # Check if corresponding future file exists
    fut_file="$FUT_DIR/lnd.ssp585.${yyyy_add_50}${mm}.nc"
    if [ -f "$fut_file" ]; then
        # Define temporary and output files
        out_file="$OUT_DIR/delta_land_${yyyy_add_50}_${yyyy}_${mm}.nc"

        if [ -f "$out_file" ]; then
            echo "${out_file} exists. "
        else
            # Compute delta (future - historical)
            # Use the CDO option -b F32 or -b F64 to increase the output precision.
            cdo -b F32 sub "$fut_file" "$hist_file" "$out_file"
            
            # Add metadata
            # cdo -L setattribute,description="Delta (future - historical) for $yyyy" "$out_file" "$out_file.tmp"
            
            echo "Generated delta for ${yyyy_add_50}_${yyyy}_${mm}: $out_file"
        fi
    else
        echo "No matching future file for ${yyyymm} and future ${yyyy_add_50}. "
    fi
}

# ==============================================================================
# Main Execution
# ==============================================================================

num_process=0

# # Loop through historical files (Atmosphere, *.nc4)
for hist_file in "$HIST_DIR"/*.nc4; do
    # # Call the delta computation function in background
    compute_atm_delta &
    num_process=$((num_process+1))

    # Wait for all background processes to finish
    if [[ ${num_process} == 68 ]]; then
        wait
        num_process=0
    fi
done

# # Loop through historical files (Land, *.nc)
# for hist_file in "$HIST_DIR"/*.nc; do
#     # # Call the delta computation function in background
#     compute_land_delta &
#     num_process=$((num_process+1))

#     # Wait for all background processes to finish
#     if [[ ${num_process} == 10 ]]; then
#         wait
#         num_process=0
#     fi
# done

wait
echo " "
echo "Delta computation completed."
```
{% endfold %}

{% fold info @compute_mean_delta.sh %}
```sh
#!/bin/bash

#------------------------------------------------#
# Author:         wpsze
# Email:          wpsze@gmail.com
# File:           compute_mean_delta.sh
# Date:           2025-07-10 13:49:58
# Version:        0.0 
# Description:    The purpose of the script
# Copyright (C):  2025 All rights reserved
#------------------------------------------------#

#set -e # stop the shell on first error
#set -u # fail when using an undefined variable
#set -x # echo script lines as they are executed
set -o pipefail


source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate venv
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

# ==============================================================================
# Script Name: compute_mean_delta.sh
# Description: Computes the mean of delta NetCDF files.
# Author: wpsze
# Date: 2025-07-10
# Usage: ./compute_mean_delta.sh # path/to/delta/files /path/to/output/file.nc
# ==============================================================================

# ==============================================================================
# Function Definitions
# ==============================================================================

# Function to compute mean of delta files
compute_mean() {
    local delta_dir="$1"
    local output_file="$2"
    local nc_name="$3"
    local target_MM="$4"
    local outfile_name="$5"

    # Check if the delta directory exists
    if [[ ! -d "$delta_dir" ]]; then
        echo "Error: Delta directory '$delta_dir' does not exist."
        exit 1
    fi

    # Create a temporary file list for CDO
    temp_file_list="temp_file_list.txt"
    rm -f "$temp_file_list"

    # Loop through delta files and add to the list
    for delta_file in "$delta_dir"/delta_${nc_name}*${target_MM}.nc; do
        if [[ -f "$delta_file" ]]; then
            echo "$delta_file" >> "$temp_file_list"
        fi
    done

    # Check if any delta files were found
    if [[ ! -s "$temp_file_list" ]]; then
        echo "Error: No delta files found in '$delta_dir'."
        exit 1
    fi

    if [[ -f "${output_file}/${outfile_name}_${nc_name}.nc" ]]; then
        echo "${output_file}/${outfile_name}_${nc_name}.nc exists. "
    else
        # Compute the mean of delta files
        local tmp_merged="${output_file}/tmp_${outfile_name}_${nc_name}.nc"
        cdo -L -M mergetime $(cat "$temp_file_list") "${tmp_merged}" || {
            echo "Error merging files with -M mergetime"
            return 1
        }
        # Compute time mean on merged file
        cdo -L timmean "${tmp_merged}" "${output_file}/${outfile_name}_${nc_name}.nc" || {
            echo "Error computing time mean for merged files"
            rm "$tmp_merged"
            return 1
        }
        # Clean up temporary merged file
        rm "$tmp_merged"
    fi

    # Clean up
    rm -f "$temp_file_list"

    echo "Mean delta computed and saved to '$output_file'."
}

# ==============================================================================
# Main Execution
# ==============================================================================


# Assign arguments to variables
# DELTA_DIR="$1"
# OUTPUT_FILE="$2"
# target_MM="$3"
DELTA_DIR="/home/wpsze/mpas/MPAS-project/PGW/Bias-corrected-CMIP6/calculate-delta/output/"
OUTPUT_FILE="/home/wpsze/mpas/MPAS-project/PGW/Bias-corrected-CMIP6/calculate-delta/output-mean/"

tmp_dir="/home/wpsze/mpas/MPAS-project/PGW/Bias-corrected-CMIP6/calculate-delta/delta-2100-2070/"
rm -r ${tmp_dir}
mkdir -p ${tmp_dir}

for target_MM in {01..12}; do
    echo " ... Month = ${target_MM} ... "

    # Loop through delta files of the specified year and add to the list
    outfile_name="delta_signal_2070_2100_${target_MM}"
    for yyyy in {2070..2100}; do
        ln -sf ${DELTA_DIR}/delta_atm_${yyyy}*${target_MM}.nc ${tmp_dir}
        ln -sf ${DELTA_DIR}/delta_land_${yyyy}*${target_MM}.nc ${tmp_dir}
    done

    # Assign year for file name
    # yyyy and mm
    # delta_atm_2069_1983_04.nc
    # delta_land_2100_2014_12.nc

    # Create directory
    mkdir -p ${OUTPUT_FILE}

    # Call the compute_mean function
    # ---- Land -------
    nc_name="land"
    compute_mean "${tmp_dir}" "${OUTPUT_FILE}" "${nc_name}" "${target_MM}" "${outfile_name}"

    # ---- atm -------
    nc_name="atm"
    compute_mean "${tmp_dir}" "${OUTPUT_FILE}" "${nc_name}" "${target_MM}" "${outfile_name}"


done

# End of script
```
{% endfold %}

{% fold info @delta_signal_2070_2100_01_atm.nc %}
```log
netcdf delta_signal_2070_2100_01_atm {
dimensions:
	time = UNLIMITED ; // (1 currently)
	bnds = 2 ;
	lon = 288 ;
	lat = 145 ;
	lev = 14 ;
variables:
	double time(time) ;
		time:standard_name = "time" ;
		time:long_name = "time" ;
		time:bounds = "time_bnds" ;
		time:units = "days since 1850-1-1 00:00:00" ;
		time:calendar = "proleptic_gregorian" ;
		time:axis = "T" ;
	double time_bnds(time, bnds) ;
	double lon(lon) ;
		lon:standard_name = "longitude" ;
		lon:long_name = "longitude" ;
		lon:units = "degrees_east" ;
		lon:axis = "X" ;
	double lat(lat) ;
		lat:standard_name = "latitude" ;
		lat:long_name = "latitude" ;
		lat:units = "degrees_north" ;
		lat:axis = "Y" ;
	double lev(lev) ;
		lev:standard_name = "air_pressure" ;
		lev:long_name = "pressure" ;
		lev:units = "Pa" ;
		lev:positive = "down" ;
		lev:axis = "Z" ;
	float hur(time, lev, lat, lon) ;
		hur:standard_name = "specific_humidity" ;
		hur:long_name = "Specific Humidity" ;
		hur:units = "1" ;
		hur:_FillValue = -32767.f ;
		hur:missing_value = -32767.f ;
		hur:cell_methods = "area: mean time: point" ;
		hur:comment = "Specific humidity is the mass fraction of water vapor in (moist) air." ;
		hur:history = "2019-07-21T06:26:14Z altered by CMOR: Reordered dimensions, original order: time lat lon plev. 2019-07-21T06:26:14Z altered by CMOR: replaced missing value flag (-9e+33) with standard missing value (1e+20). 2019-07-21T06:26:14Z altered by CMOR: Converted type from \'d\' to \'f\'. 2019-07-21T06:26:14Z altered by CMOR: Inverted axis: lat." ;
	float ps(time, lat, lon) ;
		ps:standard_name = "surface_air_pressure" ;
		ps:long_name = "Surface Air Pressure" ;
		ps:units = "Pa" ;
		ps:_FillValue = -32767.f ;
		ps:missing_value = -32767.f ;
		ps:cell_methods = "area: mean time: point" ;
		ps:comment = "surface pressure (not mean sea-level pressure), 2-D field to calculate the 3-D pressure field from hybrid coordinates" ;
		ps:history = "2019-07-21T06:26:14Z altered by CMOR: replaced missing value flag (-9e+33) with standard missing value (1e+20). 2019-07-21T06:26:14Z altered by CMOR: Converted type from \'d\' to \'f\'. 2019-07-21T06:26:14Z altered by CMOR: Inverted axis: lat." ;
	float psl(time, lat, lon) ;
		psl:standard_name = "air_pressure_at_mean_sea_level" ;
		psl:long_name = "Sea Level Pressure" ;
		psl:units = "Pa" ;
		psl:_FillValue = -32767.f ;
		psl:missing_value = -32767.f ;
		psl:cell_methods = "area: mean time: point" ;
		psl:comment = "Sea Level Pressure" ;
		psl:history = "2019-07-21T06:26:14Z altered by CMOR: replaced missing value flag (-9e+33) with standard missing value (1e+20). 2019-07-21T06:26:14Z altered by CMOR: Converted type from \'d\' to \'f\'. 2019-07-21T06:26:14Z altered by CMOR: Inverted axis: lat." ;
	float ta(time, lev, lat, lon) ;
		ta:standard_name = "air_temperature" ;
		ta:long_name = "Air Temperature" ;
		ta:units = "K" ;
		ta:_FillValue = -32767.f ;
		ta:missing_value = -32767.f ;
		ta:cell_methods = "area: mean time: point" ;
		ta:comment = "Air Temperature" ;
		ta:history = "2019-07-21T06:26:14Z altered by CMOR: Reordered dimensions, original order: time lat lon plev. 2019-07-21T06:26:14Z altered by CMOR: replaced missing value flag (-9e+33) with standard missing value (1e+20). 2019-07-21T06:26:14Z altered by CMOR: Converted type from \'d\' to \'f\'. 2019-07-21T06:26:14Z altered by CMOR: Inverted axis: lat." ;
	float tos(time, lat, lon) ;
		tos:standard_name = "sea_surface_temperature" ;
		tos:long_name = "Sea Surface Temperature" ;
		tos:units = "degC" ;
		tos:_FillValue = -32767.f ;
		tos:missing_value = -32767.f ;
		tos:cell_methods = "area: mean where sea time: point" ;
		tos:comment = "Temperature of upper boundary of the liquid ocean, including temperatures below sea-ice and floating ice shelves." ;
		tos:original_name = "tos" ;
		tos:history = "2019-07-21T06:23:24Z altered by CMOR: replaced missing value flag (-9e+33) with standard missing value (1e+20)." ;
	float ua(time, lev, lat, lon) ;
		ua:standard_name = "eastward_wind" ;
		ua:long_name = "Eastward Wind" ;
		ua:units = "m s-1" ;
		ua:_FillValue = -32767.f ;
		ua:missing_value = -32767.f ;
		ua:cell_methods = "time: point" ;
		ua:comment = "Zonal wind (positive in a eastward direction)." ;
		ua:history = "2019-08-21T17:13:47Z altered by CMOR: Reordered dimensions, original order: time lat lon lev. 2019-08-21T17:13:47Z altered by CMOR: replaced missing value flag (-9e+33) and corresponding data with standard missing value (1e+20). 2019-08-21T17:13:47Z altered by CMOR: Inverted axis: lev. 2019-08-21T17:13:47Z altered by CMOR: Inverted axis: lat." ;
	float va(time, lev, lat, lon) ;
		va:standard_name = "northward_wind" ;
		va:long_name = "Northward Wind" ;
		va:units = "m s-1" ;
		va:_FillValue = -32767.f ;
		va:missing_value = -32767.f ;
		va:cell_methods = "time: point" ;
		va:comment = "Meridional wind (positive in a northward direction)." ;
		va:history = "2019-08-21T17:13:47Z altered by CMOR: Reordered dimensions, original order: time lat lon lev. 2019-08-21T17:13:47Z altered by CMOR: replaced missing value flag (-9e+33) and corresponding data with standard missing value (1e+20). 2019-08-21T17:13:47Z altered by CMOR: Inverted axis: lev. 2019-08-21T17:13:47Z altered by CMOR: Inverted axis: lat." ;
	float zg(time, lev, lat, lon) ;
		zg:standard_name = "geopotential_height" ;
		zg:long_name = "Geopotential Height" ;
		zg:units = "m" ;
		zg:_FillValue = -32767.f ;
		zg:missing_value = -32767.f ;
		zg:cell_methods = "area: mean time: point" ;
		zg:comment = "Geopotential is the sum of the specific gravitational potential energy relative to the geoid and the specific centripetal potential energy. Geopotential height is the geopotential divided by the standard acceleration due to gravity. It is numerically similar to the altitude (or geometric height) and not to the quantity with standard name height, which is relative to the surface." ;
		zg:history = "2019-07-21T06:26:14Z altered by CMOR: Reordered dimensions, original order: time lat lon plev. 2019-07-21T06:26:14Z altered by CMOR: replaced missing value flag (-9e+33) with standard missing value (1e+20). 2019-07-21T06:26:14Z altered by CMOR: Converted type from \'d\' to \'f\'. 2019-07-21T06:26:14Z altered by CMOR: Inverted axis: lat." ;
```
{% endfold %}

{% fold info @lat/lon/lev %}
```log
data:

 lat = -90, -88.75, -87.5, -86.25, -85, -83.75, -82.5, -81.25, -80, -78.75, 
    -77.5, -76.25, -75, -73.75, -72.5, -71.25, -70, -68.75, -67.5, -66.25, 
    -65, -63.75, -62.5, -61.25, -60, -58.75, -57.5, -56.25, -55, -53.75, 
    -52.5, -51.25, -50, -48.75, -47.5, -46.25, -45, -43.75, -42.5, -41.25, 
    -40, -38.75, -37.5, -36.25, -35, -33.75, -32.5, -31.25, -30, -28.75, 
    -27.5, -26.25, -25, -23.75, -22.5, -21.25, -20, -18.75, -17.5, -16.25, 
    -15, -13.75, -12.5, -11.25, -10, -8.75, -7.5, -6.25, -5, -3.75, -2.5, 
    -1.25, 0, 1.25, 2.5, 3.75, 5, 6.25, 7.5, 8.75, 10, 11.25, 12.5, 13.75, 
    15, 16.25, 17.5, 18.75, 20, 21.25, 22.5, 23.75, 25, 26.25, 27.5, 28.75, 
    30, 31.25, 32.5, 33.75, 35, 36.25, 37.5, 38.75, 40, 41.25, 42.5, 43.75, 
    45, 46.25, 47.5, 48.75, 50, 51.25, 52.5, 53.75, 55, 56.25, 57.5, 58.75, 
    60, 61.25, 62.5, 63.75, 65, 66.25, 67.5, 68.75, 70, 71.25, 72.5, 73.75, 
    75, 76.25, 77.5, 78.75, 80, 81.25, 82.5, 83.75, 85, 86.25, 87.5, 88.75, 90 ;
}

data:

 lon = 0, 1.25, 2.5, 3.75, 5, 6.25, 7.5, 8.75, 10, 11.25, 12.5, 13.75, 15, 
    16.25, 17.5, 18.75, 20, 21.25, 22.5, 23.75, 25, 26.25, 27.5, 28.75, 30, 
    31.25, 32.5, 33.75, 35, 36.25, 37.5, 38.75, 40, 41.25, 42.5, 43.75, 45, 
    46.25, 47.5, 48.75, 50, 51.25, 52.5, 53.75, 55, 56.25, 57.5, 58.75, 60, 
    61.25, 62.5, 63.75, 65, 66.25, 67.5, 68.75, 70, 71.25, 72.5, 73.75, 75, 
    76.25, 77.5, 78.75, 80, 81.25, 82.5, 83.75, 85, 86.25, 87.5, 88.75, 90, 
    91.25, 92.5, 93.75, 95, 96.25, 97.5, 98.75, 100, 101.25, 102.5, 103.75, 
    105, 106.25, 107.5, 108.75, 110, 111.25, 112.5, 113.75, 115, 116.25, 
    117.5, 118.75, 120, 121.25, 122.5, 123.75, 125, 126.25, 127.5, 128.75, 
    130, 131.25, 132.5, 133.75, 135, 136.25, 137.5, 138.75, 140, 141.25, 
    142.5, 143.75, 145, 146.25, 147.5, 148.75, 150, 151.25, 152.5, 153.75, 
    155, 156.25, 157.5, 158.75, 160, 161.25, 162.5, 163.75, 165, 166.25, 
    167.5, 168.75, 170, 171.25, 172.5, 173.75, 175, 176.25, 177.5, 178.75, 
    180, 181.25, 182.5, 183.75, 185, 186.25, 187.5, 188.75, 190, 191.25, 
    192.5, 193.75, 195, 196.25, 197.5, 198.75, 200, 201.25, 202.5, 203.75, 
    205, 206.25, 207.5, 208.75, 210, 211.25, 212.5, 213.75, 215, 216.25, 
    217.5, 218.75, 220, 221.25, 222.5, 223.75, 225, 226.25, 227.5, 228.75, 
    230, 231.25, 232.5, 233.75, 235, 236.25, 237.5, 238.75, 240, 241.25, 
    242.5, 243.75, 245, 246.25, 247.5, 248.75, 250, 251.25, 252.5, 253.75, 
    255, 256.25, 257.5, 258.75, 260, 261.25, 262.5, 263.75, 265, 266.25, 
    267.5, 268.75, 270, 271.25, 272.5, 273.75, 275, 276.25, 277.5, 278.75, 
    280, 281.25, 282.5, 283.75, 285, 286.25, 287.5, 288.75, 290, 291.25, 
    292.5, 293.75, 295, 296.25, 297.5, 298.75, 300, 301.25, 302.5, 303.75, 
    305, 306.25, 307.5, 308.75, 310, 311.25, 312.5, 313.75, 315, 316.25, 
    317.5, 318.75, 320, 321.25, 322.5, 323.75, 325, 326.25, 327.5, 328.75, 
    330, 331.25, 332.5, 333.75, 335, 336.25, 337.5, 338.75, 340, 341.25, 
    342.5, 343.75, 345, 346.25, 347.5, 348.75, 350, 351.25, 352.5, 353.75, 
    355, 356.25, 357.5, 358.75 ;
}

data:

 lev = 100000, 92500, 85000, 70000, 60000, 50000, 40000, 30000, 25000, 20000, 
    15000, 10000, 7000, 5000 ;
}
```
{% endfold %}
