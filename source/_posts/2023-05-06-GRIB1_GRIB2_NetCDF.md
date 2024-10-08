---
layout: post
title: WRF | GRIB1, GRIB2, NetCDF
categories: [WRF]
tags: [WRF,WPS,HPC,MPAS]
mathjax: true
author: wpsze
index_img: 
banner_img: 
---

# GRIB1, GRIB2, NetCDF

Ref: [GRIB1,	GRIB2,	NetCDF:	Which	do	I	choose?	](https://rda.ucar.edu/OS/web/datasets/ds083.2/docs/README_Formats.pdf)

Your choice of data format depends on the date range that you need and what you want to do with the data. GRIB is a World Meteorological Organization (WMO) international standard for exchanging GRidded BInary data. 

**GRIB1** is the original format and requires a separate parameter table to unpack the data. **GRIB2** improves upon the standard with file compression and the inclusion of the metadata/parameter table that you need to unpack the data in each file. **GRIB2 exploits the same compression software commonly used for images to gain a roughly 50% reduction in Hile size over GRIB1. **

If you are interested in **2007.12.06** or later, then choose GRIB2 because of its smaller size. For earlier dates, use GRIB1. If you are using software that reads NetCDF but not GRIB, then convert the files to NetCDF *.nc format. Unless you need to use NetCDF tools, use GRIB2 for its efficiency. The WRF mesoscale model can use either GRIB1 or GRIB2. Newer versions of WRF Preprocessing System (WPS) can recognize FNL GRIB1 and GRIB2; WPS will even provide the appropriate parameter table for FNL in GRIB1 format so you do not need to do so manually. 

If you are using the older MM5 mesoscale model, use GRIB1. You can perform many data processing tasks on GRIB files with wgrib (for GRIB1) and wgrib2 (for GRIB2). 

You can process and visualize GRIB Hiles with GRADS, IDL, Panoply, and R (using the rNOMADS package). 

You can use the extensive NetCDF software libraries if you convert the Hiles to NetCDF. However, be mindful that NetCDF Hiles are not as compact as GRIB2, even when compressed.
