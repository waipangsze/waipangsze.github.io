---
layout: post
title: NWP | BUFR data | GFS - Binary Universal Form for the Representation of meteorological data
categories: [NWP]
tags: [MPAS, WRF, NWP, GFS, FNL, IFS, ERA5, CMA, BUFR]
author: wpsze
date: 2026-02-27 06:30:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/UGRrdak.png
banner_img: https://i.imgur.com/UGRrdak.png
---

# Binary Universal Form for the Representation of meteorological data

The Global Forecast System (GFS) utilizes Binary Universal Form for the Representation of meteorological data (BUFR), a World Meteorological Organization (WMO) table-driven, binary code designed for efficient storage and exchange of observational data. BUFR handles massive datasets, such as satellite and station observations, in a compressed, machine-independent format. It is essential for representing diverse, high-volume meteorological data, offering flexibility compared to character-based codes. 

- **Definition**: 
  - BUFR is a WMO-standardized binary code (FM 94) used to efficiently store and exchange meteorological/oceanographic data.
- **Purpose** in GFS: 
  - GFS and Global Data Assimilation System (GDAS) use BUFR for ingestion of observational data, such as from satellites, RADAR, and upper-air soundings.
- **Structure**: 
  - A BUFR message is a continuous stream of binary numbers, containing both data and data description (self-defining). It consists of several sections: Indicator (0), Identification (1), Optional (2), Data Description (3), and Data (4).
- **Key Advantages**:
  - Efficiency: Highly compressed, which is crucial for large volumes of data.
  - Portability: Machine-independent binary format.
  - Flexibility: Table-driven, making it easy to accommodate new observation types without changing the format structure.
  - Usage: It is not human-readable; it requires specific decoding software to convert the compressed binary into usable data.
- **Data Types Supported**:
  - BUFR is designed to represent virtually any meteorological data set, including: 
    - Synoptical station data
    - Radiosondes (upper air)
    - Satellite observations
    - Marine/Oceanographic data

**BUFR replaced older, character-based codes like SYNOP and TEMP.**

# Download 

