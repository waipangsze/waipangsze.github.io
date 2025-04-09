---
layout: post
title: NWP | Pseudo-Global-Warming (PGW) hands-on | ERA5 | GFS
categories: [NWP]
tags: [MPAS, NWP, WRF, PGW, ERA5, GFS, GRIB, NetCDF, CMIP6]
author: wpsze
date: 2025-02-03 16:47:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/kcuHfQW.png
banner_img: https://i.imgur.com/kcuHfQW.png
---

# Introduction

Keywords:

- The Pseudo-Global-Warming (**PGW**) approach
- **CMIP6**
- how to convert **ERA5** **GRIB** files to NetCDF format
- GRIB files are the file format used at **ECMWF**.  GRIB is a WMO standard and consists of GRIB Edition 1 and Edition 2

The ERA5 dataset, produced by the European Centre for Medium-Range Weather Forecasts (ECMWF), provides a wealth of climate and weather data. However, the data is often distributed in the GRIB format, which can be less user-friendly than the NetCDF format, commonly used in scientific computing. This blog post will walk you through the process of converting ERA5 GRIB files to NetCDF.

{% note primary %}
Read [**Pseudo-Global Warming (PGW) - Future Projection**](https://waipangsze.github.io/2024/09/30/PGW/)
{% endnote %}

# Motivation

Climate change is one of the most pressing issues of our time, and understanding its impacts requires access to high-quality climate data. The ERA5 dataset provides crucial information on past and present climate conditions, helping researchers analyze trends and signals related to climate change. However, the ERA5 data is often stored in GRIB format, which can be challenging to work with. By converting ERA5 GRIB files to NetCDF format, we can more easily integrate climate change signals into ERA5.

By applying the Pseudo-Global-Warming (PGW) approach, you can use the CMIP6 climate change signals to generate a difference and then add this to the initial conditions, such as those provided by ERA5. 

# Main Script

{% fold info @loop.sh %}
```sh
#!/bin/bash

ym="20220630"

for iday in {0..360..3}; do
    # Next_day=$(date +%Y-%m-%d_%H:%M:%S -d "$YYYYMMDD + 1 hour")
    export ERA5_datetime=$(date +%Y%m%d%H -d "$ym + $iday hour")
    echo ${ERA5_datetime}

    ./PGW_main.sh
done
```
{% endfold %}

{% fold info @PGW_main.sh %}
```sh
#!/bin/bash

#------------------------------------------------#
#Author:         wpsze
#Email：         wpsze
#date:           2025-02-10 09:45:06
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2025 All rights reserved
#------------------------------------------------#

export ERA5_datetime=${ERA5_datetime:-"2022063000"}
export ERA5_path=${ERA5_path:-"/home/wpsze/ERA5/"}
export PGW_ERA5_path=${PGW_ERA5_path:-"/home/wpsze/PGW/ERA5/"}              # save future projection of ERA5 IC
export PGW_script_path=${PGW_script_path:-"/home/wpsze/PGW/"}

export cmip6_signal_file=${cmip6_signal_file:-"/home/wpsze/CMIP6_gw_signal_difference_data/diff_ssp585far-hist_07.nc"}

export yyyymmddhh="${ERA5_datetime}"
export yyyymm="${ERA5_datetime:0:6}"
export yyyymmdd="${ERA5_datetime:0:8}"
export hh="${ERA5_datetime:8:2}"

source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate PGW

mkdir -p tmp
cd tmp

rm *idx
ln -sf ${ERA5_path}/${yyyymm}/${yyyymmdd}/ERA5-0p25-PL-${yyyymmddhh}.grib
ln -sf ${ERA5_path}/${yyyymm}/${yyyymmdd}/ERA5-0p25-SL-${yyyymmddhh}.grib

# Convert ERA5.grib to .nc
if [[ ! -f "ERA5-0p25-SL-${yyyymmddhh}.nc" ]]; then
    echo "ERA5-0p25-SL/PL-${yyyymmddhh}.nc doesn't exist, generating them ... "
    time python3 ${PGW_script_path}/grib2netcdf.py
else
    echo "ERA5-0p25-SL/PL-${yyyymmddhh}.nc exist."
fi

# rename/rebuild singal.nc
if [[ ! -f "signal.nc" ]]; then
    echo "singal.nc doesn't exits, soft-link and rebuild ..."
    ln -sf ${cmip6_signal_file} original-signal.nc
    time python3 ${PGW_script_path}/rename_signal.py
else 
    echo "signal.nc exists."
fi

# regrid signal.nc (1.25deg) to 0.25deg 
if [[ ! -f "signal_0p25.nc" ]]; then
    cdo remapbil,r1440x721 signal.nc signal_0p25.nc
else
    echo "signal_0p25.nc exists."
fi

# Add singal.nc into ERA5.nc
time python3 ${PGW_script_path}/2d_interp.py
time python3 ${PGW_script_path}/3d_interp.py
# time python3 ${PGW_script_path}/3d_interp_with_uv.py

# Convert nc file to grib file
cp ERA5-0p25-SL-${yyyymmddhh}.grib original-SL.grib
cp ERA5-0p25-PL-${yyyymmddhh}.grib original-PL.grib
time sh ${PGW_script_path}/netcdf2grib.sh
# time sh ${PGW_script_path}/netcdf2grib_with_uv.sh

# cleanup
rm original-SL.grib original-PL.grib
rm original-SL_without_vars.grib original-PL_without_vars.grib
rm skt_final.grib skt.grib skt_nan.nc skt.nc 
rm sst_final.grib sst.grib sst_nan.nc sst.nc
rm t_final.grib t.grib t_lev.grib t_nan.nc t.nc
rm r_final.grib r.grib r_lev.grib r_nan.nc r.nc
rm *idx

# soft-link (Finally, have to move all girb to there!)
mkdir -p ${PGW_ERA5_path}/${yyyymm}/${yyyymmdd}/
current_dir=$(pwd)
ln -sf ${current_dir}/new_ERA5-0p25-SL-${yyyymmddhh}.grib ${PGW_ERA5_path}/${yyyymm}/${yyyymmdd}/ERA5-0p25-SL-${yyyymmddhh}.grib
ln -sf ${current_dir}/new_ERA5-0p25-PL-${yyyymmddhh}.grib ${PGW_ERA5_path}/${yyyymm}/${yyyymmdd}/ERA5-0p25-PL-${yyyymmddhh}.grib
```
{% endfold %}


# Converting ERA5 GRIB to NetCDF: A Step-by-Step Guide

## What You Need

Before we begin, ensure you have the following:

1. **Python**: A popular programming language for data manipulation.
2. **Libraries**: You will need `xarray`, `cfgrib`, and `netCDF4`. You can install them using pip:

   ```bash
   micromamba env create -n PGW
   micromamba activate PGW
   micromamba install python=3.11 -y
   pip install xarray cfgrib netCDF4
   micromamba install scipy -y
   micromamba install conda-forge::cdo -y
   micromamba install conda-forge::wgrib -y
   micromamba install matplotlib -y
   ```

3. **ERA5 GRIB File**: Download your desired ERA5 dataset in GRIB format from the ECMWF website.
4. New tool (seems fine or for checking): [Convert GRIB file to netCDF](https://confluence.ecmwf.int/display/OIFS/How+to+convert+GRIB+to+netCDF) 
   1. `grib_to_netcdf ICMGG_hybrid.grb -o ICMGG_hybrid.nc`
   2. `micromamba install conda-forge::eccodes -y`

## Steps to Convert GRIB to NetCDF

{% fold info @grib2netcdf.py %}
```python
#!/bin/python

#------------------------------------------------#
#Author:         wpsze
#Email：         wpsze
#date:           2025-02-03 17:26:38
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2025 All rights reserved
#------------------------------------------------#
import os
import xarray as xr

# environment variable 
yyyymmddhh = os.environ['yyyymmddhh'] 

#============== SL =============================
grib_file = f'ERA5-0p25-SL-{yyyymmddhh}.grib'
ds = xr.open_dataset(grib_file, engine='cfgrib')

print(ds)

output_file = f'ERA5-0p25-SL-{yyyymmddhh}.nc'
ds.to_netcdf(output_file)

#============== PL =============================
grib_file = f'ERA5-0p25-PL-{yyyymmddhh}.grib'
ds = xr.open_dataset(grib_file, engine='cfgrib')

print(ds)

output_file = f'ERA5-0p25-PL-{yyyymmddhh}.nc'
ds.to_netcdf(output_file)
```
{% endfold %}

## Verify the Conversion

To ensure that the conversion was successful, you can reopen the NetCDF file and inspect its contents.

```python
ds_netcdf = xr.open_dataset(output_file)
print(ds_netcdf)
```

## Output

{% fold info @ERA5-SL.nc (Surface level) %}
```console
<xarray.Dataset> Size: 62MB
Dimensions:              (latitude: 721, longitude: 1440)
Coordinates:
    number               int64 8B ...
    time                 datetime64[ns] 8B ...
    step                 timedelta64[ns] 8B ...
    surface              float64 8B ...
  * latitude             (latitude) float64 6kB 90.0 89.75 89.5 ... -89.75 -90.0
  * longitude            (longitude) float64 12kB 0.0 0.25 0.5 ... 359.5 359.8
    valid_time           datetime64[ns] 8B ...
    depthBelowLandLayer  float64 8B ...
Data variables: (12/15)
    u10                  (latitude, longitude) float32 4MB ...
    v10                  (latitude, longitude) float32 4MB ...
    d2m                  (latitude, longitude) float32 4MB ...
    t2m                  (latitude, longitude) float32 4MB ...
    lsm                  (latitude, longitude) float32 4MB ...
    msl                  (latitude, longitude) float32 4MB ...
    ...                   ...
    rsn                  (latitude, longitude) float32 4MB ...
    sd                   (latitude, longitude) float32 4MB ...
    stl1                 (latitude, longitude) float32 4MB ...
    sp                   (latitude, longitude) float32 4MB ...
    swvl1                (latitude, longitude) float32 4MB ...
    z                    (latitude, longitude) float32 4MB ...
Attributes:
    GRIB_edition:            1
    GRIB_centre:             ecmf
    GRIB_centreDescription:  European Centre for Medium-Range Weather Forecasts
    GRIB_subCentre:          0
    Conventions:             CF-1.7
    institution:             European Centre for Medium-Range Weather Forecasts
    history:                 2025-02-05T09:50 GRIB to CDM+CF via cfgrib-0.9.1...

netcdf ERA5 {
dimensions:
	latitude = 721 ;
	longitude = 1440 ;
variables:
	int64 number ;
		number:long_name = "ensemble member numerical id" ;
		number:standard_name = "realization" ;
	int64 time ;
		time:long_name = "initial time of forecast" ;
		time:standard_name = "forecast_reference_time" ;
	double step ;
		step:long_name = "time since forecast_reference_time" ;
		step:standard_name = "forecast_period" ;
	double surface ;
		surface:long_name = "original GRIB coordinate for key: level(surface)" ;
	double latitude(latitude) ;
		latitude:standard_name = "latitude" ;
		latitude:long_name = "latitude" ;
	double longitude(longitude) ;
		longitude:standard_name = "longitude" ;
		longitude:long_name = "longitude" ;
	double valid_time ;
		valid_time:standard_name = "time" ;
		valid_time:long_name = "time" ;
	float u10(latitude, longitude) ;
		u10:long_name = "10 metre U wind component" ;
		u10:standard_name = "unknown" ;
	float v10(latitude, longitude) ;
		v10:long_name = "10 metre V wind component" ;
		v10:standard_name = "unknown" ;
	float d2m(latitude, longitude) ;
		d2m:long_name = "2 metre dewpoint temperature" ;
		d2m:standard_name = "unknown" ;
	float t2m(latitude, longitude) ;
		t2m:long_name = "2 metre temperature" ;
		t2m:standard_name = "unknown" ;
	float lsm(latitude, longitude) ;
		lsm:long_name = "Land-sea mask" ;
		lsm:standard_name = "land_binary_mask" ;
	float msl(latitude, longitude) ;
		msl:long_name = "Mean sea level pressure" ;
		msl:standard_name = "air_pressure_at_mean_sea_level" ;
	float siconc(latitude, longitude) ;
		siconc:long_name = "Sea ice area fraction" ;
		siconc:standard_name = "sea_ice_area_fraction" ;
	float sst(latitude, longitude) ;
		sst:long_name = "Sea surface temperature" ;
		sst:standard_name = "unknown" ;
	float skt(latitude, longitude) ;
		skt:long_name = "Skin temperature" ;
		skt:standard_name = "unknown" ;
	float rsn(latitude, longitude) ;
		rsn:long_name = "Snow density" ;
		rsn:standard_name = "unknown" ;
	float sd(latitude, longitude) ;
		sd:long_name = "Snow depth" ;
		sd:standard_name = "lwe_thickness_of_surface_snow_amount" ;
	double depthBelowLandLayer ;
		depthBelowLandLayer:long_name = "soil depth" ;
		depthBelowLandLayer:standard_name = "depth" ;
	float stl1(latitude, longitude) ;
		stl1:long_name = "Soil temperature level 1" ;
		stl1:standard_name = "surface_temperature" ;
	float sp(latitude, longitude) ;
		sp:long_name = "Surface pressure" ;
		sp:standard_name = "surface_air_pressure" ;
	float swvl1(latitude, longitude) ;
		swvl1:long_name = "Volumetric soil water layer 1" ;
		swvl1:standard_name = "unknown" ;
	float z(latitude, longitude) ;
		z:long_name = "Geopotential" ;
		z:standard_name = "geopotential" ;
```
{% endfold %}

{% fold info @ERA5-PL.nc (Pressure level) %}
```console
<xarray.Dataset> Size: 922MB
Dimensions:        (isobaricInhPa: 37, latitude: 721, longitude: 1440)
Coordinates:
    number         int64 8B ...
    time           datetime64[ns] 8B ...
    step           timedelta64[ns] 8B ...
  * isobaricInhPa  (isobaricInhPa) float64 296B 1e+03 975.0 950.0 ... 2.0 1.0
  * latitude       (latitude) float64 6kB 90.0 89.75 89.5 ... -89.5 -89.75 -90.0
  * longitude      (longitude) float64 12kB 0.0 0.25 0.5 ... 359.2 359.5 359.8
    valid_time     datetime64[ns] 8B ...
Data variables:
    z              (isobaricInhPa, latitude, longitude) float32 154MB ...
    r              (isobaricInhPa, latitude, longitude) float32 154MB ...
    q              (isobaricInhPa, latitude, longitude) float32 154MB ...
    t              (isobaricInhPa, latitude, longitude) float32 154MB ...
    u              (isobaricInhPa, latitude, longitude) float32 154MB ...
    v              (isobaricInhPa, latitude, longitude) float32 154MB ...
Attributes:
    GRIB_edition:            1
    GRIB_centre:             ecmf
    GRIB_centreDescription:  European Centre for Medium-Range Weather Forecasts
    GRIB_subCentre:          0
    Conventions:             CF-1.7
    institution:             European Centre for Medium-Range Weather Forecasts
    history:                 2025-02-05T09:45 GRIB to CDM+CF via cfgrib-0.9.1...

netcdf ERA5-PL {
dimensions:
	isobaricInhPa = 37 ;
	latitude = 721 ;
	longitude = 1440 ;
variables:
	int64 number ;
		number:long_name = "ensemble member numerical id" ;
		number:standard_name = "realization" ;
	int64 time ;
		time:long_name = "initial time of forecast" ;
		time:standard_name = "forecast_reference_time" ;
	double step ;
		step:long_name = "time since forecast_reference_time" ;
		step:standard_name = "forecast_period" ;
	double isobaricInhPa(isobaricInhPa) ;
		isobaricInhPa:long_name = "pressure" ;
		isobaricInhPa:standard_name = "air_pressure" ;
	double latitude(latitude) ;
		latitude:standard_name = "latitude" ;
		latitude:long_name = "latitude" ;
	double longitude(longitude) ;
		longitude:standard_name = "longitude" ;
		longitude:long_name = "longitude" ;
	double valid_time ;
		valid_time:standard_name = "time" ;
		valid_time:long_name = "time" ;
	float z(isobaricInhPa, latitude, longitude) ;
		z:long_name = "Geopotential" ;
		z:standard_name = "geopotential" ;
	float r(isobaricInhPa, latitude, longitude) ;
		r:long_name = "Relative humidity" ;
		r:standard_name = "relative_humidity" ;
	float q(isobaricInhPa, latitude, longitude) ;
		q:long_name = "Specific humidity" ;
		q:standard_name = "specific_humidity" ;
	float t(isobaricInhPa, latitude, longitude) ;
		t:long_name = "Temperature" ;
		t:standard_name = "air_temperature" ;
	float u(isobaricInhPa, latitude, longitude) ;
		u:long_name = "U component of wind" ;
		u:standard_name = "eastward_wind" ;
	float v(isobaricInhPa, latitude, longitude) ;
		v:long_name = "V component of wind" ;
		v:standard_name = "northward_wind" ;
```
{% endfold %}

## Additional Resources

- [xarray Documentation](https://docs.xarray.dev/en/stable/)
- [cfgrib Documentation](https://cfgrib.readthedocs.io/en/latest/)
- [ERA5 Data Access](https://www.ecmwf.int/en/forecasts/datasets/reanalysis-datasets/era5)

---

# Interpolating 2D SST/SKT from Signal NetCDF to ERA5 NetCDF

Here’s a Python script for interpolating Sea Surface Temperature (SST) and Surface Skin Temperature (SKT) from a signal NetCDF file to match the ERA5 grid. This process involves horizontal interpolation to ensure that the temperatures align accurately with the finer resolution of the ERA5 dataset. Accurate temperature data is crucial for climate modeling and analysis. By interpolating these temperatures, we can effectively integrate them into broader climate studies.

## Manipulate signal.nc

{% note primary %}
Modify `signal.nc` because the original signal.nc is not properly defined in a format recognized by CDO, making it impossible to apply CDO command tools.
{% endnote %}

{% fold info @signal.nc %}
```console
netcdf signal {
dimensions:
	ncl0 = 14 ;
	ncl1 = 145 ;
	ncl2 = 288 ;
	ncl3 = 145 ;
	ncl4 = 288 ;
	ncl5 = 14 ;
	ncl6 = 145 ;
	ncl7 = 288 ;
	ncl8 = 14 ;
	ncl9 = 145 ;
	ncl10 = 288 ;
	ncl11 = 14 ;
	ncl12 = 145 ;
	ncl13 = 288 ;
	ncl14 = 14 ;
	ncl15 = 145 ;
	ncl16 = 288 ;
variables:
	float diff_ta(ncl0, ncl1, ncl2) ;
	float diff_ts(ncl3, ncl4) ;
	float diff_rh(ncl5, ncl6, ncl7) ;
	float diff_ua(ncl8, ncl9, ncl10) ;
	float diff_va(ncl11, ncl12, ncl13) ;
	double lev(ncl14) ;
	double lat(ncl15) ;
	double lon(ncl16) ;
}
```
{% endfold %}

## cdo

Error: `cdo    remapbil (Abort): Unsupported generic coordinates (Variable: diff_ta)!`

It is because **lat/lon/lev are not well-defined**.

- The original signal.nc is not well-defined file...
- have to assign attrs of dimension,
  - isobaricInhPa = 37 ;
  - latitude = 721 ;
  - longitude = 1440 ;
- Probably you need to fix the missing values, too.

{% fold info @rename_signal.py %}
```python
#!/bin/python

#------------------------------------------------#
#Author:         wpsze
#Email：         wpsze
#date:           2025-02-07 17:37:01
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2025 All rights reserved
#------------------------------------------------#

import numpy as np
import xarray as xr

# Load the signal and ERA5 NetCDF files
signal_nc = xr.open_dataset('signal.nc')      # Replace with actual signal file path

signal_nc = signal_nc.rename({'ncl0': 'level', 'ncl1': 'latitude', 'ncl2': 'longitude',
                              'ncl3': 'latitude', 'ncl4': 'longitude',
                              'ncl5': 'level', 'ncl6': 'latitude', 'ncl7': 'longitude',
                              'ncl8': 'level', 'ncl9': 'latitude', 'ncl10': 'longitude',
                              'ncl11': 'level', 'ncl12': 'latitude', 'ncl13': 'longitude',
                              'ncl14': 'level', 'ncl15': 'latitude', 'ncl16': 'longitude',})

# Define dimensions
lev = signal_nc['lev'].values  # Level dimension
lat = signal_nc['lat'].values  # Latitude dimension
lon = signal_nc['lon'].values  # Longitude dimension

# Create DataArrays
diff_ta = xr.DataArray(
    signal_nc['diff_ta'],
    dims=["level", "latitude", "longitude"],
    coords={"level": lev, "latitude": lat, "longitude": lon}
)

diff_rh = xr.DataArray(
    signal_nc['diff_rh'],
    dims=["level", "latitude", "longitude"],
    coords={"level": lev, "latitude": lat, "longitude": lon}
)

diff_ua = xr.DataArray(
    signal_nc['diff_ua'],
    dims=["level", "latitude", "longitude"],
    coords={"level": lev, "latitude": lat, "longitude": lon}
)

diff_va = xr.DataArray(
    signal_nc['diff_va'],
    dims=["level", "latitude", "longitude"],
    coords={"level": lev, "latitude": lat, "longitude": lon}
)

diff_ts = xr.DataArray(
    signal_nc['diff_ts'],
    dims=["latitude", "longitude"],
    coords={"latitude": lat, "longitude": lon}
)

# Combine into a Dataset
test = xr.Dataset({
    "diff_ta": diff_ta,
    "diff_rh": diff_rh,
    "diff_ua": diff_ua,
    "diff_va": diff_va,
    "diff_ts": diff_ts
})

test['latitude'] = test['latitude'].assign_attrs(		
                standard_name = "latitude",
		long_name = "latitude",
		units = "degrees_north",
		axis = "Y" )
test['longitude'] = test['longitude'].assign_attrs(
                standard_name = "longitude",
		long_name = "longitude",
		units = "degrees_east",
		axis = "X")

test['level'] = test['level'].assign_attrs(
                standard_name = "air_pressure",
		long_name = "pressure",
		units = "Pa",
		positive = "down",
		axis = "Z",
		stored_direction = "decreasing")
   
#================== Save =================================================
# Save the result to a new NetCDF file
test.to_netcdf('signal.nc', format='NETCDF4')
```
{% endfold %}

and, regrid `singal.nc` to **0.25deg**,

```
cdo remapbil,r1440x721 signal.nc signal_0p25.nc
```

## Python script

{% fold info @2d_interp.py %}
```python
#!/bin/python

#------------------------------------------------#
#Author:         wpsze
#Email：         wpsze
#date:           2025-02-04 16:34:39
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2025 All rights reserved
#------------------------------------------------#
import os
import numpy as np
import xarray as xr
# from scipy.interpolate import interp1d

# environment variable 
yyyymmddhh = os.environ['yyyymmddhh'] 

# Load the signal and ERA5 NetCDF files
signal_nc = xr.open_dataset('signal_0p25.nc')      # Replace with actual signal file path
era5_nc = xr.open_dataset('ERA5-SL.nc')          # Replace with actual ERA5 file path

# Extract 2D SST variables
sst_signal = signal_nc['diff_ts']  # SST from signal file (2D array)
skt_signal = signal_nc['diff_ts']  # Skin temperature from signal file (2D array)
lat_signal = signal_nc['lat']
lon_signal = signal_nc['lon']

sst_era5 = era5_nc['sst']       # SST from ERA5 file (2D array)
skt_era5 = era5_nc['skt']       # Skin temperature from ERA5 file (2D array)

#================== SST ==================================================
print("=== processing: SST ")
# Update dimensions for signal_sst.nc
#sst_signal = sst_signal.rename({'ncl3': 'latitude', 'ncl4': 'longitude'})

# # Reverse the SST data along the latitude dimension
#reversed_sst_signal = sst_signal[::-1, :]  # Assuming latitude is the first dimension

# # Update the coordinates to reflect the new latitude order
#reversed_latitude = lat_signal[::-1]  # Reverse latitude coordinates, from [-90,90] to [90,-90]

# # Update the SST variable to use the reversed latitude
#reversed_sst_signal = reversed_sst_signal.assign_coords(latitude=reversed_latitude.values, longitude=lon_signal.values)

# Define new latitude and longitude arrays for 0.25-degree grid
# Perform horizontal interpolation to 0.25-degree grid
# method ({"linear", "nearest", "zero", "slinear", "quadratic", 
# "cubic", "quintic", "polynomial", "pchip", "barycentric", "krogh",
# "akima", "makima"}) – Interpolation method to use (see descriptions above). fill_value='extrapolate'
# reversed_sst_signal = reversed_sst_signal.interp(latitude=sst_era5.coords['latitude'].values, 
#                                                  longitude=sst_era5.coords['longitude'].values)

# Add signal to ERA5
era5_nc['sst'] = sst_era5 + sst_signal.values[::-1,:] # Reverse latitude coordinates, from [-90,90] to [90,-90]

# #================== skt ==================================================
print("=== processing: Skin temperature ")
# # Update dimensions for signal_sst.nc
# skt_signal = skt_signal.rename({'ncl3': 'latitude', 'ncl4': 'longitude'})

# # Reverse the SST data along the latitude dimension
#reversed_skt_signal = skt_signal[::-1, :]  # Assuming latitude is the first dimension

# # Update the coordinates to reflect the new latitude order
# # reversed_latitude = lat_signal[::-1]  # Reverse latitude coordinates (Done before!!)

# # Update the SST variable to use the reversed latitude
#reversed_skt_signal = reversed_skt_signal.assign_coords(latitude=reversed_latitude.values, longitude=lon_signal.values)

# # Define new latitude and longitude arrays for 0.25-degree grid
# # Perform horizontal interpolation to 0.25-degree grid
# reversed_skt_signal = reversed_skt_signal.interp(latitude=sst_era5.coords['latitude'].values, 
#                                                  longitude=sst_era5.coords['longitude'].values)

# # Add signal to ERA5
era5_nc['skt'] = skt_era5 + skt_signal.values[::-1,:] # Reverse latitude coordinates, from [-90,90] to [90,-90]

#================== Save =================================================
# Save the result to a new NetCDF file
era5_nc.to_netcdf(f'new_ERA5-0p25-SL-{yyyymmddhh}.nc')

##to_grib(era5_nc, 'new_ERA5_SL.grib') #, grib_keys={'centre': 'ecmf'})

print(f"Horizontal interpolation completed and save
```
{% endfold %}

# Interpolating 3D Temperature from Signal NetCDF to ERA5 NetCDF

Here's a Python script for interpolating a 3D temperature field from a signal NetCDF file to match the grid of an ERA5 NetCDF file.

In this part, we will demonstrate how to interpolate a 3D temperature field (`ta`) from a signal NetCDF file to match the grid of an ERA5 NetCDF file containing its own 3D temperature field (`TT`). This process is essential for applying climate change signals to ERA5 data effectively.

## Introduction
In this blog, we'll discuss how to interpolate a 3D temperature field (`ta`) from a signal NetCDF file to match the grid of an ERA5 NetCDF file, which contains a different temperature variable (`TT`). This procedure involves two main steps: vertical interpolation to align the vertical levels and horizontal interpolation to match the latitude and longitude coordinates.

## Motivation
By applying the Pseudo-Global-Warming (PGW) approach using CMIP6 climate change signals, we can generate a difference that can be added to the initial conditions like ERA5. Accurate interpolation is crucial for effective climate modeling and analysis.

## Steps for Interpolation

1. **Vertical Interpolation**: Adjust the temperature data from the signal file to the vertical levels of the ERA5 file.

### Python Script

{% fold info @ERA5 isobaricInhPa %}
```console
netcdf ERA5-PL {
dimensions:
	isobaricInhPa = 37 ;
	latitude = 721 ;
	longitude = 1440 ;

	double isobaricInhPa(isobaricInhPa) ;
		isobaricInhPa:_FillValue = NaN ;
		isobaricInhPa:long_name = "pressure" ;
		isobaricInhPa:units = "hPa" ;
		isobaricInhPa:positive = "down" ;
		isobaricInhPa:stored_direction = "decreasing" ;
		isobaricInhPa:standard_name = "air_pressure" ;


 isobaricInhPa = 1000, 975, 950, 925, 900, 875, 850, 825, 800, 775, 750, 700, 
    650, 600, 550, 500, 450, 400, 350, 300, 250, 225, 200, 175, 150, 125, 
    100, 70, 50, 30, 20, 10, 7, 5, 3, 2, 1 ;
```
{% endfold %}

{% fold info @Variables we need %}
```console
	float z(isobaricInhPa, latitude, longitude) ;
		z:GRIB_units = "m**2 s**-2" ;
		z:long_name = "Geopotential" ;
		z:standard_name = "geopotential" ;
	float r(isobaricInhPa, latitude, longitude) ;
		r:GRIB_units = "%" ;
		r:long_name = "Relative humidity" ;
		r:standard_name = "relative_humidity" ;
	float q(isobaricInhPa, latitude, longitude) ;
		q:GRIB_units = "kg kg**-1" ;
		q:long_name = "Specific humidity" ;
		q:standard_name = "specific_humidity" ;
	float t(isobaricInhPa, latitude, longitude) ;
		t:GRIB_units = "K" ;
		t:long_name = "Temperature" ;
		t:standard_name = "air_temperature" ;
	float u(isobaricInhPa, latitude, longitude) ;
		u:GRIB_units = "m s**-1" ;
		u:long_name = "U component of wind" ;
		u:standard_name = "eastward_wind" ;
	float v(isobaricInhPa, latitude, longitude) ;
		v:GRIB_units = "m s**-1" ;
		v:long_name = "V component of wind" ;
		v:standard_name = "northward_wind" ;
```
{% endfold %}

Below is a Python script that performs the required interpolations.

{% fold info @3d_interp.py (Pressure level) %}
```python
#!/bin/python

#------------------------------------------------#
#Author:         wpsze
#Email：         wpsze
#date:           2025-02-04 16:31:50
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2025 All rights reserved
#------------------------------------------------#
import os
import numpy as np
import xarray as xr
from scipy.interpolate import interp1d

# environment variable 
yyyymmddhh = os.environ['yyyymmddhh'] 

# Load the signal and ERA5 NetCDF files
signal_nc = xr.open_dataset('signal_0p25.nc')  # Replace with actual signal file path
era5_nc = xr.open_dataset('ERA5-PL.nc')   # Replace with actual ERA5 file path

#================ Vertical levels =======================================
# Vertical levels
signal_levels = signal_nc['level'].values/100            # Vertical levels from signal file,  lev(ncl14), convert to hPa unit
lat_signal = signal_nc['lat']
lon_signal = signal_nc['lon']

# Update the coordinates to reflect the new latitude order
reversed_latitude = lat_signal[::-1]  # Reverse latitude coordinates, from [-90,90] to [90,-90]

era5_levels = era5_nc['isobaricInhPa']          # Vertical levels from ERA5 file, 'level' if grib_to_netcdf, 'isobaricInhPa' if xarray/cfgrib

# signal:
#[1000.  925.  850.  700.  600.  500.  400.  300.  250.  200.  150.  100.
#   70.   50.]
# ERA5:
#[1000.  975.  950.  925.  900.  875.  850.  825.  800.  775.  750.  700.
#  650.  600.  550.  500.  450.  400.  350.  300.  250.  225.  200.  175.
#  150.  125.  100.   70.   50.   30.   20.   10.    7.    5.    3.    2.
#    1.]
# Only add singals to specific levels and Convert to by era5_levels[1:29]
# [975. 950. 925. 900. 875. 850. 825. 800. 775. 750. 700. 650. 600. 550.
# 500. 450. 400. 350. 300. 250. 225. 200. 175. 150. 125. 100.  70.  50.]

print(f"CMIP6 singal levels = {signal_levels}")
print(f"era5_levels[1:29] = {era5_levels[1:29].values}")

#================ Interpolation function ================================
# Interpolation function for vertical levels
def vertical_interpolate(var_3d, signal_levels, target_levels):
    interpolated_temp = xr.DataArray(
        np.empty((target_levels.size, var_3d.shape[1], var_3d.shape[2])), 
        dims=("isobaricInhPa", "latitude", "longitude"))
    interpolated_temp = interpolated_temp.assign_coords(isobaricInhPa=era5_levels.values, 
                                                        latitude=reversed_latitude.values,
                                                        longitude=lon_signal.values)
    f = interp1d(signal_levels, var_3d[:, :, :], axis=0, kind='linear', fill_value='extrapolate')
    interpolated_temp[:, :, :] = f(target_levels)
    return interpolated_temp

#================ T air_temperature in K ================================
print("=== processing: T air_temperature in K ")

# Extract variables
ta_signal = signal_nc['diff_ta']    # 3D temperature from signal file, diff_ta(ncl0, ncl1, ncl2)
TT_era5 = era5_nc['t']              # 3D temperature from ERA5 file, t(isobaricInhPa, latitude, longitude)

# Update dimensions
ta_signal = ta_signal.rename({'level': 'isobaricInhPa', 'lat': 'latitude', 'lon': 'longitude'})

# Reverse the TT data along the latitude dimension
reversed_ta_signal = ta_signal[:, ::-1, :]  # latitude is the second dimension

# Update the TT variable to use the reversed latitude
reversed_ta_signal = reversed_ta_signal.assign_coords(latitude=reversed_latitude.values, longitude=lon_signal.values)

# Perform vertical interpolation
interpolated_ta = vertical_interpolate(reversed_ta_signal, signal_levels, era5_levels)

# Define new latitude and longitude arrays for 0.25-degree grid
# Perform horizontal interpolation to 0.25-degree grid
## interpolated_ta = interpolated_ta.interp(latitude=TT_era5.coords['latitude'].values, longitude=TT_era5.coords['longitude'].values)

# Add signal to ERA5
era5_nc['t'][1:29,:,:] = TT_era5[1:29,:,:] + interpolated_ta[1:29,:,:]

#================ Relative Humidity (RH) in % ================================
print("=== processing: Relative Humidity (RH) in % ")

# Extract variables
rh_signal = signal_nc['diff_rh']    # 3D RH from signal file, diff_ta(ncl5, ncl6, ncl7)
RH_era5 = era5_nc['r']              # 3D RH from ERA5 file, t(isobaricInhPa, latitude, longitude)

# Update dimensions
rh_signal = rh_signal.rename({'level': 'isobaricInhPa', 'lat': 'latitude', 'lon': 'longitude'})

# Reverse the RH data along the latitude dimension
reversed_rh_signal = rh_signal[:, ::-1, :]  # latitude is the second dimension

# Update the RH variable to use the reversed latitude
reversed_rh_signal = reversed_rh_signal.assign_coords(latitude=reversed_latitude.values, longitude=lon_signal.values)

# Perform vertical interpolation
interpolated_rh = vertical_interpolate(reversed_rh_signal, signal_levels, era5_levels)

# Define new latitude and longitude arrays for 0.25-degree grid
# Perform horizontal interpolation to 0.25-degree grid
##interpolated_rh = interpolated_rh.interp(latitude=RH_era5.coords['latitude'].values, longitude=RH_era5.coords['longitude'].values)

# Add signal to ERA5
era5_nc['r'][1:29,:,:] = RH_era5[1:29,:,:] + interpolated_rh[1:29,:,:]

#=============================================================================

# Save the result to a new NetCDF file
era5_nc.to_netcdf(f'new_ERA5-0p25-PL-{yyyymmddhh}.nc')

print(f"Interpolation completed and saved to 'interpolated_ERA5-0p25-PL-{yyyymmddhh}.nc.")
```
{% endfold %}

{% fold info @3d_interp_with_uv.py %}
```python
#!/bin/python

#------------------------------------------------#
#Author:         wpsze
#Email：         wpsze
#date:           2025-02-04 16:31:50
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2025 All rights reserved
#------------------------------------------------#
import os
import numpy as np
import xarray as xr
from scipy.interpolate import interp1d

# environment variable 
yyyymmddhh = os.environ['yyyymmddhh'] 

# Load the signal and ERA5 NetCDF files
signal_nc = xr.open_dataset('signal_0p25.nc')  # Replace with actual signal file path
era5_nc = xr.open_dataset(f'ERA5-0p25-PL-{yyyymmddhh}.nc')   # Replace with actual ERA5 file path

#================ Vertical levels =======================================
# Vertical levels
signal_levels = signal_nc['level'].values/100            # Vertical levels from signal file,  lev(ncl14), convert to hPa unit
lat_signal = signal_nc['lat']
lon_signal = signal_nc['lon']

# Update the coordinates to reflect the new latitude order
reversed_latitude = lat_signal[::-1]  # Reverse latitude coordinates, from [-90,90] to [90,-90]

era5_levels = era5_nc['isobaricInhPa']          # Vertical levels from ERA5 file, 'level' if grib_to_netcdf, 'isobaricInhPa' if xarray/cfgrib

# signal:
#[1000.  925.  850.  700.  600.  500.  400.  300.  250.  200.  150.  100.
#   70.   50.]
# ERA5:
#[1000.  975.  950.  925.  900.  875.  850.  825.  800.  775.  750.  700.
#  650.  600.  550.  500.  450.  400.  350.  300.  250.  225.  200.  175.
#  150.  125.  100.   70.   50.   30.   20.   10.    7.    5.    3.    2.
#    1.]
# Only add singals to specific levels and Convert to by era5_levels[1:29]
# [975. 950. 925. 900. 875. 850. 825. 800. 775. 750. 700. 650. 600. 550.
# 500. 450. 400. 350. 300. 250. 225. 200. 175. 150. 125. 100.  70.  50.]

print(f"CMIP6 singal levels = {signal_levels}")
print(f"era5_levels[1:29] = {era5_levels[1:29].values}")

#================ Interpolation function ================================
# Interpolation function for vertical levels
def vertical_interpolate(var_3d, signal_levels, target_levels):
    interpolated_temp = xr.DataArray(
        np.empty((target_levels.size, var_3d.shape[1], var_3d.shape[2])), 
        dims=("isobaricInhPa", "latitude", "longitude"))
    interpolated_temp = interpolated_temp.assign_coords(isobaricInhPa=era5_levels.values, 
                                                        latitude=reversed_latitude.values,
                                                        longitude=lon_signal.values)
    f = interp1d(signal_levels, var_3d[:, :, :], axis=0, kind='linear', fill_value='extrapolate')
    interpolated_temp[:, :, :] = f(target_levels)
    return interpolated_temp

#================ T air_temperature in K ================================
print("=== processing: T air_temperature in K ")

# Extract variables
ta_signal = signal_nc['diff_ta']    # 3D temperature from signal file, diff_ta(ncl0, ncl1, ncl2)
TT_era5 = era5_nc['t']              # 3D temperature from ERA5 file, t(isobaricInhPa, latitude, longitude)

# Update dimensions
ta_signal = ta_signal.rename({'level': 'isobaricInhPa', 'lat': 'latitude', 'lon': 'longitude'})

# Reverse the TT data along the latitude dimension
reversed_ta_signal = ta_signal[:, ::-1, :]  # latitude is the second dimension, # Reverse latitude coordinates, from [-90,90] to [90,-90]

# Update the TT variable to use the reversed latitude
reversed_ta_signal = reversed_ta_signal.assign_coords(latitude=reversed_latitude.values, longitude=lon_signal.values)

# Perform vertical interpolation
interpolated_ta = vertical_interpolate(reversed_ta_signal, signal_levels, era5_levels)

# Define new latitude and longitude arrays for 0.25-degree grid
# Perform horizontal interpolation to 0.25-degree grid
## interpolated_ta = interpolated_ta.interp(latitude=TT_era5.coords['latitude'].values, longitude=TT_era5.coords['longitude'].values)

# Add signal to ERA5
era5_nc['t'][1:29,:,:] = TT_era5[1:29,:,:] + interpolated_ta[1:29,:,:]

#================ Relative Humidity (RH) in % ================================
print("=== processing: Relative Humidity (RH) in % ")

# Extract variables
rh_signal = signal_nc['diff_rh']    # 3D RH from signal file, diff_ta(ncl5, ncl6, ncl7)
RH_era5 = era5_nc['r']              # 3D RH from ERA5 file, t(isobaricInhPa, latitude, longitude)

# Update dimensions
rh_signal = rh_signal.rename({'level': 'isobaricInhPa', 'lat': 'latitude', 'lon': 'longitude'})

# Reverse the RH data along the latitude dimension
reversed_rh_signal = rh_signal[:, ::-1, :]  # latitude is the second dimension, # Reverse latitude coordinates, from [-90,90] to [90,-90]

# Update the RH variable to use the reversed latitude
reversed_rh_signal = reversed_rh_signal.assign_coords(latitude=reversed_latitude.values, longitude=lon_signal.values)

# Perform vertical interpolation
interpolated_rh = vertical_interpolate(reversed_rh_signal, signal_levels, era5_levels)

# Define new latitude and longitude arrays for 0.25-degree grid
# Perform horizontal interpolation to 0.25-degree grid
##interpolated_rh = interpolated_rh.interp(latitude=RH_era5.coords['latitude'].values, longitude=RH_era5.coords['longitude'].values)

# Add signal to ERA5
era5_nc['r'][1:29,:,:] = RH_era5[1:29,:,:] + interpolated_rh[1:29,:,:]

#================ u eastward_wind ================================
print("=== processing: u eastward_wind (m s**-1) ")

# Extract variables
ua_signal = signal_nc['diff_ua']    # 3D diff_ua from signal file, diff_ua(level, latitude, longitude)
u_era5 = era5_nc['u']              # 3D u from ERA5 file, u(isobaricInhPa, latitude, longitude)

# Update dimensions
ua_signal = ua_signal.rename({'level': 'isobaricInhPa', 'lat': 'latitude', 'lon': 'longitude'})

# Reverse the RH data along the latitude dimension
reversed_ua_signal = ua_signal[:, ::-1, :]  # latitude is the second dimension, # Reverse latitude coordinates, from [-90,90] to [90,-90]

# Update the RH variable to use the reversed latitude
reversed_ua_signal = reversed_ua_signal.assign_coords(latitude=reversed_latitude.values, longitude=lon_signal.values)

# Perform vertical interpolation
interpolated_ua = vertical_interpolate(reversed_ua_signal, signal_levels, era5_levels)

# Define new latitude and longitude arrays for 0.25-degree grid
# Perform horizontal interpolation to 0.25-degree grid
##interpolated_rh = interpolated_rh.interp(latitude=RH_era5.coords['latitude'].values, longitude=RH_era5.coords['longitude'].values)

# Add signal to ERA5
era5_nc['u'][1:29,:,:] = u_era5[1:29,:,:] + interpolated_ua[1:29,:,:]

#================ v northward_wind ================================
print("=== processing: v northward_wind (m s**-1) ")

# Extract variables
va_signal = signal_nc['diff_va']    # 3D diff_va from signal file, diff_va(level, latitude, longitude)
v_era5 = era5_nc['v']              # 3D v from ERA5 file, u(isobaricInhPa, latitude, longitude)

# Update dimensions
va_signal = va_signal.rename({'level': 'isobaricInhPa', 'lat': 'latitude', 'lon': 'longitude'})

# Reverse the RH data along the latitude dimension
reversed_va_signal = va_signal[:, ::-1, :]  # latitude is the second dimension, # Reverse latitude coordinates, from [-90,90] to [90,-90]

# Update the RH variable to use the reversed latitude
reversed_va_signal = reversed_va_signal.assign_coords(latitude=reversed_latitude.values, longitude=lon_signal.values)

# Perform vertical interpolation
interpolated_va = vertical_interpolate(reversed_va_signal, signal_levels, era5_levels)

# Define new latitude and longitude arrays for 0.25-degree grid
# Perform horizontal interpolation to 0.25-degree grid
##interpolated_rh = interpolated_rh.interp(latitude=RH_era5.coords['latitude'].values, longitude=RH_era5.coords['longitude'].values)

# Add signal to ERA5
era5_nc['v'][1:29,:,:] = v_era5[1:29,:,:] + interpolated_va[1:29,:,:]

#=============================================================================

# Save the result to a new NetCDF file
era5_nc.to_netcdf(f'new_ERA5-0p25-PL-{yyyymmddhh}.nc')

print(f"Interpolation completed and saved to 'interpolated_ERA5-0p25-PL-{yyyymmddhh}.nc.")
```
{% endfold %}

### Explanation of the Code

1. **Loading Data**: The script uses `xarray` to load the signal and ERA5 NetCDF files.
2. **Vertical Interpolation**: A function (`vertical_interpolate`) is defined to perform linear interpolation on the vertical levels of the temperature data.
3. **Saving the Result**: The interpolated temperature data is saved to a new NetCDF file.

## Conclusion
This interpolation approach allows for aligning temperature data from different datasets, which is essential for climate modeling. By accurately matching the grids of the signal and ERA5 data, we can effectively analyze and apply climate change signals in our research.

---

# Converting NetCDF Files back to GRIB Format: A Step-by-Step Guide

## Introduction

In the realm of climate data analysis, the ability to convert between different file formats is essential. While NetCDF is widely used for storing multidimensional scientific data, GRIB (Generalized Regularly-distributed Information in Binary form) is prevalent in meteorological applications. This blog will guide you through the process of converting NetCDF files back to GRIB format, ensuring you're equipped to handle various data formats in your climate studies.

## Motivation

The motivation behind converting NetCDF files to GRIB format can stem from several factors:

- **Compatibility**: Many meteorological models and tools predominantly use GRIB format. Converting your NetCDF data ensures compatibility with these systems.
- **Efficiency**: GRIB files are often more compact than their NetCDF counterparts, making them easier to store and share, especially when dealing with large datasets.
- **Standardization**: For specific applications, adhering to a standard format like GRIB can enhance data interoperability across different platforms and tools.

## 麻煩了

**這一步真是麻煩太多了**

Move to [**NWP | PGW | To prepare the ERA5 GRIB file**](https://waipangsze.github.io/2025/02/05/NWP-PGW-To-prepare-the-ERA5-GRIB-file/)


## Verify the Conversion

After conversion, it’s crucial to verify that the GRIB file has been created correctly. You can do this by checking the contents:

```bash
grib_ls output_file.grib
```

## Verify the initial condition

To plot the vertical profiles of the given data, we can use Python with the matplotlib library. Here's how visualize the profiles of signal_1p25, signal_0p25, diff_era5 and diff-MPAS-init.nc against the specified levels.

{% fold info @check_plot.py %}
```python
#!/bin/python

#------------------------------------------------#
#Author:         wpsze
#Email：         wpsze
#date:           2025-02-06 08:54:13
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2025 All rights reserved
#------------------------------------------------#

import xarray as xr
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

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

#=================================================================
yyyymmddhh = "2022063000"

#============== signal 1.25 deg =============================
dsignal = xr.open_dataset("signal.nc")

#============== signal 0.25 deg =============================
dsignal_0p25 = xr.open_dataset("signal_0p25.nc")

#============== SL/PL =============================
original_SL_file = xr.open_dataset(f"ERA5-0p25-SL-{yyyymmddhh}.nc")
new_SL_file = xr.open_dataset(f"new_ERA5-0p25-SL-{yyyymmddhh}.nc")
# original_PL_file = xr.open_dataset(f"ERA5-0p25-SL-{yyyymmddhh}.nc")
# new_PL_file = xr.open_dataset(f"new_ERA5-0p25-SL-{yyyymmddhh}.nc")
diff_PL = xr.open_dataset("diff_pl.nc") # ncdiff new_PL.nc PL.nc

#============== MPAS init.nc diff =============================
diff_init = xr.open_dataset("diff_init.nc") # ncdiff xx.nc xx.nc

# diff_init["skintemp"].shape
# (1, 40962)
# diff_init["theta"].shape
# (1, 40962, 55)
#============== SST & SKT =============================
tmp = np.nanmean(dsignal["diff_ts"].values)
print(f"signal diff_ts 1.25deg nanmean = {tmp}")

tmp = np.nanmean(dsignal_0p25["diff_ts"].values)
print(f"signal diff_ts 0.25deg nanmean = {tmp}")

tmp = new_SL_file["sst"].values - original_SL_file["sst"].values
print(f"diff EAR5 (future-past) SST = {np.nanmean(tmp)}")

tmp = new_SL_file["skt"].values - original_SL_file["skt"].values
print(f"diff EAR5 (future-past) SKT = {np.nanmean(tmp)}")

tmp = diff_init["skintemp"]
print(f"diff init (future-past) skintemp = {np.nanmean(tmp)}")
#============== vertial =============================
# Extract the variable 'ta' (ensure to adjust the variable name if it's different)
ta_signal = dsignal['diff_ta']
ta_signal_0p25 = dsignal_0p25['diff_ta']
ta_era5 = diff_PL['t']

# Assuming the vertical dimension is named 'level' or 'plev'; adjust as necessary
levels = ta_signal['level'].values  # Adjust depending on your dimensions
levels_era5 = diff_PL['isobaricInhPa'].values  # Adjust depending on your dimensions

# Plotting
plt.figure(figsize=(10, 6))

# Plot each profile
plt.plot(ta_era5.sel(isobaricInhPa=levels_era5).mean(dim='latitude').mean(dim='longitude'), levels_era5, label='ERA5', color='green', marker = 'o')
plt.plot(ta_signal.sel(level=levels).mean(dim='latitude').mean(dim='longitude'), levels/100, label='Signal', color='blue', marker = 'o')
plt.plot(ta_signal_0p25.sel(level=levels).mean(dim='lat').mean(dim='lon'), levels/100, label='Signal 0.25°', color='orange', marker = 'o')

# Add labels and title
plt.xlabel('diff Temperature (K)')  # Adjust based on your variable unit
plt.ylabel('Pressure Level (hPa)')  # Adjust based on your vertical coordinate
plt.title('Vertical Profiles of diff Temperature (ta)')
plt.legend()
plt.grid()
plt.gca().invert_yaxis()  # Invert y-axis for pressure levels
plt.tight_layout()

# Show the plot
# plt.show()
plt.savefig("diff_ta.png", dpi=300)

#============== vertial =============================
# Extract the variable 'ta' (ensure to adjust the variable name if it's different)
ta_signal = dsignal['diff_rh']
ta_signal_0p25 = dsignal_0p25['diff_rh']
ta_era5 = diff_PL['r']

# Assuming the vertical dimension is named 'level' or 'plev'; adjust as necessary
levels = ta_signal['level'].values  # Adjust depending on your dimensions
levels_era5 = diff_PL['isobaricInhPa'].values  # Adjust depending on your dimensions

# Plotting
plt.figure(figsize=(10, 6))

# Plot each profile
plt.plot(ta_era5.sel(isobaricInhPa=levels_era5).mean(dim='latitude').mean(dim='longitude'), levels_era5, label='ERA5', color='green', marker = 'o')
plt.plot(ta_signal.sel(level=levels).mean(dim='latitude').mean(dim='longitude'), levels/100, label='Signal', color='blue', marker = 'o')
plt.plot(ta_signal_0p25.sel(level=levels).mean(dim='lat').mean(dim='lon'), levels/100, label='Signal 0.25°', color='orange', marker = 'o')

# Add labels and title
plt.xlabel('diff RH (%)')  # Adjust based on your variable unit
plt.ylabel('Pressure Level (hPa)')  # Adjust based on your vertical coordinate
plt.title('Vertical Profiles of diff RH (%)')
plt.legend()
plt.grid()
plt.gca().invert_yaxis()  # Invert y-axis for pressure levels
plt.tight_layout()

# Show the plot
# plt.show()
plt.savefig("diff_rh.png", dpi=300)

#========================================================
# Plotting
plt.figure(figsize=(10, 6))

# Plot each profile
plt.plot(np.mean(diff_init["relhum"][0,:,:], axis=0), np.linspace(0,54,54+1), label='MPAS-120km', color='black', marker = 'o')

# Add labels and title
plt.xlabel('diff RH (%)')  # Adjust based on your variable unit
plt.ylabel('level')  # Adjust based on your vertical coordinate
plt.title('Vertical Profiles of diff RH (%)')
plt.legend()
plt.grid()
plt.tight_layout()

# Show the plot
# plt.show()
plt.savefig("diff_init_rh.png", dpi=300)
```
{% endfold %}

# NCL references


# Discussion Summary

1. **Which Variables Can Be Used?**
   - The selection of variables is highly dependent on the specific goals and context of the case study. Commonly utilized variables include:
     - **Temperature (T)**: Essential for understanding thermal dynamics.
     - **Relative Humidity (RH)**: Important for moisture content and cloud formation.
     - Additional variables may include geopotential and wind components, depending on the focus of the study.

2. **Importing Wind Components (u, v): !!!**
   - The decision to import wind components (u and v) should be guided by their relevance to the study. Wind data is crucial for:
     - Enhancing model accuracy, especially in studies involving atmospheric dynamics.
     - Providing insights into transport processes and weather patterns.
   - **The PGW method is designed to isolate the thermodynamic effects of climate change** while largely preserving the historical synoptic patterns and dynamics of the atmosphere, such as the large-scale wind fields that govern tropical cyclone steering. **In most PGW implementations, the future climate signal is applied to thermodynamic variables (e.g., temperature and specific humidity profiles) rather than dynamic variables like the 3D wind field**. **This is because altering the wind field directly could disrupt the atmospheric balance (e.g., geostrophic or hydrostatic equilibrium), leading to unrealistic adjustments in the model during the initial spin-up period**. Instead, the wind fields are typically left unchanged in the initial conditions, allowing the model to dynamically adjust the winds in response to the perturbed thermodynamic state as the simulation evolves.
   - For tropical cyclones, the steering flow—defined as the vertically averaged environmental wind that guides the storm’s motion—is a critical factor. This flow is influenced by the large-scale circulation patterns (e.g., subtropical ridges, troughs, and jet streams), which are part of the initial and boundary conditions derived from reanalysis data (e.g., ERA-Interim or NCEP) in a control run. In a PGW simulation, the assumption is often that the future climate signal does not significantly alter these large-scale wind patterns in the initial state. **Therefore, in many studies, the 3D wind field is not explicitly modified by adding a future wind signal to the initial conditions**. The focus is instead on how thermodynamic changes (e.g., warmer SSTs, increased atmospheric moisture) affect cyclone intensity, structure, and potentially its track indirectly through feedback on the dynamics.
   - **If your goal is to isolate the thermodynamic effects of warming** (e.g., how increased SSTs or humidity affect TC intensity), then not adding a 3D wind perturbation is standard practice. The steering flow will remain consistent with the historical case, and any changes in TC behavior will stem from thermodynamic feedbacks.
   - **If your goal** is to explore how future changes in large-scale circulation might affect TC steering, then you could consider adding a 3D wind perturbation derived from GCM projections (e.g., CMIP5 or CMIP6 under a scenario like RCP8.5). **However, this is less common in PGW because**:
     - GCMs show significant disagreement on future wind field changes, especially at regional scales relevant to TC steering.
     - Adding wind perturbations risks introducing imbalances that could destabilize the model unless carefully adjusted (e.g., via pressure corrections to maintain hydrostatic balance).
     - The steering flow’s response to climate change is often studied separately using full transient GCM simulations rather than PGW.
   - **In practice, some PGW studies avoid wind perturbations entirely to maintain consistency with the historical storm track and focus on intensity or precipitation changes**. Others might use sensitivity experiments to test the impact of hypothetical wind changes, but this is not the norm.

3. **Interpolation Methods:**
   - **Horizontal Interpolation**: Bilinear interpolation is commonly used for its simplicity and effectiveness in providing smooth transitions between grid points.
   - **Vertical Interpolation**: The choice between linear and logarithmic interpolation should be based on the nature of the variable being interpolated:
     - **Linear Interpolation** is suitable for most variables.
     - **Logarithmic Interpolation** may be more appropriate for variables with a wide range of values, such as pressure or density.

4. **Consideration of Vertical Levels:**
   - When performing vertical interpolation, it is important to consider whether to include levels such as 100 hPa. Generally:
     - If the climate signal data does not extend above certain levels (e.g., 50 hPa), it is prudent to exclude those levels to avoid introducing inconsistencies into the model.

5. **Data Integration Formats:**
   - The climate signal can be integrated into different formats based on the model being used:
     - For **ERA5**, climate signals can be added to the ICBC (Initial Condition Boundary Condition) files in GRIB format.
     - In **WRF**, data may be added directly to `met_em` files, which are used for meteorological input.
       - ungrib 
         - Extract meteorological fields from GRIB files
       - metgrid 
         - Horizontally interpolate meteorological fields (from ungrib) to simulation grids (defined by geogrid)
       - real.exe
         - Initialization will calculate reference state, vertical coordinate interpolation, disturbance field calculation, output boundary conditions, surface data mask, etc.
         - [初始化會計算 reference state ，垂直座標內插，擾動場計算，產出邊界條件，地表資料遮罩等等](https://sites.google.com/g.ntu.edu.tw/wrf-tutorial-modules/%E6%A8%A1%E5%BC%8F%E8%A8%AD%E8%A8%88/wrf-initialization)
         - <https://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.2/users_guide_chap4.html>
         - **So, it is fine for adding signal into met_em because met_em only does horizontal interpolation.**
       - wrf.exe
     - For **MPAS**, it is no met_em step and only from `static.nc` to `init.nc`. Therefore, the only way is to add signal into ICBC=ERA5.

6. **Long-term Simulations:**
   - For extended runs in both MPAS and WRF, it is essential to **regularly update SST data**. This practice helps maintain the relevance of the model outputs to current climate conditions, ensuring that the simulations reflect the most recent data and improving the accuracy of long-term forecasts.

{% note primary %}
Read **[MPAS | ERA5| Update SST and sea-ice fraction](https://waipangsze.github.io/2025/02/14/MPAS-ERA5-Update-SST-and-sea-ice-fraction/)**
{% endnote %}

Integrating and interpolating climate variables in models like WRF and MPAS involves careful consideration of the variables used, the methods of interpolation, and the formats for data integration. By addressing these factors, researchers can enhance the accuracy and reliability of climate and weather predictions. Proper handling of SST and other variables in both short-term and long-term simulations is crucial for effective climate modeling and analysis.

---

# References

1. [Climate Data Operators (CDO) Tutorial (**recommend**)](https://code.mpimet.mpg.de/projects/cdo/wiki/tutorial#Basic-Usage)
2. [failed to set key 'endStep' in to_grib](https://github.com/ecmwf/cfgrib/issues/289)
3. [Add support to write a xarray.Dataset to a GRIB file.](https://github.com/ecmwf/cfgrib/issues/18)
   1. cfgrib.to_grib(ds, 'User_Guide_Example_Data_from_cfgrib.grib')#, grib_keys={'centre': 'ecmf'})
   2. `cfgrib/xarray_to_grib.py:252: FutureWarning: GRIB write support is experimental, DO NOT RELY ON IT!`
4. [Converting NetCDF to GRIB | 2014](https://code.mpimet.mpg.de/boards/1/topics/2907)
   1. `cdo -v -f grb -copy sstatlantic_addhifreq.nc ofile.grb`
      1. `cdo -v -f grb2 -copy sstatlantic_addhifreq.nc ofile.grb` if grib2 format.
   2. The large GRIB file has data with 2602x1402=3648004 grid points. CDO stores netCDF double precision floats to 24bit packed GRIB per default. The result is a GRIB record with 10944120 bytes. The max. record length in the GRIB1 standard is 8388607 bytes. **CDO uses the ECMWF extension of the GRIB1 standard for larger GRIB records**. These large GRIB records can only be read by CDO or tools from the ECMWF.
   3. You can create a 16bit GRIB record to reduce the record size:
      1. `cdo -b 16 -f grb copy file.nc file.grb`
5. Due to the fact that the NetCDF data model is much more free and extensible than the GRIB one, it is not possible to write a generic xarray.Dataset to a GRIB file. The aim for cfgrib is to implement write support for a subset of carefully crafted datasets that fit the GRIB data model. In particular the only coordinates that we target at the moment are the one returned by opening a GRIB with the cfgrib flavour of cfgrib.open_dataset, namely:number, time, step, a vertical coordinate (isobaricInhPa, heightAboveGround, surface, etc), and the horizontal coordinates (for example latitude and longitude for a regular_ll grid type).