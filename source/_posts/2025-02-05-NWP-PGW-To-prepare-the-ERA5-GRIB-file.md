---
layout: post
title: NWP | PGW | To prepare the ERA5 GRIB file
categories: [NWP]
tags: [MPAS, NWP, WRF, PGW, ERA5, GFS, GRIB, NetCDF, CMIP6]
author: wpsze
date: 2025-02-05 15:33:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/kcuHfQW.png
banner_img: https://i.imgur.com/kcuHfQW.png
---

{% note primary %}
[**NWP | Pseudo-Global-Warming (PGW) hands-on | ERA5 | GFS**](https://waipangsze.github.io/2025/02/03/NWP-PGW-hands-on-ERA5-GFS/)
{% endnote %}

{% note primary %}
[**NWP | ERA5 | GRIB1 and GRIB2**](https://waipangsze.github.io/2025/02/05/NWP-ERA5-GRIB1-GRIB2/)
{% endnote %}

To prepare the `ERA5` GRIB file from the modified `era5_with_signal.nc`, you need to specify GRIB metadata such as `dataDate`, `shortName`, `level`, `dataType`, and `typeOfLevel` when converting `era5_with_signal.nc` back to GRIB format. The process involves using **`cdo`** and **`grib_set`** from ecCodes to properly define these GRIB headers.

Below is the detailed step-by-step guide:

---

### **Step 1: Check Metadata of the Original GRIB File**
Inspect the original `ERA5` GRIB file (`era5.grib`) to understand the required metadata.

```bash
grib_ls era5.grib
```

This will give you information about:
- `dataDate`: The date of the data (e.g., 20250101 for January 1, 2025).
- `shortName`: The short name of the variable (e.g., `t2m` for 2-meter temperature).
- `level`: The model level, pressure level, or surface (e.g., `2` for surface data).
- `dataType`: The type of data, such as `an` (analysis) or `fc` (forecast).
- `typeOfLevel`: Specifies the level type (e.g., `surface`, `isobaricInhPa`, etc.).

Ensure you note these details.

---

### **Step 2: Convert `era5_with_signal.nc` to GRIB Format**
Use **`cdo`** to convert the modified NetCDF file to GRIB format. You can specify metadata such as short names, levels, and GRIB options directly.

```bash
cdo -f grb copy era5_with_signal.nc era5_with_signal.grib
```

This creates a basic GRIB file, but it may not yet have all the required metadata (e.g., `dataDate`, `shortName`, `typeOfLevel`). You can fix those in the next steps.

---

### **Step 3: Assign GRIB Metadata**
Use **`grib_set`** to modify GRIB metadata. First, inspect the GRIB file to see the current metadata:

```bash
grib_ls era5_with_signal.grib
```

If metadata like `dataDate`, `shortName`, or `typeOfLevel` is incorrect or missing, update them as follows:

#### Example 1: Set `dataDate` and `shortName`
Set the date and variable short name (e.g., for `2-meter temperature`):

```bash
grib_set -s dataDate=20250101,shortName=t2m era5_with_signal.grib era5_with_signal_updated.grib
```

#### Example 2: Set `typeOfLevel` and `level`
If the `typeOfLevel` (e.g., `surface`) or `level` (e.g., `2`) is missing or incorrect, set them:

```bash
grib_set -s typeOfLevel=surface,level=2 era5_with_signal_updated.grib era5_with_signal_final.grib
```

---

### **Step 4: Verify the Final GRIB File**
After updating the metadata, verify the contents of the resulting GRIB file:

```bash
grib_ls era5_with_signal_final.grib
```

Check that all metadata (e.g., `dataDate`, `shortName`, `level`, etc.) matches the desired values.

---

### **Step 5: Automate Metadata Assignment (Optional)**
If you need to assign multiple metadata fields at once, you can use a configuration file with `grib_set` or write a script to automate the process.

#### Example: Bulk Metadata Update Script
Create a script to set multiple fields:

```bash
#!/bin/bash

INPUT_GRIB="era5_with_signal.grib"
OUTPUT_GRIB="era5_with_signal_final.grib"

# Update metadata
grib_set -s dataDate=20250101,shortName=t2m,typeOfLevel=surface,level=2 $INPUT_GRIB $OUTPUT_GRIB

# Verify metadata
grib_ls $OUTPUT_GRIB
```

Save this as `update_grib_metadata.sh`, make it executable, and run it:

```bash
chmod +x update_grib_metadata.sh
./update_grib_metadata.sh
```

---

### **Step 6: Handling Multiple Variables**
If your `era5_with_signal.nc` contains multiple variables (e.g., temperature `t2m`, wind speed `u10`, etc.), you need to split the file by variable, assign correct metadata, and merge them back into one GRIB file.

#### Split by Variable:
```bash
cdo selname,t2m era5_with_signal.nc t2m.nc
cdo selname,u10 era5_with_signal.nc u10.nc
```

#### Convert Each Variable to GRIB:
```bash
cdo -f grb copy t2m.nc t2m.grib
cdo -f grb copy u10.nc u10.grib
```

#### Set Metadata for Each GRIB File:
```bash
grib_set -s dataDate=20250101,shortName=t2m,typeOfLevel=surface,level=2 t2m.grib t2m_final.grib
grib_set -s dataDate=20250101,shortName=u10,typeOfLevel=surface,level=10 u10.grib u10_final.grib
```

#### Merge the GRIB Files:
```bash
cdo merge t2m_final.grib u10_final.grib era5_with_signal_final.grib
```

---

### **Summary of Commands**
1. Convert NetCDF to GRIB:
   ```bash
   cdo -f grb copy era5_with_signal.nc era5_with_signal.grib
   ```

2. Assign GRIB metadata:
   ```bash
   grib_set -s dataDate=YYYYMMDD,shortName=VAR,typeOfLevel=LEVEL_TYPE,level=LEVEL era5_with_signal.grib era5_with_signal_final.grib
   ```

3. Verify the final GRIB file:
   ```bash
   grib_ls era5_with_signal_final.grib
   ```

This process ensures that your modified ERA5 file is correctly formatted with all necessary GRIB metadata. Let me know if you encounter any issues!


# ** Hands-on: wpsze

```ssh
#!/bin/bash

#------------------------------------------------#
#Author:         wpsze
#Email：         wpsze
#date:           2025-02-06 08:41:43
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2025 All rights reserved
#------------------------------------------------#

#================= SL ====================================
cdo selname,sst new_ERA5_SL.nc sst_nan.nc
cdo selname,skt new_ERA5_SL.nc skt_nan.nc

cdo setmissval,-999 sst_nan.nc sst.nc
cdo setmissval,-999 skt_nan.nc skt.nc

# cdo pack sst.nc sst_test.nc
# cdo pack skt.nc skt_test.nc

cdo -f grb copy sst.nc sst.grib
cdo -f grb copy skt.nc skt.grib

cdo showname sst.grib
cdo showname skt.grib

grib_set -s dataDate=20240714,shortName=sst,typeOfLevel=surface,level=0 sst.grib sst_final.grib
grib_set -s dataDate=20240714,shortName=skt,typeOfLevel=surface,level=0 skt.grib skt_final.grib

cdo showname sst_final.grib
cdo showname skt_final.grib

cdo delname,var34,var235 original-SL.grib original-SL_without_vars.grib

rm era5_with_signal_final_SL.grib
cdo merge sst_final.grib skt_final.grib original-SL_without_vars.grib era5_with_signal_final_SL.grib

cdo showname era5_with_signal_final_SL.grib

#================= PL: t ====================================
cdo selname,t new_ERA5_PL.nc t_nan.nc

cdo setmissval,-999 t_nan.nc t.nc

# cdo pack t.nc t_test.nc

# How to ensure the order of levels
# encodeBMS_float   : Missing value = NaN is unsupported!
cdo -f grb copy t.nc t_lev.grib
cdo invertlev t_lev.grib t.grib

cdo showname t.grib

grib_set -s dataDate=20240714,shortName=t,typeOfLevel=isobaricInhPa t.grib t_final.grib

cdo showname t_final.grib

##cdo delname,var130,var157 original-PL.grib original-PL_without_vars.grib
##cdo merge t_final.grib original-PL_without_vars.grib era5_with_signal_final_PL.grib
##cdo showname era5_with_signal_final_PL.grib

#================= PL: r ====================================
cdo selname,r new_ERA5_PL.nc r_nan.nc

cdo setmissval,-999 r_nan.nc r.nc

# cdo pack r.nc r_test.nc

# How to ensure the order of levels
# encodeBMS_float   : Missing value = NaN is unsupported!
cdo -f grb copy r.nc r_lev.grib
cdo invertlev r_lev.grib r.grib

cdo showname r.grib

grib_set -s dataDate=20240714,shortName=r,typeOfLevel=isobaricInhPa r.grib r_final.grib

cdo showname r_final.grib

#================= PL: combine t, r ====================================
cdo delname,var130,var157 original-PL.grib original-PL_without_vars.grib

rm era5_with_signal_final_PL.grib
cdo merge t_final.grib r_final.grib original-PL_without_vars.grib era5_with_signal_final_PL.grib

cdo showname era5_with_signal_final_PL.grib
```

## Debug

### encodeBMS_double : Missing value = NaN is unsupported!

- [CDO NetCDF to Grib files | 2013](https://code.mpimet.mpg.de/boards/1/topics/1628)

**NaN is not allowed in the grib standard,** but you can change it with cdo:

```console
[ram@thingol:~/tar/downloads]cdo setmissval,-9.e38 oscar_vel7403.nc t.nc
cdo setmissval: Processed 2310724 values from 4 variables over 1 timestep ( 0.12s )
```

and then convert it to grib,

```console
[ram@thingol:~/tar/downloads]cdo -f grb copy t.nc oscar_vel7403.grb            
cgribexDefLevel    : Changed zaxis type from generic to pressure
cdo copy: Processed 2310724 values from 4 variables over 1 timestep ( 0.09s )
```

Some lines on grib and netcdf:

GRIB1/GRIB2 are international standards for climate data sets defined by the WMO. Every data set has to have some identifiers (mostly integers), which describe what the data is (physical variable,unit,...). See here for a docu on the definitions. For example, NaN is not allowed for a missing value in the GRIB1 standard. In contrast to this Netcdf is self-explanatory: The user can defined, which name, unit or other (freely definable) attributes a certain variable should have. That's why it's not always possible to convert netcdf to grib.

### NetCDF: Numeric conversion not representable

```console
 $ ncview sst.nc
Ncview 2.1.7 David W. Pierce  29 March 2016
http://meteora.ucsd.edu:80/~pierce/ncview_home_page.html
Copyright (C) 1993 through 2015, David W. Pierce
Ncview comes with ABSOLUTELY NO WARRANTY; for details type `ncview -w'.
This is free software licensed under the Gnu General Public License version 3; type `ncview -c' for redistribution details.

Note: no Ncview app-defaults file found, using internal defaults
netcdf_fi_get_data: error on nc_get_vara_float call
cdfid=65536   variable=sst
start, count:
[0]: 0  721
[1]: 0  1440
NetCDF: Numeric conversion not representable
```

- [NetCDF: Numeric conversion not representable](https://code.mpimet.mpg.de/boards/1/topics/165)
- [netCDF with packed data](https://code.mpimet.mpg.de/projects/cdo/wiki#netCDF-with-packed-data)
  - Packing reduces the data volume by reducing the precision of the stored numbers. In NetCDF it is implemented using the attributes add_offset and scale_factor. CDO supports NetCDF files with packed data but can not automatically repack the data. That means the attributes add_offset and scale_factor are never changed. If you are using a CDO operator which change the range of the data you also have to take care that the modified data can be packed with the same add_offset and scale_factor. Otherwise the result could be wrong. You will get the following error message if some data values are out of the range of the packed datatype:
  - `Error (cdf_put_vara_double) : NetCDF: Numeric conversion not representable`
  - In this case you have to change the data type to **single or double precision floating-point.** This can be done with the CDO option `-b F32` or `-b F64`.
  - As of CDO release 2.3.0, NetCDF data is always written out unpacked if the operator changes the range of the data. Use the new operator pack to repack the data if required:
  - `cdo pack -<operator>  infile outfile`

```console
 $ cdo pack sst.nc sst_test.nc
cdo    pack: Processed 1038240 values from 1 variable over 1 timestep [0.29s 54MB].

-rw-rw-r-- 1 wpsze wpsze 8.0M Feb  6 11:16 sst.nc
-rw-rw-r-- 1 wpsze wpsze 2.1M Feb  6 11:51 sst_test.nc
```

> - Packing reduces the data volume by reducing the precision of the stored numbers
> - It reduces the precision, so not apply `cdo pack`

{% fold info @ncdump -h sst*.nc %}
```console
 $ ncdump -h sst.nc
netcdf sst {
dimensions:
	longitude = 1440 ;
	latitude = 721 ;
variables:
	double sst(latitude, longitude) ;
		sst:coordinates = "depthBelowLandLayer" ;
		sst:_FillValue = -Infinity ;
		sst:missing_value = -Infinity ;


 $ ncdump -h sst_test.nc 
netcdf sst_test {
dimensions:
	longitude = 1440 ;
	latitude = 721 ;
variables:
	short sst(latitude, longitude) ;
		sst:coordinates = "depthBelowLandLayer" ;
		sst:add_offset = 294.354168667848 ;
		sst:scale_factor = 0.000648260007869365 ;
		sst:_FillValue = -32767s ;
		sst:missing_value = -32767s ;
```
{% endfold %}

then, `ncview sst_test.nc` is successful. 