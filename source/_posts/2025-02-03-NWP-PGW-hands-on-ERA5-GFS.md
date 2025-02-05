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
index_img: 
banner_img: 
---

# Introduction

Keywords:

- The Pseudo-Global-Warming (PGW) approach
- CMIP6
- how to convert ERA5 GRIB files to NetCDF format
- GRIB files are the file format used at ECMWF.  GRIB is a WMO standard and consists of GRIB Edition 1 and Edition 2

The ERA5 dataset, produced by the European Centre for Medium-Range Weather Forecasts (ECMWF), provides a wealth of climate and weather data. However, the data is often distributed in the GRIB format, which can be less user-friendly than the NetCDF format, commonly used in scientific computing. This blog post will walk you through the process of converting ERA5 GRIB files to NetCDF.

# Motivation

Climate change is one of the most pressing issues of our time, and understanding its impacts requires access to high-quality climate data. The ERA5 dataset provides crucial information on past and present climate conditions, helping researchers analyze trends and signals related to climate change. However, the ERA5 data is often stored in GRIB format, which can be challenging to work with. By converting ERA5 GRIB files to NetCDF format, we can more easily integrate climate change signals into ERA5.

By applying the Pseudo-Global-Warming (PGW) approach, you can use the CMIP6 climate change signals to generate a difference and then add this to the initial conditions, such as those provided by ERA5. 

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
   ```

3. **ERA5 GRIB File**: Download your desired ERA5 dataset in GRIB format from the ECMWF website.
4. New tool (not use): [Convert GRIB file to netCDF](https://confluence.ecmwf.int/display/OIFS/How+to+convert+GRIB+to+netCDF) 
   1. `grib_to_netcdf ICMGG_hybrid.grb -o ICMGG_hybrid.nc`
   2. `micromamba install conda-forge::eccodes -y`

## Steps to Convert GRIB to NetCDF

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

import xarray as xr

#============== SL =============================
grib_file = 'ERA5-SL-0p25-2024071400.grib'
ds = xr.open_dataset(grib_file, engine='cfgrib')

print(ds)

output_file = 'ERA5-SL.nc'
ds.to_netcdf(output_file)

#============== PL =============================
grib_file = 'ERA5-PL-0p25-2024071400.grib'
ds = xr.open_dataset(grib_file, engine='cfgrib')

print(ds)

output_file = 'ERA5-PL.nc'
ds.to_netcdf(output_file)
```

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

# Converting NetCDF Files back to GRIB Format: A Step-by-Step Guide

## Introduction

In the realm of climate data analysis, the ability to convert between different file formats is essential. While NetCDF is widely used for storing multidimensional scientific data, GRIB (Generalized Regularly-distributed Information in Binary form) is prevalent in meteorological applications. This blog will guide you through the process of converting NetCDF files back to GRIB format, ensuring you're equipped to handle various data formats in your climate studies.

## Motivation

The motivation behind converting NetCDF files to GRIB format can stem from several factors:

- **Compatibility**: Many meteorological models and tools predominantly use GRIB format. Converting your NetCDF data ensures compatibility with these systems.
- **Efficiency**: GRIB files are often more compact than their NetCDF counterparts, making them easier to store and share, especially when dealing with large datasets.
- **Standardization**: For specific applications, adhering to a standard format like GRIB can enhance data interoperability across different platforms and tools.

## Required Tools

Before you begin, ensure you have the following tools installed:

- **Python**: Ensure you have Python installed on your system.
- **xarray**: A Python library for handling NetCDF files.
- **cfgrib**: A Python library for reading and writing GRIB files.
- **cdo**: CLI tools to manipulate and analyse Climate and NWP model Data

```console
micromamba install conda-forge::cdo
```

and,
```
 $ cdo -v -f grb -copy new_ERA5_SL.nc new_ERA5_SL.grib
 OpenMP:  num_procs=24  max_threads=1
cdo    copy: Process file: new_ERA5_SL.nc
Warning (cdfInqContents): Coordinates variable number can't be assigned!
Warning (cdfInqContents): Coordinates variable time can't be assigned!
Warning (cdfInqContents): Coordinates variable step can't be assigned!
Warning (cdfInqContents): Coordinates variable surface can't be assigned!
encodeBMS_float   : Missing value = NaN is unsupported!
encodeBMS_double  : Missing value = NaN is unsupported!
cdo    copy: Processed 15573600 values from 15 variables over 1 timestep [1.40s 45MB].
```

