---
layout: post
title: WRF | WRF/WPS Geographical Static Data
categories: [WRF]
tags: [WRF,WPS,geodata]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/RwpxULg.png
banner_img: https://i.imgur.com/RwpxULg.png
---

# WRF geographical static Data

- [WPS V4 Geographical Static Data Downloads Page](https://www2.mmm.ucar.edu/wrf/users/download/get_sources_wps_geog.html)

These are all the mandatory fields required to run WPS and WRF.

All are under:

- <https://www2.mmm.ucar.edu/wrf/src/wps_files/>

Download links:

- <http://www2.mmm.ucar.edu/wrf/src/wps_files/geog_complete.tar.bz2>
  - This is a large compressed file (≈ 2.3 GB)
  - This file will expand to about 50GB in size.
  - bunzip2 geog complete.tar.bz2
  - tar -xf geog complete.tar

- <https://www2.mmm.ucar.edu/wrf/users/download/get_sources_wps_geog.html>
  - Highest Resolution of each Mandatory Field (*Note: ~29G Uncompressed (2.6G Compressed))
  - WPS Geographical Input Data Mandatory for Specific Applications
  - Optional WPS Geographical Input Data

For the WPS geographical input data download sets that correspond to **Version 3** of the WPS/WRF code.

- <https://www2.mmm.ucar.edu/wrf/users/download/get_sources_wps_geog_V3.html>

## Landuse

[References for geogrid static data](https://forum.mmm.ucar.edu/threads/references-for-geogrid-static-data.168/)

The Noah LSM page identifies the sources of data that they use. We believe, with high probability, that we are using the same. The topography data is definitely the USGS GTOPO30, the soil category data is probably the hybrid STATSGO/FAO, and the land-cover data is probably the USGS GLCC v2 land-use/land-cover data. For future reference, here is a link to the Noah page: [Land Atmosphere Interactions | Research Applications Laboratory](http://www.ral.ucar.edu/research/land/technology/lsm.php)

The 30-arc-second MODIS land use datasets (both with and without lakes) are probably derived from the "Modified IGBP MODIS" data in the Noah link.

To the best of our knowledge, all of the land cover data in the 'landuse_*' directories are the USGS GLCC data from 1992-1993. The coarser resolutions are derived from the 30-arc-second data.

The data in the 'modis_landuse_20class_15s' directory is based on:

- (1) Broxton, P. D., Zeng, X., Sulla-Menashe, D., & Troch, P. A. (2014). A 

global land cover climatology using MODIS data. Journal of Applied Meteorology and Climatology, 53(6), 1593-1605. DOI: 10.1175/JAMC-D-13-0270.1
and

- (2) ftp://ftp.emc.ncep.noaa.gov/mmb/gcp/ldas/noahlsm/README

The data in the 'modis_landuse_20class_30s' directory were provided by NCEP and include the addition of a tundra class; these data were most likely collected in 2001.

The data in the 'modis_landuse_20class_30s_with_lakes' directory are the same as in the 'modis_landuse_20class_30s' directory, but have an inland water body category that was added by NCAR.

The data in the 'modis_landuse_21class_30s' data were modified by Boston University, most likely based on data collected in 2004.

### In summary:

```
landuse_30s - USGS GLCC, 1992-1993
landuse_30s_with_lakes - USGS GLCC, 1992-1993, lakes added by NCAR
landuse_2m - USGS GLCC, 1992-1993, coarsened from 30s
landuse_5m - USGS GLCC, 1992-1993, coarsened from 30s
landuse_10m - USGS GLCC, 1992-1993, coarsened from 30s

modis_landuse_20class_15s - MODIS, See reference
modis_landuse_20class_30s - MODIS, NCEP included tundra class, 2001
modis_landuse_20class_30s_with_lakes - MODIS, NCEP included tundra class, 2001, lakes added by NCAR
modis_landuse_21class_30s - MODIS, modified by BU, 2004
```

# Interpolation options for different resolution

(1) Is there an algorithm in geogrid/src that compares the original binary data resolution to the model resolution to apply the interpolation option differently?

-  No, I don't think so. The interpolation option is always dependent on that specified in GEOGRID.TBL

(2) If not, is there an algorithm that applies appropriate interpolation options when receiving categorical or continuous variables?

- For both categorical and continuous variables, the interpolation is conducted based on the option given in GEOGRID.TBL.

# References

1. [Advanced Usage of the WRF Preprocessing System](https://www2.mmm.ucar.edu/wrf/users/tutorial/presentation_pdfs/202101/duda_wps_advanced.pdf)

