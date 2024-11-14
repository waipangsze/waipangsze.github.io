---
layout: post
title: WRF | WRF uses Pnetcdf
categories: [WRF]
tags: [WRF, Installation, Pnetcdf]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/RwpxULg.png
banner_img: https://i.imgur.com/RwpxULg.png
---

# Issue

To Be Tested.

# Remark

1. [Parallel Netcdf4 I/O support (as opposed to pnetcdf)](https://forum.mmm.ucar.edu/threads/parallel-netcdf4-i-o-support-as-opposed-to-pnetcdf.9509/)
> Unfortunately the WRF model is not capable of using parallel I/O. If you are using netCDF-4, you must install it without activating parallel I/O based on HDF5. It is possible to use compression with netcdf-4 and HDF5 (without parallel I/O). See this page for details:
https://www2.mmm.ucar.edu/wrf/users/building_netcdf4.html

2. [WRF Build with Parallel NetCDF Support](http://climate-cms.wikis.unsw.edu.au/WRF_Build_with_Parallel_NetCDF_Support)
-  export PNETCDF=/projects/WRF/parallel-netcdf/xxx/ (BASH)
-  Configure namelist to use pNetCDF

```sh
In the namelist.input, the following settings support pNetCDF by setting value to 11:

io_form_boundary

io_form_history

io_form_auxinput2

io_form_auxhist2

Note that you need set nocolons = .true. in the section &time_control of namelist.input
```

- With pNetCDF support:

    - Timing for Writing wrfout_d03_2010-02-06_01_00_00 for domain 3: 77.62950 elapsed seconds. Timing for Writing wrfout_d03_2010-02-06_02_00_00 for domain 3: 75.74540 elapsed seconds. Timing for Writing wrfout_d03_2010-02-06_03_00_00 for domain 3: 80.26440 elapsed seconds.

- Without pNetCDF support:
    - Timing for Writing wrfout_d03_2010-02-06_01:00:00 for domain 3: 315.81271 elapsed seconds. Timing for Writing wrfout_d03_2010-02-06_02:00:00 for domain 3: 299.80011 elapsed seconds. Timing for Writing wrfout_d03_2010-02-06_03:00:00 for domain 3: 307.18109 elapsed seconds.