- <https://www.nco.ncep.noaa.gov/pmb/products/gfs/>
  - [NCEP BUFR FILE STRUCTURE](https://www.emc.ncep.noaa.gov/mmb/data_processing/NCEP_BUFR_File_Structure.htm)
  - <https://nomads.ncep.noaa.gov/pub/data/nccf/com/obsproc/prod/>

| Data                                                                    | WCOSS Filename (gfs.tCCz...) | Inventory |
|-------------------------------------------------------------------------|------------------------------|-----------|
| Upper-Air                                                               | adpupa.tm00.bufr_d           |           |
| Wind Profiler                                                           | proflr.tm00.bufr_d           |           |
| Satellite-derived wind                                                  | satwnd.tm00.bufr_d           |           |
| VAD (NEXRAD) wind                                                       | vadwnd.tm00.bufr_d           |           |
| GOES Satellite data                                                     | goesnd.tm00.bufr_d           |           |
| WindSat scatterometer data from FNMOC                                   | wndsat.tm00.bufr_d           |           |
| SBUV layer ozone product (Version 8)                                    | osbuv8.tm00.bufr_d           |           |
| METOP-2 IASI 1C radiance data (variable channels)                       | mtiasi.tm00.bufr_d           |           |
| GOES 11x17 fov imager clear radiances                                   | geoimr.tm00.bufr_d           |           |
| HIRS-3 NCEP-processed brightness temps                                  | 1bhrs3.tm00.bufr_d           |           |
| HIRS-4 1b radiances                                                     | 1bhrs4.tm00.bufr_d           |           |
| Advanced Tech Microwave Sounder                                         | atms.tm00.bufr_d             |           |
| Microwave Limb Sounder Ozone                                            | mls.tm00.bufr_d              |           |
| Microwave Humidity Sounder-processed bright temps                       | 1bmhs.tm00.bufr_d            |           |
| Microwave Humidity Sounder-proc bright T from EARS, SA-RARS and AP-RARS | esmhs.tm00.bufr_d            |           |
| AMSU-A NCEP-processed brightness temps                                  | 1bamua.tm00.bufr_d           |           |
| AMSU-B NCEP-processed brightness temps                                  | 1bamub.tm00.bufr_d           |           |
| AMSU-B-proc bright T from EARS, SA-RARS and AP-RARS                     | esamub.tm00.bufr_d           |           |
| Hi-res3-proc bright T from EARS, SA-RARS and AP-RARS                    | eshrs3.tm00.bufr_d           |           |
| AQUA/AMSR-E processed brightness temperatures                           | amsre.tm00.bufr_d            |           |
| AQUA-AIRS AIRS/AMSU-A/HSB proc bright T                                 | airsev.tm00.bufr_d           |           |
| Global Ozone Monitoring Experiment-2                                    | gome.tm00.bufr_d             |           |
| Ozone Monitoring Instrument                                             | omi.tm00.bufr_d              |           |
| GOES 1x1 fov sounder radiances                                          | goesfv.tm00.bufr_d           |           |
| GPS Integrated Precipitable Water                                       | gpsipw.tm00.bufr_d           |           |
| GPS radio occultation data                                              | gpsro.tm00.bufr_d            |           |
| Radio Acoustic Sounding System Temp Profiles                            | rassda.tm00.bufr_d           |           |
| SEVIRI Clear Sky Radiances                                              | sevcsr.tm00.bufr_d           |           |
| Mean Sea-level Pressure bogus reports                                   | sfcbog.tm00.bufr_d           |           |
| Surface marine                                                          | sfcshp.tm00.bufr_d           |           |

# Delivery time

- gdas.*

```console
https://nomads.ncep.noaa.gov/pub/data/nccf/com/obsproc/prod/gdas.20260428/

gdas.t00z.prepbufr.nr            29-Apr-2026 06:01   65M
gdas.t06z.prepbufr.nr            29-Apr-2026 11:59   62M
gdas.t12z.prepbufr.nr            29-Apr-2026 18:00   62M
gdas.t18z.prepbufr.nr            30-Apr-2026 00:39   56M 

gdas.t00z.1bamua.tm00.bufr_d     29-Apr-2026 05:53  5.0M
gdas.t06z.1bamua.tm00.bufr_d     29-Apr-2026 11:53  5.0M
gdas.t12z.1bamua.tm00.bufr_d     29-Apr-2026 17:53  5.0M
gdas.t18z.1bamua.tm00.bufr_d     29-Apr-2026 23:53  4.9M
```

{% note primary %}
The gdas.t12z.prepbufr.nr file (Global Data Assimilation System, 1200 UTC cycle, PrepBUFR, near-real-time) is part of the NCEP operational data flow, **which is typically delivered roughly 6 hours** after the 1200 UTC analysis time.
{% endnote %}

- gfs.*

```console
https://nomads.ncep.noaa.gov/pub/data/nccf/com/obsproc/prod/gfs.20260429/

gfs.t00z.prepbufr.nr             29-Apr-2026 02:58   61M
gfs.t06z.prepbufr.nr             29-Apr-2026 08:56   57M  
gfs.t12z.prepbufr.nr             29-Apr-2026 14:57   57M
gfs.t18z.prepbufr.nr             29-Apr-2026 20:58   51M

gfs.t00z.1bamua.tm00.bufr_d      29-Apr-2026 02:49  4.3M
gfs.t06z.1bamua.tm00.bufr_d      29-Apr-2026 08:49  3.8M
gfs.t12z.1bamua.tm00.bufr_d      29-Apr-2026 14:50  4.1M
gfs.t18z.1bamua.tm00.bufr_d      29-Apr-2026 20:50  3.9M
```

# Historical data

## NCEP ADP Global Upper Air and Surface Weather Observations (PREPBUFR format)

NCEP ADP Global Upper Air and Surface Weather Observations (PREPBUFR format) are composed of a global set of surface and upper air reports operationally collected by the National Centers for Environmental Prediction (NCEP). These include land surface, marine surface, radiosonde, pibal and aircraft reports from the Global Telecommunications System (GTS), profiler and US radar derived winds, SSM/I oceanic winds and TCW retrievals, and satellite wind data from the National Environmental Satellite Data and Information Service (NESDIS). The reports can include pressure, geopotential height, temperature, dew point temperature, wind direction and speed. Report time intervals range from hourly to 12 hourly.

- <https://gdex.ucar.edu/datasets/d337000/dataaccess/#>

```sh
#!/usr/bin/env csh
#
# c-shell script to download selected files from gdex.ucar.edu using Wget
# NOTE: if you want to run under a different shell, make sure you change
#       the 'set' commands according to your shell's syntax
# after you save the file, don't forget to make it executable
#   i.e. - "chmod 755 <name_of_script>"
#
# Experienced Wget Users: add additional command-line flags to 'opts' here
#   Use the -r (--recursive) option with care
#   Do NOT use the -b (--background) option - simultaneous file downloads
#       can cause your data access to be blocked
set opts = "-N"
#
# Check wget version.  Set the --no-check-certificate option 
# if wget version is 1.10 or higher
set v = `wget -V |grep 'GNU Wget ' | cut -d ' ' -f 3`
set a = `echo $v | cut -d '.' -f 1`
set b = `echo $v | cut -d '.' -f 2`
if(100 * $a + $b > 109) then
  set cert_opt = "--no-check-certificate"
else
  set cert_opt = ""
endif

set filelist= ( \
  https://osdf-director.osg-htc.org/ncar/gdex/d337000/tarfiles/2026/prepbufr.20260101.nr.tar.gz \
)
while($#filelist > 0)
  set syscmd = "wget $cert_opt $opts $filelist[1]"
  echo "$syscmd ..."
  $syscmd
  shift filelist
end
```

## NCEP GDAS Satellite Data 2004-continuing

This dataset contains a subset of the level 1b or higher satellite data products used in the NCEP Global Data Assimilation System. These files can be used with the Weather Research Forecast (WRF) Data Assimilation (DA) system. However, they can be useful for a variety of applications, particularly where conventional data is scarce.

- <https://gdex.ucar.edu/datasets/d735000/dataaccess/#>

```sh
#!/usr/bin/env csh
#
# c-shell script to download selected files from gdex.ucar.edu using Wget
# NOTE: if you want to run under a different shell, make sure you change
#       the 'set' commands according to your shell's syntax
# after you save the file, don't forget to make it executable
#   i.e. - "chmod 755 <name_of_script>"
#
# Experienced Wget Users: add additional command-line flags to 'opts' here
#   Use the -r (--recursive) option with care
#   Do NOT use the -b (--background) option - simultaneous file downloads
#       can cause your data access to be blocked
set opts = "-N"
#
# Check wget version.  Set the --no-check-certificate option 
# if wget version is 1.10 or higher
set v = `wget -V |grep 'GNU Wget ' | cut -d ' ' -f 3`
set a = `echo $v | cut -d '.' -f 1`
set b = `echo $v | cut -d '.' -f 2`
if(100 * $a + $b > 109) then
  set cert_opt = "--no-check-certificate"
else
  set cert_opt = ""
endif

set filelist= ( \
  https://osdf-director.osg-htc.org/ncar/gdex/d735000/1bamua/2026/1bamua.20260101.tar.gz \
)
while($#filelist > 0)
  set syscmd = "wget $cert_opt $opts $filelist[1]"
  echo "$syscmd ..."
  $syscmd
  shift filelist
end
```

# References

1. [https://www.ecmwf.int/sites/default/files/elibrary/1987/12522-binary-universal-form-representation-meteorological-data-introduction-fm-94-bufr.pdf](https://www.ecmwf.int/sites/default/files/elibrary/1987/12522-binary-universal-form-representation-meteorological-data-introduction-fm-94-bufr.pdf)