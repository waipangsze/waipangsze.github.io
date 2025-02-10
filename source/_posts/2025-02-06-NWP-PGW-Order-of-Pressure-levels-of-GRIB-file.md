---
layout: post
title: NWP | PGW | Order of Pressure levels of GRIB file
categories: [NWP]
tags: [MPAS, NWP, WRF, PGW, ERA5, GFS, GRIB, NetCDF, CMIP6]
author: wpsze
date: 2025-02-06 15:04:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/yzuPf2q.png
banner_img: https://i.imgur.com/yzuPf2q.png
---

{% note primary %}
[**NWP | Pseudo-Global-Warming (PGW) hands-on | ERA5 | GFS**](https://waipangsze.github.io/2025/02/03/NWP-PGW-hands-on-ERA5-GFS/)
{% endnote %}

{% note primary %}
[**NWP | ERA5 | GRIB1 and GRIB2**](https://waipangsze.github.io/2025/02/05/NWP-ERA5-GRIB1-GRIB2/)
{% endnote %}

In GRIB files, the order of pressure levels is important because many tools expect them to be sorted either in ascending (from surface to top of the atmosphere) or descending (from top of the atmosphere to surface) order. Here's how you can **ensure the order of pressure levels** and **reverse them** using tools like `cdo` or `grib_set`.

---

### **Step 1: Check the Current Order of Pressure Levels**

To inspect the order of pressure levels in the GRIB file, use the following commands:

1. **Using `grib_ls`**:
   ```bash
   grib_ls -p level era5_with_signal.grib
   ```
   This lists the levels (`level`) in the GRIB file. Check if they are in ascending or descending order.

2. **Using `cdo`**:
   ```bash
   cdo showlevel era5_with_signal.grib
   ```
   This outputs the pressure levels in the order they appear in the file.

---

### **Step 2: Ensure Ascending or Descending Order**

If the pressure levels are not in the desired order, you can reorder them using `cdo`.

#### **Ensure Ascending Order (Surface to Top of Atmosphere)**
To reorder pressure levels in ascending order (e.g., 1000 hPa → 850 hPa → ... → 10 hPa):

```bash
cdo vertsort era5_with_signal.grib era5_sorted_ascending.grib
```

The `vertsort` operation ensures that the levels are sorted in ascending order.

---

#### **Ensure Descending Order (Top of Atmosphere to Surface)**
To reorder pressure levels in descending order (e.g., 10 hPa → 100 hPa → ... → 1000 hPa), use the `vertsort` operation and then reverse the order:

```bash
cdo invertlev era5_with_signal.grib era5_sorted_descending.grib
```

The `invertlev` operation reverses the order of the vertical levels.

---

### **Step 3: Verify the New Order**

After reordering the levels, verify the result:

1. **Using `grib_ls`**:
   ```bash
   grib_ls -p level era5_sorted_ascending.grib
   grib_ls -p level era5_sorted_descending.grib
   ```

2. **Using `cdo`**:
   ```bash
   cdo showlevel era5_sorted_ascending.grib
   cdo showlevel era5_sorted_descending.grib
   ```

Ensure that the levels are in the desired order.

---

### **Step 4: For NetCDF Files**

If you are working with a NetCDF file (`era5_with_signal.nc`) and need to reorder the pressure levels:

#### **Ascending Order**
Use `cdo vertsort` for NetCDF files as well:

```bash
cdo vertsort era5_with_signal.nc era5_sorted_ascending.nc
```

#### **Descending Order**
Use `cdo invertlev` to reverse the order:

```bash
cdo invertlev era5_with_signal.nc era5_sorted_descending.nc
```

---

### Additional Tips:

1. **Regrid if Necessary**:
   If the pressure levels are missing or inconsistent, you can interpolate them to a standard set using `cdo intlevel`. For example:

   ```bash
   cdo intlevel,1000,850,500,250 era5_with_signal.nc era5_regridded_levels.nc
   ```

2. **Metadata Consistency**:
   Ensure that the `typeOfLevel` (e.g., `isobaricInhPa`) is correctly set, especially after reordering or interpolating levels. Use `grib_set` if needed:

   ```bash
   grib_set -s typeOfLevel=isobaricInhPa era5_sorted_ascending.grib era5_final.grib
   ```

---

### Summary of Commands:

1. **Check the current order of levels**:
   ```bash
   grib_ls -p level era5_with_signal.grib
   cdo showlevel era5_with_signal.grib
   ```

2. **Ensure ascending order (surface to top)**:
   ```bash
   cdo vertsort era5_with_signal.grib era5_sorted_ascending.grib
   ```

3. **Ensure descending order (top to surface)**:
   ```bash
   cdo invertlev era5_with_signal.grib era5_sorted_descending.grib
   ```

4. **Verify the new order**:
   ```bash
   grib_ls -p level era5_sorted_ascending.grib
   cdo showlevel era5_sorted_ascending.grib
   ```

5. **For NetCDF files**:
   ```bash
   cdo vertsort era5_with_signal.nc era5_sorted_ascending.nc
   cdo invertlev era5_with_signal.nc era5_sorted_descending.nc
   ```

# References

1. [Climate Data Operators (CDO) Tutorial (**recommend**)](https://code.mpimet.mpg.de/projects/cdo/wiki/tutorial#Basic-Usage)