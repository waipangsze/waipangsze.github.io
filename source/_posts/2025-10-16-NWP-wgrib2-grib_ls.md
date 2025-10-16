---
layout: post
title: NWP | wgrib2 | grib_ls
categories: [NWP]
tags: [MPAS, WRF, NWP, GFS, FNL, IFS, ERA5, CMA, wgrib2, grib_ls, grib]
author: wpsze
date: 2025-10-16 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/cdKnOPc.png
banner_img: https://i.imgur.com/cdKnOPc.png
---

# `wgrib2`

- <https://www.cpc.ncep.noaa.gov/products/wesley/wgrib2/>

**wgrib2** is a widely-used, free, and open-source command-line utility designed for the management and manipulation of **GRIB2 (GRIdded Binary) files**, which are the standard data format for distributing gridded meteorological and oceanographic data, like numerical weather prediction (NWP) model output.

Developed primarily by the National Oceanic and Atmospheric Administration's (NOAA) National Centers for Environmental Prediction (NCEP), its main purpose is to:

1.  **Read and Inspect GRIB2 Files:** It can generate a verbose inventory (or index file) of the contents of a GRIB2 file, showing parameters, levels, forecast times, and more.
2.  **Extract and Manipulate Data:** Users can select, filter, or subset data based on various criteria (e.g., variable name, pressure level, time, geographic area).
3.  **Output Conversion:** It can convert GRIB2 data to other formats, including:
    * Binary (IEEE format)
    * Text or CSV
    * NetCDF
4.  **Data Processing:** It includes numerous options for calculations, regridding (interpolating data to a different grid), and creating new GRIB2 fields.

Essentially, **wgrib2** is an indispensable tool for meteorologists and researchers who need to efficiently work with the often-large, complex binary files produced by weather and climate models. It also includes library interfaces for C, Fortran, and Python, allowing it to be integrated into other software applications.

## Using `wgrib2` (NCEP)

`wgrib2` uses specific inventory flags (`-s` or custom options) to output variable information. For GRIB2 files, it often provides the variable abbreviation, description, and unit directly in the inventory output.

### Command Syntax

```bash
wgrib2 -h              # misc        help, shows common options
```

{% fold info @Command Syntax %}
```
-ftime1          inv         forecast time
-full_name       inv         extended name, var+qualifiers
-grid            inv         grid definition
-grid_id         inv         show values from grid_id
-hybrid          inv         shows vertical coordinate parameters from Sec4
-JMA             inv         inventory for JMA locally defined PDT
-lev             inv         level (code table 4.5)
-max             inv         print maximum value
-min             inv         print minimum value
-misc            inv         variable name qualifiers like chemical, ensemble, probability, etc
-model_version_date inv         prints model date code
-n               inv         prints out inventory number
-N_ens           inv         number of ensemble member
-pdt             inv         Product Definition Table (Code Table 4.0)
-radius          inv         radius of Earth
-RT              inv         type of reference Time
-table           inv         parameter table
-V               inv         diagnostic output
-var             inv         short variable name
-varX            inv         raw variable name - discipline mastertab localtab center parmcat parmnum
-vector_dir      inv         grid or earth relative winds
-t               inv         reference time YYYYMMDDHH, -v2 for alt format
-T               inv         reference time YYYYMMDDHHMMSS
-vt              inv         verf time = reference_time + forecast_time, -v2 for alt format
-VT              inv         verf time = reference_time + forecast_time (YYYYMMDDHHMMSS)
-import_text     misc X      read text file (X) for data
-config          misc        shows the configuration
```
{% endfold %}

#### Standard Inventory (`-s` or `-v`)

```bash
wgrib2 your_grib2_file.grib2
wgrib2 your_grib2_file.grib2 -s
wgrib2 your_grib2_file.grib2 -v
```

  * **`-s`**: Prints a summary inventory. This output often includes the variable abbreviation, level, and forecast time, which is usually enough to identify the variable.
  * **`-v`**: Prints a more verbose inventory, which often includes the unit.

