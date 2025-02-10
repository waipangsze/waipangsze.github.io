---
layout: post
title: NWP | ERA5 | GRIB1 and GRIB2
categories: [NWP]
tags: [MPAS, NWP, WRF, PGW, ERA5, GFS, GRIB1, GRIB2, NetCDF, CMIP6]
author: wpsze
date: 2025-02-05 16:35:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/yzuPf2q.png
banner_img: https://i.imgur.com/yzuPf2q.png
---

{% note primary %}
[**WRF | GRIB1, GRIB2, NetCDF**](https://waipangsze.github.io/2023/05/06/GRIB1_GRIB2_NetCDF/)
{% endnote %}

{% note primary %}
[**ERA5 CDS upgrade (2024 Sep)**](https://waipangsze.github.io/2024/08/16/ERA5-2024-upgrade/)
{% endnote %}

GRIB is a WMO format for gridded data. GRIB is used by the operational meteorological centers for storage and the exchange of gridded fields. GRIB's major advantages are files are typically 1/2 to 1/3 of the size of normal binary files (floats), the fields are self describing, and GRIB is an open, international standard.

GRIB stands for "General Regularly distributed Information in Binary form" and is a WMO (World Meteorological Organisation) standard format for archiving and exchanging gridded data. GRIB is a binary format, and the data is packed to increase storage efficiency. GRIB messages are often concatenated together to form a GRIB file. GRIB files usually have the extension .grib, .grb or .gb.

Currently there are two different coding standards: GRIB edition 1 (commonly referred to as GRIB1) and GRIB edition 2 (GRIB2). The major differences are in the structure of the messages; in GRIB2, several variables are defined with more precision (e.g. in GRIB1, latitudes and longitudes are in milli-degrees while in GRIB2, they are in micro-degrees). Also in GRIB2, longitude values must lie between 0 and 360 degrees), the encoding of the parameter is very different, and in GRIB2 the description of the data is template/table based. Note that a GRIB file can contain a mix of GRIB1 and GRIB2 messages.

The ECMWF model (the Integrated Forecasting System, IFS) currently outputs model-level fields in GRIB2 while pressure and surface level outputs are produced in GRIB1. For example,ERA-Interim (a climate reanalysis dataset provided by ECMWF) is produced in the GRIB edition 1 format. The ERA-Interim data is then made available for download in its native GRIB format.

> In some cases, data is also available in NetCDF format as the result of the conversion of the GRIB file to NetCDF. Note that due to this conversion, not all the information in the GRIB file will be included in the NetCDF version, and his is particularly true for the GRIB file metadata. As a result, care should be taken when using these files. At this time, the NetCDF format is not formally supported by ECMWF.

ECMWF provides and supports [ecCodes](https://confluence.ecmwf.int/display/ECC/ecCodes+Home). This software package has an Application Program Interface which makes ECMWF GRIB1 and GRIB2 files accessible from C, FORTRAN and Python programmes.

- **ecCodes** by ECMWF
  - <https://confluence.ecmwf.int/display/ECC/GRIB+tools>
  - Supports GRIB to NetCDF conversion (`grib_to_netcdf -o netcdf_file grib_file`)
  - Supports GRIB to JSON (`grib_dump -j <gribfile>`)
  - `grib_ls`: List content of GRIB files printing values of some keys. It does not fail when a key is not found.
  - `grib_dump`: Dump the content of a GRIB file in different formats.
  - [**Using the ecCodes GRIB Tools**](https://www.google.com/search?q=eccodes-grib-tools2-2016.pdf&oq=eccodes-grib-tools2-2016.pdf&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTIGCAEQRRg80gEHMzUxajBqN6gCALACAA&sourceid=chrome&ie=UTF-8#:~:text=Using%20the%20ecCodes%20GRIB%20Tools)

> Please be aware that when reading GRIB files where the range of valid data values includes '9999' that some software may incorrectly indicate that these data points are missing. This is because 9999 is the default missing value indicator. 

[Metview](https://confluence.ecmwf.int/display/METV/Metview) is a software tool from ECMWF which allows users to read, process and visualise GRIB 1 and GRIB 2 data (see Metview documentation).

[WGRIB](https://www.cpc.ncep.noaa.gov/products/wesley/wgrib.html) is a program to manipulate, inventory and decode GRIB files. The program is known to work on machines ranging from 486s to Cray supercomputers. (One fellow even ported it to a 286!) The program is Y2K friendly (NCO Y2K testing procedure).

{% fold info @cdo showname %}
```console
 $ cdo showname ERA5-SL-0p25-2024071400.grib
 var165 var166 var168 var167 var172 var151 var31 var34 var235 var33
 var141 var139 var170 var183 var236 var134 var39 var40 var41 var42
 var129
cdo    showname: Processed 21 variables [0.05s 26MB].

 $ cdo showname ERA5-PL-0p25-2024071400.grib
 var129 var157 var133 var130 var131 var132
cdo    showname: Processed 6 variables [0.47s 23MB].
```
{% endfold %}

{% fold info @grib_ls *.grib %}
```console
 $ grib_ls ERA5-SL-0p25-2024071400.grib
ERA5-SL-0p25-2024071400.grib
edition      centre       typeOfLevel  level        dataDate     stepRange    dataType     shortName    packingType  gridType     
1            ecmf         surface      0            20240714     0            an           10u          grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           10v          grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           2d           grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           2t           grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           lsm          grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           msl          grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           ci           grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           sst          grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           skt          grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           rsn          grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           sd           grid_simple  regular_ll  
1            ecmf         depthBelowLandLayer  0            20240714     0            an           stl1         grid_simple  regular_ll  
1            ecmf         depthBelowLandLayer  7            20240714     0            an           stl2         grid_simple  regular_ll  
1            ecmf         depthBelowLandLayer  28           20240714     0            an           stl3         grid_simple  regular_ll  
1            ecmf         depthBelowLandLayer  100          20240714     0            an           stl4         grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           sp           grid_simple  regular_ll  
1            ecmf         depthBelowLandLayer  0            20240714     0            an           swvl1        grid_simple  regular_ll  
1            ecmf         depthBelowLandLayer  7            20240714     0            an           swvl2        grid_simple  regular_ll  
1            ecmf         depthBelowLandLayer  28           20240714     0            an           swvl3        grid_simple  regular_ll  
1            ecmf         depthBelowLandLayer  100          20240714     0            an           swvl4        grid_simple  regular_ll  
1            ecmf         surface      0            20240714     0            an           z            grid_simple  regular_ll  
21 of 21 messages in ERA5-SL-0p25-2024071400.grib

21 of 21 total messages in 1 files

 $ grib_ls ERA5-PL-0p25-2024071400.grib
ERA5-PL-0p25-2024071400.grib
edition      centre       typeOfLevel  level        dataDate     stepRange    dataType     shortName    packingType  gridType     
1            ecmf         isobaricInhPa  1            20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  1            20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  1            20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  1            20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  1            20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  1            20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  2            20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  2            20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  2            20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  2            20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  2            20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  2            20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  3            20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  3            20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  3            20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  3            20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  3            20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  3            20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  5            20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  5            20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  5            20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  5            20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  5            20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  5            20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  7            20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  7            20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  7            20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  7            20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  7            20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  7            20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  10           20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  10           20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  10           20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  10           20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  10           20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  10           20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  20           20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  20           20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  20           20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  20           20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  20           20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  20           20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  30           20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  30           20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  30           20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  30           20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  30           20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  30           20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  50           20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  50           20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  50           20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  50           20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  50           20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  50           20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  70           20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  70           20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  70           20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  70           20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  70           20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  70           20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  100          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  100          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  100          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  100          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  100          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  100          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  125          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  125          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  125          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  125          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  125          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  125          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  150          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  150          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  150          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  150          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  150          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  150          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  175          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  175          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  175          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  175          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  175          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  175          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  200          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  200          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  200          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  200          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  200          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  200          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  225          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  225          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  225          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  225          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  225          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  225          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  250          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  250          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  250          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  250          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  250          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  250          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  300          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  300          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  300          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  300          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  300          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  300          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  350          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  350          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  350          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  350          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  350          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  350          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  400          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  400          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  400          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  400          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  400          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  400          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  450          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  450          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  450          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  450          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  450          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  450          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  500          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  500          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  500          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  500          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  500          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  500          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  550          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  550          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  550          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  550          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  550          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  550          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  600          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  600          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  600          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  600          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  600          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  600          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  650          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  650          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  650          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  650          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  650          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  650          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  700          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  700          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  700          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  700          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  700          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  700          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  750          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  750          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  750          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  750          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  750          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  750          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  775          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  775          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  775          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  775          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  775          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  775          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  800          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  800          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  800          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  800          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  800          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  800          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  825          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  825          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  825          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  825          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  825          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  825          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  850          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  850          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  850          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  850          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  850          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  850          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  875          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  875          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  875          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  875          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  875          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  875          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  900          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  900          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  900          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  900          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  900          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  900          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  925          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  925          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  925          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  925          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  925          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  925          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  950          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  950          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  950          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  950          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  950          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  950          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  975          20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  975          20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  975          20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  975          20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  975          20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  975          20240714     0            an           v            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  1000         20240714     0            an           z            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  1000         20240714     0            an           r            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  1000         20240714     0            an           q            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  1000         20240714     0            an           t            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  1000         20240714     0            an           u            grid_simple  regular_ll  
1            ecmf         isobaricInhPa  1000         20240714     0            an           v            grid_simple  regular_ll  
222 of 222 messages in ERA5-PL-0p25-2024071400.grib

222 of 222 total messages in 1 files
```
{% endfold %}

- Vtable.Vtable.ERA-interim.pl has GRIB1 format.

{% fold info @Vtable.ERA-interim.pl %}
```console
GRIB | Level| Level| Level| metgrid  |  metgrid | metgrid                                  |
Code | Code |   1  |   2  | Name     |  Units   | Description                              |
-----+------+------+------+----------+----------+------------------------------------------+
 129 | 100  |   *  |      | GEOPT    | m2 s-2   |                                          | 
     | 100  |   *  |      | HGT      | m        | Height                                   |
 130 | 100  |   *  |      | TT       | K        | Temperature                              |
 131 | 100  |   *  |      | UU       | m s-1    | U                                        |
 132 | 100  |   *  |      | VV       | m s-1    | V                                        |
 157 | 100  |   *  |      | RH       | %        | Relative Humidity                        |
 165 |  1   |   0  |      | UU       | m s-1    | U                                        | At 10 m
 166 |  1   |   0  |      | VV       | m s-1    | V                                        | At 10 m
 167 |  1   |   0  |      | TT       | K        | Temperature                              | At 2 m
 168 |  1   |   0  |      | DEWPT    | K        |                                          | At 2 m
     |  1   |   0  |      | RH       | %        | Relative Humidity                        | At 2 m
 172 |  1   |   0  |      | LANDSEA  | 0/1 Flag | Land/Sea flag                            |
 129 |  1   |   0  |      | SOILGEO  | m2 s-2   |                                          |
     |  1   |   0  |      | SOILHGT  | m        | Terrain field of source analysis         |
 134 |  1   |   0  |      | PSFC     | Pa       | Surface Pressure                         |
 151 |  1   |   0  |      | PMSL     | Pa       | Sea-level Pressure                       |
 235 |  1   |   0  |      | SKINTEMP | K        | Sea-Surface Temperature                  |
  31 |  1   |   0  |      | SEAICE   | fraction | Sea-Ice Fraction                         |
  34 |  1   |   0  |      | SST      | K        | Sea-Surface Temperature                  |
  33 |  1   |   0  |      | SNOW_DEN | kg m-3   |                                          |
 141 |  1   |   0  |      | SNOW_EC  | m        |                                          |
     |  1   |   0  |      | SNOW     | kg m-2   |Water Equivalent of Accumulated Snow Depth|
     |  1   |   0  |      | SNOWH    | m        | Physical Snow Depth                      |
 139 | 112  |   0  |   7  | ST000007 | K        | T of 0-7 cm ground layer                 |
 170 | 112  |   7  |  28  | ST007028 | K        | T of 7-28 cm ground layer                |
 183 | 112  |  28  | 100  | ST028100 | K        | T of 28-100 cm ground layer              |
 236 | 112  | 100  | 255  | ST100289 | K        | T of 100-289 cm ground layer             |
  39 | 112  |   0  |   7  | SM000007 | m3 m-3   | Soil moisture of 0-7 cm ground layer     |
  40 | 112  |   7  |  28  | SM007028 | m3 m-3   | Soil moisture of 7-28 cm ground layer    |
  41 | 112  |  28  | 100  | SM028100 | m3 m-3   | Soil moisture of 28-100 cm ground layer  |
  42 | 112  | 100  | 255  | SM100289 | m3 m-3   | Soil moisture of 100-289 cm ground layer |
-----+------+------+------+----------+----------+------------------------------------------+
#
#  For use with ERA-interim pressure-level output.
#
#  Grib codes are from Table 128
#  http://www.ecmwf.int/services/archive/d/parameters/order=grib_parameter/table=128/
#  
# snow depth is converted to the proper units in rrpr.F
#
#  For ERA-interim data at NCAR, use the pl (sc and uv) and sfc sc files. 
```
{% endfold %}

- Vtable.ECMWF has GRIB2 format.

{% fold info @Vtable.ECMWF %}
```console
GRIB1| Level| From |  To  | metgrid  | metgrid  | metgrid                                  |GRIB2|GRIB2|GRIB2|GRIB2|
Param| Type |Level1|Level2| Name     | Units    | Description                              |Discp|Catgy|Param|Level|
-----+------+------+------+----------+----------+------------------------------------------+-----------------------+
 129 | 100  |   *  |      | GEOPT    | m2 s-2   |                                          |  0  |  0  |     | 100 |
 156 | 100  |   *  |      | HGT      | m        | Height                                   |  0  |  3  |  5  | 100 |  
 130 | 100  |   *  |      | TT       | K        | Temperature                              |  0  |  0  |  0  | 100 |
 131 | 100  |   *  |      | UU       | m s-1    | U                                        |  0  |  2  |  2  | 100 |
 132 | 100  |   *  |      | VV       | m s-1    | V                                        |  0  |  2  |  3  | 100 |
 157 | 100  |   *  |      | RH       | %        | Relative Humidity                        |  0  |  1  |  1  | 100 |
 165 |  1   |   0  |      | UU       | m s-1    | U                    At 10 m             |  0  |  2  |  2  | 103 | 
 166 |  1   |   0  |      | VV       | m s-1    | V                    At 10 m             |  0  |  2  |  3  | 103 | 
 167 |  1   |   0  |      | TT       | K        | Temperature          At  2 m             |  0  |  0  |  0  | 103 |
 168 |  1   |   0  |      | DEWPT    | K        |                                          |  0  |  0  |  6  | 103 |
     |  1   |   0  |      | RH       | %        | Relative Humidity    At  2 m             |  0  |  0  |     | 103 | 
 172 |  1   |   0  |      | LANDSEA  | 0/1 Flag | Land/Sea flag                            |  2  |  0  |  0  |  1  | 
 129 |  1   |   0  |      | SOILGEO  | m2 s-2   |                                          |  0  |  0  |     | 103 | 
 156 |  1   |   0  |      | SOILHGT  | m        | Terrain field of source analysis         |  0  |  0  |     | 106 | 
 134 |  1   |   0  |      | PSFC     | Pa       | Surface Pressure                         |  0  |  3  |  0  |  1  |
 151 |  1   |   0  |      | PMSL     | Pa       | Sea-level Pressure                       |  0  |  3  |  0  | 101 |
 235 |  1   |   0  |      | SKINTEMP | K        | Sea-Surface Temperature                  |  0  |  3  |     | 101 |
  31 |  1   |   0  |      | SEAICE   | 0/1 Flag | Sea-Ice-Flag                             |  0  |  3  |     | 101 |
  34 |  1   |   0  |      | SST      | K        | Sea-Surface Temperature                  |  0  |  3  |     | 101 |
 141 |  1   |   0  |      | SNOW_EC  | m        |                                          |  0  |  3  |     | 101 |
     |  1   |   0  |      | SNOW     | kg m-2   |Water Equivalent of Accumulated Snow Depth|  0  |  3  |     | 101 |
 139 | 112  |   0  |   7  | ST000007 | K        | T of 0-7 cm ground layer                 |  2  |  0  |  2  | 106 | 
 170 | 112  |   7  |  28  | ST007028 | K        | T of 7-28 cm ground layer                | 192 | 128 | 170 | 106 | 
 183 | 112  |  28  | 100  | ST028100 | K        | T of 28-100 cm ground layer              | 192 | 128 | 183 | 106 | 
 236 | 112  | 100  | 255  | ST100289 | K        | T of 100-289 cm ground layer             | 192 | 128 | 236 | 106 | 
  39 | 112  |   0  |   7  | SM000007 | fraction | Soil moisture of 0-7 cm ground layer     | 192 | 128 | 39  | 106 | 
  40 | 112  |   7  |  28  | SM007028 | fraction | Soil moisture of 7-28 cm ground layer    | 192 | 128 | 40  | 106 | 
  41 | 112  |  28  | 100  | SM028100 | fraction | Soil moisture of 28-100 cm ground layer  | 192 | 128 | 41  | 106 | 
  42 | 112  | 100  | 255  | SM100289 | fraction | Soil moisture of 100-289 cm ground layer | 192 | 128 | 42  | 106 | 
-----+------+------+------+----------+----------+------------------------------------------+-----+-----+-----+-----+
#
#  Grib codes are from Table 128 
#  http://old.ecmwf.int/publications/manuals/d/gribapi/param/filter=grib1/order=paramId/order_type=asc/p=1/table=128/
#  
#  snow depth is converted to the proper units in rrpr.F
#
#  Tested on NCAR/RDA ds113.0 dataset. http://rda.ucar.edu/datasets/ds113.0/
#  Note that for ds113.0 there is one surface data file per day and 4 pressure-level files per day.
```
{% endfold %}

{% fold info @ECMWF user-defined parameter table (#128) %}
```console
0:var0:undefined
1:STRF:Stream function [m**2 s**-1]
2:VPOT:Velocity potential [m**2 s**-1]
3:PT:Potential temperature [K]
4:EQPT:Equivalent potential temperature [K]
5:SEPT:Saturated equivalent potential temperature [K]
6:var6:Reserved for Metview
7:var7:Reserved for Metview
8:var8:Reserved for Metview
9:var9:Reserved for Metview
10:var10:Reserved for Metview
11:UDVW:U component of divergent wind [m s**-1]
12:VDVW:V component of divergent wind [m s**-1]
13:URTW:U component of rotational wind [m s**-1]
14:VRTW:V component of rotational wind [m s**-1]
15:var15:Reserved for Metview
16:var16:Reserved for Metview
17:var17:Reserved for Metview
18:var18:Reserved for Metview
19:var19:Reserved for Metview
20:var20:Reserved for Metview
21:UCTP:Unbalanced component of temperature [K]
22:UCLN:Unbalanced component of logarithm of surface pressure
23:UCDV:Unbalanced component of divergence [s**-1]
24:var24:Reserved for future unbalanced components
25:var25:Reserved for future unbalanced components
26:CL:Lake cover [(0-1)]
27:CVL:Low vegetation cover [(0-1)]
28:CVH:High vegetation cover [(0-1)]
29:TVL:Type of low vegetation
30:TVH:Type of high vegetation
31:CI:Sea-ice cover [(0-1)]
32:ASN:Snow albedo [(0-1)]
33:RSN:Snow density [kg m**-3]
34:SSTK:Sea surface temperature [K]
35:ISTL1:Ice surface temperature layer 1 [K]
36:ISTL2:Ice surface temperature layer 2 [K]
37:ISTL3:Ice surface temperature layer 3 [K]
38:ISTL4:Ice surface temperature layer 4 [K]
39:SWVL1:Volumetric soil water layer 1 [m**3 m**-3]
40:SWVL2:Volumetric soil water layer 2 [m**3 m**-3]
41:SWVL3:Volumetric soil water layer 3 [m**3 m**-3]
42:SWVL4:Volumetric soil water layer 4 [m**3 m**-3]
43:SLT:Soil type
44:ES:Snow evaporation [m of water]
45:SMLT:Snowmelt [m of water]
46:SDUR:Solar duration [s]
47:DSRP:Direct solar radiation [w m**-2]
48:MAGSS:Magnitude of surface stress [N m**-2 s]
49:10FG:Wind gust at 10 metres [m s**-1]
50:LSPF:Large-scale precipitation fraction [s]
51:MX2T24:Maximum 2 metre temperature [K]
52:MN2T24:Minimum 2 metre temperature [K]
53:MONT:Montgomery potential [m**2 s**-2]
54:PRES:Pressure [Pa]
55:var55:undefined
56:var56:undefined
57:var57:undefined
58:var58:undefined
59:var59:undefined
60:PV:Potential vorticity [K m**2 kg**-1 s**-1]
78:TCLW:Total column liquid water [kg m**-2]
79:TCIW:Total column ice water [kg m**-2]
100:100:Experimental product [Undefined]
101:101:Experimental product [Undefined]
102:102:Experimental product [Undefined]
103:103:Experimental product [Undefined]
104:104:Experimental product [Undefined]
105:105:Experimental product [Undefined]
106:106:Experimental product [Undefined]
107:107:Experimental product [Undefined]
108:108:Experimental product [Undefined]
109:109:Experimental product [Undefined]
110:110:Experimental product [Undefined]
111:111:Experimental product [Undefined]
112:112:Experimental product [Undefined]
113:113:Experimental product [Undefined]
114:114:Experimental product [Undefined]
115:115:Experimental product [Undefined]
116:116:Experimental product [Undefined]
117:117:Experimental product [Undefined]
118:118:Experimental product [Undefined]
119:119:Experimental product [Undefined]
120:120:Experimental product [Undefined]
121:var121:undefined
122:var122:undefined
123:var123:undefined
124:var124:undefined
125:var125:undefined
126:var126:undefined
127:AT:Atmospheric tide
128:BV:Budget values
129:Z:Geopotential [m**2 s**-2]
130:T:Temperature [K]
131:U:U velocity [m s**-1]
132:V:V velocity [m s**-1]
133:Q:Specific humidity [kg kg**-1]
134:SP:Surface pressure [Pa]
135:W:Vertical velocity [Pa s**-1]
136:TCW:Total column water [kg m**-2]
137:TCWV:Total column water vapour [kg m**-2]
138:VO:Vorticity (relative) [s**-1]
139:STL1:Soil temperature level 1 [K]
140:SWL1:Soil wetness level 1 [m of water]
141:SD:Snow depth [m of water equivalent]
142:LSP:Stratiform precipitation [m]
143:CP:Convective precipitation [m]
144:SF:Snowfall (convective + stratiform) [m of water equivalent]
145:BLD:Boundary layer dissipation [W m**-2 s]
146:SSHF:Surface sensible heat flux [W m**-2 s]
147:SLHF:Surface latent heat flux [W m**-2 s]
148:CHNK:Charnock
149:SNR:Surface net radiation [W m**-2 s]
150:TNR:Top net radiation
151:MSL:Mean sea-level pressure [Pa]
152:LNSP:Logarithm of surface pressure
153:SWHR:Short-wave heating rate [K]
154:LWHR:Long-wave heating rate [K]
155:D:Divergence [s**-1]
156:GH:Height [m]
157:R:Relative humidity [%]
158:TSP:Tendency of surface pressure [Pa s**-1]
159:BLH:Boundary layer height [m]
160:SDOR:Standard deviation of orography
161:ISOR:Anisotropy of sub-gridscale orography
162:ANOR:Angle of sub-gridscale orography [rad]
163:SLOR:Slope of sub-gridscale orography
164:TCC:Total cloud cover [(0 - 1)]
165:10U:10 metre U wind component [m s**-1]
166:10V:10 metre V wind component [m s**-1]
167:2T:2 metre temperature [K]
168:2D:2 metre dewpoint temperature [K]
169:SSRD:Surface solar radiation downwards [W m**-2 s]
170:STL2:Soil temperature level 2 [K]
171:SWL2:Soil wetness level 2 [m of water]
172:LSM:Land/sea mask [(0, 1)]
173:SR:Surface roughness [m]
174:AL:Albedo [(0 - 1)]
175:STRD:Surface thermal radiation downwards [W m**-2 s]
176:SSR:Surface solar radiation [W m**-2 s]
177:STR:Surface thermal radiation [W m**-2 s]
178:TSR:Top solar radiation [W m**-2 s]
179:TTR:Top thermal radiation [W m**-2 s]
180:EWSS:East/West surface stress [N m**-2 s]
181:NSSS:North/South surface stress [N m**-2 s]
182:E:Evaporation [m of water]
183:STL3:Soil temperature level 3 [K]
184:SWL3:Soil wetness level 3 [m of water]
185:CCC:Convective cloud cover [(0 - 1)]
186:LCC:Low cloud cover [(0 - 1)]
187:MCC:Medium cloud cover [(0 - 1)]
188:HCC:High cloud cover [(0 - 1)]
189:SUND:Sunshine duration [s]
190:EWOV:EW component of subgrid orographic variance [m**2]
191:NSOV:NS component of subgrid orographic variance [m**2]
192:NWOV:NWSE component of subgrid orographic variance [m**2]
193:NEOV:NESW component of subgrid orographic variance [m**2]
194:BTMP:Brightness temperature [K]
195:LGWS:Lat. component of gravity wave stress [N m**-2 s]
196:MGWS:Meridional component of gravity wave stress [N m**-2 s]
197:GWD:Gravity wave dissipation [W m**-2 s]
198:SRC:Skin reservoir content [m of water]
199:VEG:Vegetation fraction [(0 - 1)]
200:VSO:Variance of sub-gridscale orography [m**2]
201:MX2T:Maximum 2 metre temperature since previous post-processing [K]
202:MN2T:Minimum 2 metre temperature since previous post-processing [K]
203:O3:Ozone mass mixing ratio [kg kg**-1]
204:PAW:Precipiation analysis weights
205:RO:Runoff [m]
206:TCO3:Total column ozone [Dobson]
207:10SI:10 meter windspeed [m s**-1]
208:TSRC:Top net solar radiation, clear sky [W m**-2]
209:TTRC:Top net thermal radiation, clear sky [W m**-2]
210:SSRC:Surface net solar radiation, clear sky [W m**-2]
211:STRC:Surface net thermal radiation, clear sky [W m**-2]
212:SI:Solar insolation [W m**-2]
213:var213:undefined
214:DHR:Diabatic heating by radiation [K]
215:DHVD:Diabatic heating by vertical diffusion [K]
216:DHCC:Diabatic heating by cumulus convection [K]
217:DHLC:Diabatic heating large-scale condensation [K]
218:VDZW:Vertical diffusion of zonal wind [m s**-1]
219:VDMW:Vertical diffusion of meridional wind [m s**-1]
220:EWGD:EW gravity wave drag tendency [m s**-1]
221:NSGD:NS gravity wave drag tendency [m s**-1]
222:CTZW:Convective tendency of zonal wind [m s**-1]
223:CTMW:Convective tendency of meridional wind [m s**-1]
224:VDH:Vertical diffusion of humidity [kg kg**-1]
225:HTCC:Humidity tendency by cumulus convection [kg kg**-1]
226:HTLC:Humidity tendency large-scale condensation [kg kg**-1]
227:CRNH:Change from removing negative humidity [kg kg**-1]
228:TP:Total precipitation [m]
229:IEWS:Instantaneous X surface stress [N m**-2]
230:INSS:Instantaneous Y surface stress [N m**-2]
231:ISHF:Instantaneous surface heat flux [W m**-2]
232:IE:Instantaneous moisture flux [kg m**-2 s]
233:ASQ:Apparent surface humidity [kg kg**-1]
234:LSRH:Logarithm of surface roughness length for heat
235:SKT:Skin temperature [K]
236:STL4:Soil temperature level 4 [K]
237:SWL4:Soil wetness level 4 [m]
238:TSN:Temperature of snow layer [K]
239:CSF:Convective snowfall [m of water equivalent]
240:LSF:Large-scale snowfall [m of water equivalent]
241:ACF:Accumulated cloud fraction tendency [(-1 to 1)]
242:ALW:Accumulated liquid water tendency [(-1 to 1)]
243:FAL:Forecast albedo [(0 - 1)]
244:FSR:Forecast surface roughness [m]
245:FLSR:Forecast log of surface roughness for heat
246:CLWC:Cloud liquid water content [kg kg**-1]
247:CIWC:Cloud ice water content [kg kg**-1]
248:CC:Cloud cover [(0 - 1)]
249:AIW:Accumulated ice water tendency [(-1 to 1)]
250:ICE:Ice age [1,0]
251:ATTE:Adiabatic tendency of temperature [K]
252:ATHE:Adiabatic tendency of humidity [kg kg**-1]
253:ATZE:Adiabatic tendency of zonal wind [m s**-1]
254:ATMW:Adiabatic tendency of meridional wind [m s**-1]
255:var255:undefined
```
{% endfold %}

# References

1. [Climate Data Operators (CDO) Tutorial (**recommend**)](https://code.mpimet.mpg.de/projects/cdo/wiki/tutorial#Basic-Usage)
2. [Reading GRIB Files | NCEP](https://www.cpc.ncep.noaa.gov/products/wesley/reading_grib.html)
3. [wgrib2: wgrib for GRIB-2 files](https://www.cpc.ncep.noaa.gov/products/wesley/wgrib2)
4. [What are GRIB files and how can I read them](https://confluence.ecmwf.int/display/CKB/What+are+GRIB+files+and+how+can+I+read+them)
5. [Please see the WMO "Introduction to GRIB Edition 1 and GRIB Edition 2" documentation for further details.](https://old.wmo.int/extranet/pages/prog/www/WMOCodes/Guides/GRIB/Introduction_GRIB1-GRIB2.pdf)
6. [GRIB格式介绍 (**推薦**)](https://perillaroc.github.io/eccodes-tutorial-cn/01-introduction/)
   1. GRIB 格式是面向二进制的数据交换格式，无法直接阅读，需要使用软件进行解码和编码。
   2. 使用16进制编辑器打开 GRIB 文件的示例，除了最开始4个字节外，其余部分都无法解析成文本。 后面将会看到前4个字节对应的 ASCII 编码内容 GRIB 正是 GRIB 文件的标志。