## Verify the Conversion

After conversion, it’s crucial to verify that the GRIB file has been created correctly. You can do this by checking the contents:

```bash
grib_ls output_file.grib
```

## Further Reading

For more in-depth information, consider checking the official documentation for:

- [xarray](https://docs.xarray.dev/en/stable/)
- [cfgrib](https://cfgrib.readthedocs.io/en/latest/)
- [ecCodes](https://confluence.ecmwf.int/display/ECC/ecCodes)

---

# Interpolating 2D SST/SKT from Signal NetCDF to ERA5 NetCDF

Here’s a Python script for interpolating Sea Surface Temperature (SST) and Surface Skin Temperature (SKT) from a signal NetCDF file to match the ERA5 grid. This process involves horizontal interpolation to ensure that the temperatures align accurately with the finer resolution of the ERA5 dataset. Accurate temperature data is crucial for climate modeling and analysis. By interpolating these temperatures, we can effectively integrate them into broader climate studies.

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

import numpy as np
import xarray as xr
from scipy.interpolate import interp1d


# Load the signal and ERA5 NetCDF files
signal_nc = xr.open_dataset('signal.nc')      # Replace with actual signal file path
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
sst_signal = sst_signal.rename({'ncl3': 'latitude', 'ncl4': 'longitude'})

# Reverse the SST data along the latitude dimension
reversed_sst_signal = sst_signal[::-1, :]  # Assuming latitude is the first dimension

# Update the coordinates to reflect the new latitude order
reversed_latitude = lat_signal[::-1]  # Reverse latitude coordinates, from [-90,90] to [90,-90]

# Update the SST variable to use the reversed latitude
reversed_sst_signal = reversed_sst_signal.assign_coords(latitude=reversed_latitude.values, longitude=lon_signal.values)

# Define new latitude and longitude arrays for 0.25-degree grid
# Perform horizontal interpolation to 0.25-degree grid
reversed_sst_signal = reversed_sst_signal.interp(latitude=sst_era5.coords['latitude'].values, longitude=sst_era5.coords['longitude'].values)

# Add signal to ERA5
era5_nc['sst'] = sst_era5 + reversed_sst_signal

#================== skt ==================================================
print("=== processing: Skin temperature ")
# Update dimensions for signal_sst.nc
skt_signal = skt_signal.rename({'ncl3': 'latitude', 'ncl4': 'longitude'})

# Reverse the SST data along the latitude dimension
reversed_skt_signal = skt_signal[::-1, :]  # Assuming latitude is the first dimension

# Update the coordinates to reflect the new latitude order
# reversed_latitude = lat_signal[::-1]  # Reverse latitude coordinates (Done before!!)

# Update the SST variable to use the reversed latitude
reversed_skt_signal = reversed_skt_signal.assign_coords(latitude=reversed_latitude.values, longitude=lon_signal.values)

# Define new latitude and longitude arrays for 0.25-degree grid
# Perform horizontal interpolation to 0.25-degree grid
reversed_skt_signal = reversed_skt_signal.interp(latitude=sst_era5.coords['latitude'].values, longitude=sst_era5.coords['longitude'].values)

# Add signal to ERA5
era5_nc['skt'] = skt_era5 + reversed_skt_signal

#================== Save =================================================
# Save the result to a new NetCDF file
era5_nc.to_netcdf('new_ERA5_SL.nc')

print("Horizontal interpolation completed and saved to 'interpolated_SL.nc'.")
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
2. **Horizontal Interpolation**: Align the interpolated vertical data to the spatial grid (latitude and longitude) of the ERA5 dataset.

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

{% fold info @3d_interp.nc (Pressure level) %}
```console
#!/bin/python

#------------------------------------------------#
#Author:         wpsze
#Email：         wpsze
#date:           2025-02-04 16:31:50
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2025 All rights reserved
#------------------------------------------------#

import numpy as np
import xarray as xr
from scipy.interpolate import interp1d

# Load the signal and ERA5 NetCDF files
signal_nc = xr.open_dataset('signal.nc')  # Replace with actual signal file path
era5_nc = xr.open_dataset('ERA5-PL.nc')   # Replace with actual ERA5 file path

#================ Vertical levels =======================================
# Vertical levels
signal_levels = signal_nc['lev'].values/100            # Vertical levels from signal file,  lev(ncl14), convert to hPa unit
lat_signal = signal_nc['lat']
lon_signal = signal_nc['lon']

# Update the coordinates to reflect the new latitude order
reversed_latitude = lat_signal[::-1]  # Reverse latitude coordinates, from [-90,90] to [90,-90]

era5_levels = era5_nc['isobaricInhPa']          # Vertical levels from ERA5 file

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

print(signal_levels)
print(era5_levels[1:29].values)

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
ta_signal = ta_signal.rename({'ncl0': 'isobaricInhPa', 'ncl1': 'latitude', 'ncl2': 'longitude'})

# Reverse the TT data along the latitude dimension
reversed_ta_signal = ta_signal[:, ::-1, :]  # latitude is the second dimension

# Update the TT variable to use the reversed latitude
reversed_ta_signal = reversed_ta_signal.assign_coords(latitude=reversed_latitude.values, longitude=lon_signal.values)

# Perform vertical interpolation
interpolated_ta = vertical_interpolate(reversed_ta_signal, signal_levels, era5_levels)

# Define new latitude and longitude arrays for 0.25-degree grid
# Perform horizontal interpolation to 0.25-degree grid
interpolated_ta = interpolated_ta.interp(latitude=TT_era5.coords['latitude'].values, longitude=TT_era5.coords['longitude'].values)

# Add signal to ERA5
era5_nc['t'][1:29,:,:] = TT_era5[1:29,:,:] + interpolated_ta[1:29,:,:]

#================  ================================

# Save the result to a new NetCDF file
era5_nc.to_netcdf('new_ERA5_PL.nc')

print("Interpolation completed and saved to 'interpolated_PL.nc'.")
```
{% endfold %}

### Explanation of the Code

1. **Loading Data**: The script uses `xarray` to load the signal and ERA5 NetCDF files.
2. **Vertical Interpolation**: A function (`vertical_interpolate`) is defined to perform linear interpolation on the vertical levels of the temperature data.
3. **Horizontal Interpolation**: The `interp` method from `xarray` is used to interpolate the data to the ERA5 latitude and longitude grid.
4. **Saving the Result**: The interpolated temperature data is saved to a new NetCDF file.

## Conclusion
This interpolation approach allows for aligning temperature data from different datasets, which is essential for climate modeling. By accurately matching the grids of the signal and ERA5 data, we can effectively analyze and apply climate change signals in our research.

# References

1. [failed to set key 'endStep' in to_grib](https://github.com/ecmwf/cfgrib/issues/289)
2. [Add support to write a xarray.Dataset to a GRIB file.](https://github.com/ecmwf/cfgrib/issues/18)
   1. cfgrib.to_grib(ds, 'User_Guide_Example_Data_from_cfgrib.grib')#, grib_keys={'centre': 'ecmf'})
   2. `cfgrib/xarray_to_grib.py:252: FutureWarning: GRIB write support is experimental, DO NOT RELY ON IT!`
3. [Converting NetCDF to GRIB | 2014](https://code.mpimet.mpg.de/boards/1/topics/2907)
   1. `cdo -v -f grb -copy sstatlantic_addhifreq.nc ofile.grb`
   2. The large GRIB file has data with 2602x1402=3648004 grid points. CDO stores netCDF double precision floats to 24bit packed GRIB per default. The result is a GRIB record with 10944120 bytes. The max. record length in the GRIB1 standard is 8388607 bytes. **CDO uses the ECMWF extension of the GRIB1 standard for larger GRIB records**. These large GRIB records can only be read by CDO or tools from the ECMWF.
   3. You can create a 16bit GRIB record to reduce the record size:
      1. `cdo -b 16 -f grb copy file.nc file.grb`
4. Due to the fact that the NetCDF data model is much more free and extensible than the GRIB one, it is not possible to write a generic xarray.Dataset to a GRIB file. The aim for cfgrib is to implement write support for a subset of carefully crafted datasets that fit the GRIB data model. In particular the only coordinates that we target at the moment are the one returned by opening a GRIB with the cfgrib flavour of cfgrib.open_dataset, namely:number, time, step, a vertical coordinate (isobaricInhPa, heightAboveGround, surface, etc), and the horizontal coordinates (for example latitude and longitude for a regular_ll grid type).