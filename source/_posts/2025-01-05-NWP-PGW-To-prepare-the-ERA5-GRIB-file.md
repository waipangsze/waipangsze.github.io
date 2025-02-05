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
index_img: 
banner_img: 
---

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


# wpsze (todo)

```console
cdo showname sst.grib
cdo showname sst_final.grib

cdo selname,sst era5_with_signal.nc sst.nc
cdo selname,skt era5_with_signal.nc skt.nc

cdo -f grb copy sst.nc sst.grib
cdo -f grb copy skt.nc skt.grib

grib_set -s dataDate=20240714,shortName=sst sst.grib sst_x.grib

grib_set -s dataDate=20240714,shortName=sst,typeOfLevel=surface,level=0 sst.grib sst_final.grib
grib_set -s dataDate=20240714,shortName=skt,typeOfLevel=surface,level=0 skt.grib skt_final.grib

grib_set -s dataDate=20240714,shortName=sst,typeOfLevel=surface,level=0,TR=0 sst.grib sst_final.grib

sst_final.grib
edition      centre       typeOfLevel  level        dataDate     stepRange    shortName    packingType  gridType     
1            ecmf         surface      0            20240714     0            sst          grid_simple  regular_ll  
1 of 1 messages in sst_final.grib

cdo delname,var34 original-SL.grib original-SL_without_vars.grib

cdo merge sst_final.grib original-SL_without_vars.grib era5_with_signal_final.grib
cdo merge sst_final.grib skt_final.grib original-SL_without_vars.grib era5_with_signal_final.grib
```