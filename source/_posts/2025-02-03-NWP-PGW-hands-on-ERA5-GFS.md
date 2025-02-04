---
layout: post
title: NWP | Pseudo-Global-Warming (PGW) hands-on | ERA5 | GFS
categories: [NWP]
tags: [MPAS, NWP, WRF, ERA5, GFS, GRIB, NetCDF, CMIP6]
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

### Step 1: Import Required Libraries

Start your Python script or Jupyter Notebook by importing the necessary libraries.

```python
import xarray as xr
```

### Step 2: Load the GRIB File

Use the `xarray` library to open the GRIB file. Replace `your_file.grib` with the path to your GRIB file.

```python
grib_file = 'your_file.grib'
ds = xr.open_dataset(grib_file, engine='cfgrib')
```

### Step 3: Inspect the Data

It’s good practice to check the structure of your dataset before converting it. Use the following command to view the dataset's dimensions, coordinates, and variables.

```python
print(ds)
```

### Step 4: Save as NetCDF

Once you are satisfied with the dataset, you can save it in NetCDF format. Specify the desired output filename:

```python
output_file = 'output_file.nc'
ds.to_netcdf(output_file)
```

### Step 5: Verify the Conversion

To ensure that the conversion was successful, you can reopen the NetCDF file and inspect its contents.

```python
ds_netcdf = xr.open_dataset(output_file)
print(ds_netcdf)
```

## Conclusion

Converting ERA5 GRIB files to NetCDF format is straightforward with the use of Python and the `xarray` library. This conversion allows for easier manipulation and analysis of climate data. Whether you're conducting research or analyzing weather patterns, using NetCDF can significantly enhance your workflow.

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
- **ecCodes**: A library provided by ECMWF for encoding and decoding GRIB messages.

You can install the necessary Python packages using pip:

```bash
micromamba install xarray cfgrib
```

## Conversion Steps

### Step 1: Load the NetCDF File

First, you need to load the NetCDF file using `xarray`:

```python
import xarray as xr

# Load the NetCDF file
netcdf_file = 'your_file.nc'
data = xr.open_dataset(netcdf_file)
```

### Step 2: Prepare the Data

Ensure that the data in the NetCDF file is structured properly for GRIB output. Some adjustments may be necessary to match the GRIB conventions.

### Step 3: Convert to GRIB Format

Use the `cfgrib` library to convert the data to GRIB format:

```python
import cfgrib

# Save to GRIB format
grib_file = 'output_file.grib'
data.to_netcdf(grib_file, format='GRIB')
```

### Step 4: Verify the Conversion

After conversion, it’s crucial to verify that the GRIB file has been created correctly. You can do this by checking the contents:

```bash
grib_ls output_file.grib
```

- `conda install conda-forge::eccodes`
- [grib_ls](https://confluence.ecmwf.int/display/ECC/grib_ls): List content of GRIB files printing values of some keys. It does not fail when a key is not found.



## Conclusion

Converting NetCDF files to GRIB format is a straightforward process that enhances data interoperability and efficiency in meteorological applications. By following the steps outlined in this guide, you can seamlessly transition between these two formats, ensuring your climate data analysis remains robust and versatile.

## Further Reading

For more in-depth information, consider checking the official documentation for:

- [xarray](https://docs.xarray.dev/en/stable/)
- [cfgrib](https://cfgrib.readthedocs.io/en/latest/)
- [ecCodes](https://confluence.ecmwf.int/display/ECC/ecCodes)

---

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

Below is a Python script that performs the required interpolations.

```python
import numpy as np
import xarray as xr
from scipy.interpolate import interp1d

# Load the signal and ERA5 NetCDF files
signal_nc = xr.open_dataset('signal.nc')  # Replace with actual signal file path
era5_nc = xr.open_dataset('era5.nc')      # Replace with actual ERA5 file path

# Extract variables
ta_signal = signal_nc['ta']  # 3D temperature from signal file
TT_era5 = era5_nc['TT']       # 3D temperature from ERA5 file

# Vertical levels
signal_levels = signal_nc['level'].values  # Vertical levels from signal file
era5_levels = era5_nc['level'].values        # Vertical levels from ERA5 file

# Interpolation function for vertical levels
def vertical_interpolate(temperature, signal_levels, target_levels):
    interpolated_temp = np.empty((temperature.shape[0], target_levels.size, temperature.shape[2], temperature.shape[3]))
    for i in range(temperature.shape[0]):  # Iterate over time
        f = interp1d(signal_levels, temperature[i, :, :, :], axis=0, kind='linear', fill_value='extrapolate')
        interpolated_temp[i, :, :, :] = f(target_levels)
    return interpolated_temp

# Perform vertical interpolation
interpolated_ta = vertical_interpolate(ta_signal.values, signal_levels, era5_levels)

# Create a new DataArray for the interpolated temperature
interpolated_ta_da = xr.DataArray(interpolated_ta, coords=[ta_signal.coords['time'], era5_levels, TT_era5.coords['latitude'], TT_era5.coords['longitude']], dims=['time', 'level', 'latitude', 'longitude'])

# Horizontal interpolation (using xarray's interpolation)
interpolated_ta_da = interpolated_ta_da.interp(latitude=TT_era5.coords['latitude'], longitude=TT_era5.coords['longitude'])

# Save the result to a new NetCDF file
interpolated_ta_da.to_netcdf('interpolated_temperature.nc')

print("Interpolation completed and saved to 'interpolated_temperature.nc'.")
```

### Explanation of the Code

1. **Loading Data**: The script uses `xarray` to load the signal and ERA5 NetCDF files.
2. **Vertical Interpolation**: A function (`vertical_interpolate`) is defined to perform linear interpolation on the vertical levels of the temperature data.
3. **Horizontal Interpolation**: The `interp` method from `xarray` is used to interpolate the data to the ERA5 latitude and longitude grid.
4. **Saving the Result**: The interpolated temperature data is saved to a new NetCDF file.

## Conclusion
This interpolation approach allows for aligning temperature data from different datasets, which is essential for climate modeling. By accurately matching the grids of the signal and ERA5 data, we can effectively analyze and apply climate change signals in our research.