# `grib_ls`

- <https://confluence.ecmwf.int/display/ECC/grib_ls>

**`grib_ls`** is a fundamental command-line utility for inspecting and listing the content of GRIB (GRIdded Binary) files, which are the international standard format for the exchange of meteorological data.

It is part of the **ecCodes** package developed by the European Centre for Medium-Range Weather Forecasts (ECMWF), and is one of the core tools for interactive and batch processing of GRIB data.

Here is a summary of its key features and usage:

## 1\. Primary Function

The main purpose of `grib_ls` is to **list the values of specified keys (metadata) from one or more GRIB messages** within a file, presenting the information in a structured, column-based output.

## 2\. Key Characteristics

  * **Inspection Tool:** It's primarily a tool for quickly examining the header information of GRIB messages.
  * **Key/Value Approach:** It operates on a key/value system, where keys are used to access specific pieces of metadata (e.g., `shortName`, `dataDate`, `stepRange`, `level`).
  * **Non-Failing:** Unlike its counterpart `grib_get`, `grib_ls` is designed to be more robust for interactive use. It **does not fail** or return an error code if a requested key is not found in a message; instead, it will typically print a blank or missing value.

## 3\. Common Usage and Options

The basic syntax is:

```bash
grib_ls [options] grib_file [grib_file...]
```

Some of the most useful options include:

| Option | Description | Example Usage |
| :--- | :--- | :--- |
| **`-p`** | **Print Keys:** Specifies a comma-separated list of keys to print. | `grib_ls -p paramId,shortName,level file.grib` |
| **`-w`** | **Where Clause (Filter):** Filters the messages to be processed, only showing those that match specified key/value constraints. | `grib_ls -w shortName=t,level=850 file.grib` |
| **`-n`** | **Namespace:** Prints all keys belonging to a specific namespace (e.g., `ls`, `time`, `mars`). | `grib_ls -n mars file.grib` |
| **`-l`** | **Nearest Neighbour:** Finds and prints the data value(s) closest to a specified latitude/longitude point. | `grib_ls -l 51.46,-1.33,1 -p dataDate,value file.grib` |
| **`-B`** | **Order By:** Sorts the output based on one or more keys. | `grib_ls -B 'dataDate asc,step:i desc' file.grib` |
| **`-j`** | **JSON Output:** Prints the output in JSON format, useful for scripting. | `grib_ls -j file.grib` |

`grib_ls` is an essential tool for meteorologists and developers working with GRIB data, offering a quick way to understand the contents and structure of meteorological fields.

# NCEP WMO GRIB2 Documentation

- <https://www.nco.ncep.noaa.gov/pmb/docs/grib2/grib2_doc/>
- [GRIB2 - CODE TABLE 4.2](https://www.nco.ncep.noaa.gov/pmb/docs/grib2/grib2_doc/grib2_table4-2.shtml)

# References

1. [What is GRIB2? — Herbie 2023.3.0 documentation](https://herbie.readthedocs.io/en/2023.3.0/user_guide/grib2.html)
2. [GRIB 工具](https://perillaroc.github.io/eccodes-tutorial-cn/02-grib-tools/)
3. [GRIB API学习笔记01——GRIB简介](https://blog.perillaroc.wang/post/2013/2013-12-28-grib-apie5ada6e4b9a0e7ac94e8aeb001-gribe7ae80e4bb8b/)
4. [GRIB学习笔记：样例文件分析 - Section 1](https://blog.perillaroc.wang/post/2019/2019-05-29-grib-notebook-learn-by-sample-section1/)
5. [GRIB学习笔记：GRIB2解码 - 处理JPEG2000压缩](https://blog.perillaroc.wang/post/2019/2019-06-07-grib-notebook-decode-grib2-jpeg-packing/)
6. [GRIB学习笔记：添加自定义表格](https://blog.perillaroc.wang/post/2020/03/2020-03-28-grib-notebook-add-local-table/)