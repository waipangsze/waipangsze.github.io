---
layout: post
title: GEFS | MPAS | WRF
categories: [NWP]
tags: [WRF,WPS,MPAS,IC/BC,GFS,FNL,RDA,NCEP,GEFS]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-09-17 06:00:00
index_img: https://i.imgur.com/szq6aDX.png
banner_img: https://i.imgur.com/szq6aDX.png
---

# Global Ensemble Forecast System (GEFS)

The Global Ensemble Forecast System (GEFS) is a weather model created by the National Centers for Environmental Prediction (NCEP) that generates 

- **30 + 1 separate forecasts (ensemble members)** 
- **30 perturbed and 1 unperturbed/control**

to address underlying uncertainties in the input data such limited coverage, instruments or observing systems biases, and the limitations of the model itself. GEFS quantifies these uncertainties by generating multiple forecasts, which in turn produce a range of potential outcomes based on differences or perturbations applied to the data after it has been incorporated into the model. Each forecast compensates for a different set of uncertainties.

- <https://www.ncei.noaa.gov/products/weather-climate-models/global-ensemble-forecast>

## Training on the use of ensemble forecasts 

- <https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/gefs/faq.php>

## Introduction / History

- <https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/gefs.php>

![](https://i.imgur.com/AwzO98l.png){width=400}

## GEFS in the NOAA Big Data Program

The [NOAA Open Data Dissemination Program](https://www.noaa.gov/information-technology/open-data-dissemination) provides access to data 

- for multiple resolutions 
- from **1/1/2017–present** 
- in the [AWS Open Data Registry for GEFS](https://registry.opendata.aws/noaa-gefs/)

# Products Inventory

The "`pgrb2ap5`," "`pgrb2bp5`," and "`pgrb2sp25`" directories in the Global Ensemble Forecast System (GEFS) refer to different sets of gridded binary (GRIB2) forecast product files at different spatial resolutions and with different contents:

- `pgrb2ap5` and `pgrb2bp5` contain GEFS forecast products at a **0.5-degree spatial resolution**. These two subdirectories combined hold the same content as the older 1-degree products but at higher resolution and with updates in the division of variables between the two directories. The "`pgrb2ap5`" files include about **43+ variables** such as height, temperature, relative humidity at various pressure levels, winds, surface pressure, precipitation types, and others. The "`pgrb2bp5`" directory **contains most of the other variables**. The output frequency is typically every three hours for the first eight days of the forecast. These 0.5-degree products replaced the older 2.5-degree GRIB2 datasets.
- `pgrb2sp25` contains selected primary forecast fields at a higher **0.25-degree spatial resolution** (quarter degree). This directory has **fewer (~35+) most common variables but at higher spatial detail**. Starting from GEFS version 12.1.2 (2021), new variables such as Mean Sea Level Pressure with Eta reduction (MSLET) were added to the "`pgrb2sp25`" files.
- **Jun 21 2021**/**GEFSv12.1.2 upgrade**: <https://preview.weather.gov/media/notification/pdf2/scn21-61gefs_upgrade_v12.1.2.pdf>

{% gi 2 2 %}
![](https://i.imgur.com/hz70Jlk.png)
![](https://i.imgur.com/s89dzZb.png)
{% endgi %}

- [herbie: GEFS](https://herbie.readthedocs.io/en/2024.3.0/user_guide/_model_notebooks/gefs.html): The GEFS model product could be any of the following: - 'atmos.5' - Half degree atmos PRIMARY fields (`pgrb2ap5`); **~83 most common variables**. - 'atmos.5b' - Half degree atmos SECONDARY fields (`pgrb2bp5`); **~500 least common variables** - 'atmos.25' - Quarter degree atmos PRIMARY fields (`pgrb2sp25`); **~35 most common variables** - 'wave' - Global wave products. - 'chem.5' - Chemistry fields on 0.5 degree grid - 'chem.25' - Chemistry fields on 0.25 degree grid.

## Label

- `NN` is the number (name) of ensemble members (c00, p01-p30)
- `CC` is the current cycle (00, 06, 12, 18)
- `xxx` denotes the forecast hour (i.e. 00, 06, ..., 384)
- `PP` refers to 10, 50 or 90
- `YYYYMMDD` refers to the current Year Month Day

1. <https://www.nco.ncep.noaa.gov/pmb/products/gens/#GEFS>

## GEFS - Atmos - pgrb2a/pgrb2bp5 (0.5 degree resolution)

![GEFS - Atmos - pgrb2a: Most commonly used parameters](https://i.imgur.com/sxo0Teu.png){width=500}

![GEFS - Atmos - pgrb2bp5 (Secondary Parms)](https://i.imgur.com/RCyeRQU.png){width=500}

## GEFS - Atmos - pgrb2sp25 (Select Parms) (0.25 degree resolution)

![](https://i.imgur.com/T5qEutO.png){width=500}

# ungrib

## GEFS sep2020 landmask fix (May 5, 2021)

- <https://github.com/wrf-model/WPS/pull/158/files>

Update `rrpr.F` to **correctly process the land surface fields in the September 2020 GEFS files**. The GEFS changes were similar to the GFS changes in July 2017 which includes a new missing value for soil fields. The landmask in the grib file does not match the soil fields, so a landmask is created from the soil temperature field. Without this fix the soil temperature and moisture can be zero which causes wrf to blow up.
If NCEP eventually fixes this problem we may need to revisit this processing.

- **gep01.t00z.pgrb2a.0p50.f000**
- **gep01.t00z.pgrb2b.0p50.f000**
- ungrib.exe
- link_grib.csh
  - `$ link_grib.csh gep01.*`
- `Vtable.GFSENS`
- `Vtable -> Vtable.GFSENS`
- namelist.wps

```namelist.wps
&share
        wrf_core = 'ARW',
        max_dom  = 1,
        start_date = '2025-09-13_00:00:00',
        end_date   = '2025-09-13_00:00:00',
        interval_seconds = 10800,
        io_form_geogrid = 2,
/

&ungrib
        out_format = 'WPS',
        prefix = 'GEFS',
/
```

# References

1. [How to utilize GEFS ensemble mean data for input](https://forum.mmm.ucar.edu/threads/how-to-utilize-gefs-ensemble-mean-data-for-input.14717/)
2. [wrf.exe crash when using the new GEFS 0.5 data](https://forum.mmm.ucar.edu/threads/wrf-exe-crash-when-using-the-new-gefs-0-5-data.9590/#p18644)
3. [Resolved issues with GEFS ICs causing WRF to crash in first model time step](https://forum.mmm.ucar.edu/threads/resolved-issues-with-gefs-ics-causing-wrf-to-crash-in-first-model-time-step.12770/#post-41282)
4. [Running WRF using GEFS, error in ungrib](https://forum.mmm.ucar.edu/threads/running-wrf-using-gefs-error-in-ungrib.19713/)
5. [Use of WRF with GEFS data](https://forum.mmm.ucar.edu/threads/use-of-wrf-with-gefs-data.12785/)