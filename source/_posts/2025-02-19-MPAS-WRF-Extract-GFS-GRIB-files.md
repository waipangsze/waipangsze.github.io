---
layout: post
title: MPAS | WRF | Extract subset of GFS GRIB files
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS]
author: wpsze
date: 2025-02-19 18:02:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/GO7mmRR.png
banner_img: https://i.imgur.com/GO7mmRR.png
---

# GFS subset download

## NOMADS Data at NCEP

- <https://nomads.ncep.noaa.gov/>
- <https://nomads.ncep.noaa.gov/gribfilter.php?ds=gfs_0p25_1hr>
- <https://nomads.ncep.noaa.gov/gribfilter.php?ds=gfs_0p25>
- Example:
  ```URL
  https://nomads.ncep.noaa.gov/cgi-bin/filter_gfs_0p25_1hr.pl?dir=%2Fgfs.20250228%2F00%2Fatmos&file=gfs.t00z.pgrb2.0p25.anl&var_TMP=on&var_U-GWD=on&var_V-GWD=on&all_lev=on
  ```
- Dowload script:
  {% fold info @download_gfs.sh %}
  ```sh
  #!/bin/bash
  #
  # define URL
  #
  workdir=/home/wpsze/mpas/GFS/
  hr=00
  #date_st=20240920
  date_st=$(date -d "today -1 days" +%Y%m%d)

  #wget 

  for iday in {0..0..1}; do
      
      idate=$(date -d "$date_st + $iday days" +%Y%m%d)
      echo $date_st $idate

      for fhr in {000..384..1}; do # or {076..76..1}

    # https://nomads.ncep.noaa.gov/gribfilter.php?ds=gfs_0p25

      URL="https://nomads.ncep.noaa.gov/cgi-bin/filter_gfs_0p25.pl?dir=%2Fgfs.${idate}%2F${hr}%2Fatmos&file=gfs.t${hr}z.pgrb2.0p25.f${fhr}&var_DSWRF=on&var_RH=on&var_TMP=on&var_UGRD=on&var_VGRD=on&lev_2_m_above_ground=on&lev_10_m_above_ground=on&lev_20_m_above_ground=on&lev_30_m_above_ground=on&lev_40_m_above_ground=on&lev_50_m_above_ground=on&lev_80_m_above_ground=on&lev_100_m_above_ground=on&lev_surface=on"

      echo ${URL}

      yyyymm=${idate:0:6}
      datapath=${workdir}/0p25/${yyyymm}/${idate}/
      mkdir -p ${datapath}

      # download file
      wget -c "$URL" -O ${datapath}/gfs.t${hr}z.pgrb2.0p25.f${fhr}.grb
      done
  done
  ```
  {% endfold %}

![NCEP GFS Forecasts (0.25 degree grid)](https://i.imgur.com/uC0QDnd.png){width=500}

## Size of grib file

- If full set is downlaoded, 
  - `519M` per grib file.

## rda.ucar.edu

- <https://rda.ucar.edu/datasets/d084001/dataaccess/#>
  - Sign up with orcid
  - Customizable Data Requests > Subsetting 
  - Waiting for completion
  - csh/python scripts are provided

{% gi 4 1-3 %}
![](https://i.imgur.com/GO7mmRR.png)
![](https://i.imgur.com/gyRwXFJ.png)
![](https://i.imgur.com/HDtKiPl.png)
{% endgi %}

{% fold info @rda-download.csh %}
```sh
#!/usr/bin/env csh
#
# c-shell script to download selected files from rda.ucar.edu using Wget
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
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2024123000.f384-25.2025011200.f195.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025011200.f198-25.2025011618.f135.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025011618.f138-25.2025012100.f360.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012100.f366-25.2025012512.f129.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012512.f132-25.2025012918.f330.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012918.f336-25.2025020306.f105.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025020306.f108-25.2025020712.f228.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025020712.f231-25.2025021200.f003.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025021200.f006-25.2025021606.f084.grib2.tar \
  https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025021606.f087-25.2025021712.f384.grib2.tar \
)
while($#filelist > 0)
  set syscmd = "wget $cert_opt $opts $filelist[1]"
  echo "$syscmd ..."
  $syscmd
  shift filelist
end
```
{% endfold %}

{% fold info @rda-download.py %}
```python
#!/usr/bin/env python
""" 
Python script to download selected files from rda.ucar.edu.
After you save the file, don't forget to make it executable
i.e. - "chmod 755 <name_of_script>"
"""
import sys, os
from urllib.request import build_opener

opener = build_opener()

filelist = [
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2024123000.f384-25.2025011200.f195.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025011200.f198-25.2025011618.f135.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025011618.f138-25.2025012100.f360.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012100.f366-25.2025012512.f129.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012512.f132-25.2025012918.f330.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025012918.f336-25.2025020306.f105.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025020306.f108-25.2025020712.f228.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025020712.f231-25.2025021200.f003.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025021200.f006-25.2025021606.f084.grib2.tar',
  'https://request.rda.ucar.edu/dsrqst/SZE782753/TarFiles/gfs.0p25.2025021606.f087-25.2025021712.f384.grib2.tar'
]

for file in filelist:
    ofile = os.path.basename(file)
    sys.stdout.write("downloading " + ofile + " ... ")
    sys.stdout.flush()
    infile = opener.open(file)
    outfile = open(ofile, "wb")
    outfile.write(infile.read())
    outfile.close()
    sys.stdout.write("done\n")
```
{% endfold %}

# Micromamba env setup

```yml
 $ cat list.yml 
name: PGW
channels:
- anaconda
- conda-forge
dependencies:
- conda-forge::cdo
- conda-forge::dask
- matplotlib
- python=3.11
- scipy
- conda-forge::wgrib
```

# grib checking

take GFS-wave model as example,

- `grib_ls gfs.t00z.global.0p16.f000.grib2`
  {% fold info @gfs.t00z.global.0p16.f000.grib2 %}
  ```console
  gfs.t00z.global.0p16.f000.grib2
  edition      centre       date         dataType     gridType     stepRange    typeOfLevel  level        shortName    packingType  
  2            kwbc         20250227     fc           regular_ll   0            surface      1            ws           grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            wdir         grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            u            grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            v            grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            swh          grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            perpw        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            dirpw        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            shww         grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  1            swell        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  2            swell        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  3            swell        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            mpww         grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  1            swper        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  2            swper        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  3            swper        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            wvdir        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  1            swdir        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  2            swdir        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  3            swdir        grid_jpeg   
  19 of 19 messages in gfs.t00z.global.0p16.f000.grib2

  19 of 19 total messages in 1 files
  ```
  {% endfold %}
- re-download dataset that only contains surface variables.
- `grib_to_netcdf gfs.t00z.global.0p16.f000.grib2 -o test.nc`
- `ncvis/ncview test.nc`

## From Fullset of GFS

### To check `paramId`

```
$ grib_ls -n parameter grib.file
```

```
centre      paramId     shortName   units       name        
kwbc        260074      prmsl       Pa          Pressure reduced to MSL 
kwbc        260018      clwmr       kg kg**-1   Cloud mixing ratio 
kwbc        260019      icmr        kg kg**-1   Ice water mixing ratio 
kwbc        260020      rwmr        kg kg**-1   Rain mixing ratio 
kwbc        260021      snmr        kg kg**-1   Snow mixing ratio 
```

### wind speed (heightAboveGround)

{% fold info @wind speed (heightAboveGround) %}
```
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  20           u            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  20           v            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  30           u            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  30           v            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  40           u            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  40           v            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  50           u            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  50           v            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  80           t            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  80           q            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  80           pres         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  80           u            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  80           v            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  100          t            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  100          100u         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            heightAboveGround  100          100v         grid_complex_spatial_differencing 
```
{% endfold %}

### surface

{% fold info @surface %}
```
2            kwbc         20241130     fc           regular_ll   3            surface      0            vis          grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            gust         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            hindex       grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            sp           grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            orog         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            t            grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            cnwat        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            sdwe         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            sde          grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            pevpr        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            icetk        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            cpofp        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            cprat        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            prate        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            cprat        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            prate        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            tp           grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            tp           grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            acpcp        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            acpcp        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            watr         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            csnow        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            cicep        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            cfrzr        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            crain        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            csnow        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            cicep        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            cfrzr        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            crain        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            lhtfl        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            shtfl        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            gflux        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            uflx         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            vflx         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            sr           grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            fricv        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            u-gwd        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            v-gwd        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            veg          grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            slt          grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            wilt         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            fldcp        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            SUNSD        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            lftx         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            cape         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            cin          grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            dswrf        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            dlwrf        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            uswrf        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            ulwrf        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            4lftx        grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            hpbl         grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            lsm          grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            ci           grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   0-3          surface      0            al           grid_complex_spatial_differencing 
2            kwbc         20241130     fc           regular_ll   3            surface      0            ist          grid_complex_spatial_differencing 
```
{% endfold %}

# Read GFS grib2 files

To process a CSV file containing station information (latitude and longitude) and extract weather data from GFS GRIB files, you can follow the steps below. This example assumes you have a CSV file named `stations.csv` with columns for station names, latitude, and longitude.

```console
 $ grib_ls ./subset-GFS/gfs.0p25.2025021200.f006.grib2
./subset-GFS/gfs.0p25.2025021200.f006.grib2
edition      centre       date         dataType     gridType     typeOfLevel  level        stepRange    shortName    packingType  
2            kwbc         20250212     fc           regular_ll   surface      0            0-6          dswrf        grid_complex_spatial_differencing 
1 of 1 messages in ./subset-GFS/gfs.0p25.2025021200.f006.grib2
```
## Step 1: Prepare Your CSV File

Ensure your `stations.csv` has the following structure:

```
station,latitude,longitude
Station1,22.3,114.2
Station2,22.6,114.6
```

## Step 2: Code to Extract Data and Save to CSV

Here's a complete Python script that reads the station information, extracts the required data from the GRIB files, and writes the results to a new CSV file.

- `$ grib_ls -p paramId,shortName,level gfs_20241204_0000_0p25_003`

{% fold info @grib_ls -p paramId,shortName,level %}
```bash
paramId     shortName   level 
134         sp          0          
228002      orog        0          
130         t           0          
228139      st          0          
260185      soilw       0          
260205      soill       0          
228139      st          0          
260185      soilw       0          
260205      soill       0          
228139      st          0          
260185      soilw       0          
260205      soill       0          
228139      st          1          
260185      soilw       1          
260205      soill       1          
260189      cnwat       0          
260056      sdwe        0          
3066        sde         0          
260037      pevpr       0          
3092        icetk       0          
167         2t          2          
174096      2sh         2          
168         2d          2          
260242      2r          2          
260255      aptmp       2          
3015        tmax        2          
3016        tmin        2          
165         10u         10         
166         10v         10         
3097        iceg        10 
......
```
{% endfold %}

-  $ `wgrib2 gfs_20241204_0000_0p25_003 -v` 
{% fold info @wgrib2 -v %}
```bash
1:0:d=2024120400:PRMSL Pressure Reduced to MSL [Pa]:mean sea level:3 hour fcst:
2:998096:d=2024120400:CLMR Cloud Mixing Ratio [kg/kg]:1 hybrid level:3 hour fcst:
3:1076966:d=2024120400:ICMR Ice Water Mixing Ratio [kg/kg]:1 hybrid level:3 hour fcst:
4:1405742:d=2024120400:RWMR Rain Mixing Ratio [kg/kg]:1 hybrid level:3 hour fcst:
5:1656285:d=2024120400:SNMR Snow Mixing Ratio [kg/kg]:1 hybrid level:3 hour fcst:
6:1757478:d=2024120400:GRLE Graupel [kg/kg]:1 hybrid level:3 hour fcst:
7:1805850:d=2024120400:REFD Reflectivity [dB]:1 hybrid level:3 hour fcst:
8:2596761:d=2024120400:REFD Reflectivity [dB]:2 hybrid level:3 hour fcst:
9:3388182:d=2024120400:REFC Composite reflectivity [dB]:entire atmosphere:3 hour fcst:
10:4239776:d=2024120400:VIS Visibility [m]:surface:3 hour fcst:
......
```
{% endfold %}

{% fold info @extract_GFS.py %}
```python
#!/bin/python

import numpy as np
import xarray as xr
import pandas as pd
import glob

#===============================================================================================
def check_assign(ds, var, lat, lon):
    # Check if 'var' exists in the dataset
    if var in ds:
        return ds[var].interp(latitude=lat, longitude=lon, method='linear').values
    else:
        return np.nan  # Assign NaN if 'var' does not exist

def calculate_wind_direction(u, v):
    """
    Calculates the meteorological wind direction from u and v components.

    Args:
        u (float or array-like): Eastward wind component.
        v (float or array-like): Northward wind component.

    Returns:
        float or array-like: Wind direction in degrees (0-360), representing
                             the direction the wind is coming *from*.
    """
    return (np.rad2deg(np.arctan2(u, v)) + 180.0) % 360.0
    
#===============================================================================================
# Step 1: Load the station data
stations = pd.read_csv('station.csv')

# Step 2: Get and sort the list of GFS GRIB files
#grib_files = sorted(glob.glob('./0p25/202509/20250914/gfs.t00z*grb') )  # Adjust the path and pattern
yyyymm="202411"
dd="30"
grib_files = sorted(glob.glob(f"./0p25/{yyyymm}/{yyyymm}{dd}/gfs_*0p25*")) 

# Remove files that end with '.idx'
grib_files = [file for file in grib_files if not file.endswith('.idx')]

# Remove the first row (first file)
if grib_files:
    grib_files = grib_files[1:4]

# Step 3: Interpolate data for each station across all GRIB files
results = []

# GFS grib file
# Coordinates:
# * time        (time) datetime64[ns] 824B 2025-02-12 2025-02-12 ... 2025-02-12
# step        (time) timedelta64[ns] 824B 0 days 06:00:00 ... 16 days 00:00:00
# surface     float64 8B 0.0
# * latitude    (latitude) float64 6kB 90.0 89.75 89.5 ... -89.5 -89.75 -90.0
# * longitude   (longitude) float64 12kB 0.0 0.25 0.5 0.75 ... 359.2 359.5 359.8
# valid_time  (time) datetime64[ns] 824B 2025-02-12T06:00:00 ... 2025-02-28


for grib_file in grib_files:
    print(f"Processing: {grib_file}")

    # Step 4: Open the GRIB files using xarray
    # ds = xr.open_mfdataset(grib_files, engine='cfgrib', combine='by_coords') #combine='nested', concat_dim="time") # combine='by_coords')
    #ds = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'typeOfLevel': 'surface', 'shortName': 'dswrf'})

    ds_2t = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': '2t'})
    ds_2r = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': '2r'}) 

    ds_10u = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': '10u'})
    ds_10v = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': '10v'})
    ds_100u = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': '100u'})
    ds_100v = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': '100v'})

    ds_20u = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'u', 'typeOfLevel': 'heightAboveGround', 'level': 20})
    ds_20v = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'v', 'typeOfLevel': 'heightAboveGround', 'level': 20})
    ds_30u = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'u', 'typeOfLevel': 'heightAboveGround', 'level': 30})
    ds_30v = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'v', 'typeOfLevel': 'heightAboveGround', 'level': 30})
    ds_40u = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'u', 'typeOfLevel': 'heightAboveGround', 'level': 40})
    ds_40v = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'v', 'typeOfLevel': 'heightAboveGround', 'level': 40})
    ds_50u = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'u', 'typeOfLevel': 'heightAboveGround', 'level': 50})
    ds_50v = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'v', 'typeOfLevel': 'heightAboveGround', 'level': 50})
    ds_80u = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'u', 'typeOfLevel': 'heightAboveGround', 'level': 80})
    ds_80v = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'v', 'typeOfLevel': 'heightAboveGround', 'level': 80})

    ds_dswrf = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'typeOfLevel': 'surface', 'shortName': 'dswrf'})
    ds_sp = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'sp'})
    ds_hpbl = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'hpbl'})

    ds_vis = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'vis', 'typeOfLevel': 'surface'}) # Visibility [m]
    ds_cape = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'cape', 'typeOfLevel': 'surface'}) # CAPE Convective Available Potential Energy [J/kg]
    ds_cin = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True, filter_by_keys={'shortName': 'cin', 'typeOfLevel': 'surface'}) # CIN Convective Inhibition [J/kg]


    #print(ds_2r)
    #exit()
    ## Print the names of the variables
    # print(ds_2t)
    # selected_time = ds_2t.time.values # It is initial time!!
    selected_time = ds_2t.valid_time.values # It is valid time
    ## Assuming selected_time_utc is in numpy datetime64 format
    selected_time_utc = pd.to_datetime(selected_time)  # Convert to pandas datetime
    selected_time_utc_plus_8 = selected_time_utc + pd.Timedelta(hours=8)  # Add 8 hours

    print(selected_time)

    # Step 5: Extract data for each station
    for index, row in stations.iterrows():
        lat = row['latitude']
        lon = row['longitude']
        
        # Step 6: Interpolate the data using bilinear interpolation
        t2m = check_assign(ds_2t, 't2m', lat, lon) - 273.15        # var name = t2m
        r2m = check_assign(ds_2r, 'r2', lat, lon)                  # var name = r2
        u10 = check_assign(ds_10u, 'u10', lat, lon)
        v10 = check_assign(ds_10v, 'v10', lat, lon)
        u80 = check_assign(ds_80u, 'u', lat, lon)
        v80 = check_assign(ds_80v, 'v', lat, lon)
        u100 = check_assign(ds_100u, 'u100', lat, lon)
        v100 = check_assign(ds_100v, 'v100', lat, lon)
        dswrf = check_assign(ds_dswrf, 'dswrf', lat, lon)
        hpbl = check_assign(ds_hpbl, 'hpbl', lat, lon)
        vis = check_assign(ds_vis, 'vis', lat, lon)
        
        # Step 7: Append the results
        results.append({
            'station': row['station'],
            'datetime[UTC]': selected_time_utc,
            'datetime[BJT]': selected_time_utc_plus_8,
            't2m[degC]': t2m,
            'r2m[%]': r2m,
            'u10[m/s]': u10,
            'v10[m/s]': v10,
            'ws10[m/s]': np.sqrt(u10**2 + u10**2),
            'wd10[deg]': calculate_wind_direction(u10, v10),
            'ws80[m/s]': np.sqrt(u80**2 + u80**2),
            'wd80[deg]': calculate_wind_direction(u80, v80),
            'ws100[m/s]': np.sqrt(u100**2 + u100**2),
            'wd100[deg]': calculate_wind_direction(u100, v100),
            'dswrf[W/m2]': dswrf,
            'hpbl[m]': hpbl,
            'vis[m]': vis,
        })

# Step 8: Create a DataFrame from the results
output_df = pd.DataFrame(results)

# Step 9: Save the results to a CSV file
output_df.to_csv(f'GFS_{yyyymm}{dd}.csv', index=False)
```
{% endfold %}

## Step 3: Run the Script

1. Make sure to replace `'path_to_your_file.grib2'` with the actual path to your GRIB file.
2. Save the script to a `.py` file and run it using Python.

## Output

The output will be a CSV file named `output.csv` with the following columns:

- `station`
- `t2m` (Temperature at 2m)
- `u10` (U-component of wind at 10m)
- `v10` (V-component of wind at 10m)
- `rh2m` (Relative humidity at 2m)
- `sdswrf` (The surface downward shortwave radiation flux)

```console
 $ cat output.csv 
station,sdswrf
Station1,52.232960662841684
Station2,47.779839172363275
```

## Note

- Ensure that the indices for `t2m`, `u10`, `v10`, `rh2m`, and `sdswrf` are correctly set according to how your GRIB data is structured.
- You may need to adjust the extraction logic based on the specific GRIB messages you are working with.

# Read Multiple GFS grib files

## Code to Extract Data and Save to CSV

{% fold info @extract_GFS_multiple.py %}
```python
#!/bin/python

#------------------------------------------------#
#Author:         wpsze
#Email：         wpsze
#date:           2025-02-19 14:29:21
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2025 All rights reserved
#------------------------------------------------#

import xarray as xr
import pandas as pd
import glob

# Step 1: Load the station data
stations = pd.read_csv('station.csv')

# Step 2: Get and sort the list of GFS GRIB files
grib_files = sorted(glob.glob('./subset-GFS/gfs.0p25.2025021200.f*.grib2') )  # Adjust the path and pattern

# Step 3: Interpolate data for each station across all GRIB files
results = []

# GFS grib file
# Coordinates:
# * time        (time) datetime64[ns] 824B 2025-02-12 2025-02-12 ... 2025-02-12
# step        (time) timedelta64[ns] 824B 0 days 06:00:00 ... 16 days 00:00:00
# surface     float64 8B 0.0
# * latitude    (latitude) float64 6kB 90.0 89.75 89.5 ... -89.5 -89.75 -90.0
# * longitude   (longitude) float64 12kB 0.0 0.25 0.5 0.75 ... 359.2 359.5 359.8
# valid_time  (time) datetime64[ns] 824B 2025-02-12T06:00:00 ... 2025-02-28


for grib_file in grib_files:
    print(f"Processing: {grib_file}")

    # Step 4: Open the GRIB files using xarray
    # ds = xr.open_mfdataset(grib_files, engine='cfgrib', combine='by_coords') #combine='nested', concat_dim="time") # combine='by_coords')
    ds = xr.open_dataset(grib_file, engine='cfgrib', decode_timedelta=True) #,backend_kwargs={'filter_by_keys': {'typeOfLevel': 'isobaricInhPa'}})

    ## Print the names of the variables
    # print(ds)
    # selected_time = ds.time.values # It is initial time!!
    selected_time = ds.valid_time.values # It is valid time
    ## Assuming selected_time_utc is in numpy datetime64 format
    selected_time_utc = pd.to_datetime(selected_time)  # Convert to pandas datetime
    selected_time_utc_plus_8 = selected_time_utc + pd.Timedelta(hours=8)  # Add 8 hours

    print(selected_time)

    # Step 5: Extract data for each station
    for index, row in stations.iterrows():
        lat = row['latitude']
        lon = row['longitude']
        
        # Step 6: Interpolate the data using bilinear interpolation
        # t2m = ds['t2m'].interp(latitude=lat, longitude=lon, method='linear').values
        # u10 = ds['u10'].interp(latitude=lat, longitude=lon, method='linear').values
        # v10 = ds['v10'].interp(latitude=lat, longitude=lon, method='linear').values
        # rh2m = ds['rh2m'].interp(latitude=lat, longitude=lon, method='linear').values
        sdswrf = ds['sdswrf'].interp(latitude=lat, longitude=lon, method='linear').values
        
        # Step 7: Append the results
        results.append({
            'station': row['station'],
            'datetime': selected_time,
            'sdswrf': sdswrf
            # 't2m': t2m,
            # 'u10': u10,
            # 'v10': v10,
            # 'rh2m': rh2m
        })

# Step 8: Create a DataFrame from the results
output_df = pd.DataFrame(results)

# Step 9: Save the results to a CSV file
output_df.to_csv('output_multiple.csv', index=False)
```
{% endfold %}

## Output

{% fold info @output_multiple.csv %}
```console
station,datetime[UTC],datetime[BJT],sdswrf
Station1,2025-02-12 06:00:00,2025-02-12 14:00:00,52.232960662841684
Station2,2025-02-12 06:00:00,2025-02-12 14:00:00,47.779839172363275
Station1,2025-02-12 09:00:00,2025-02-12 17:00:00,62.86639785766609
Station2,2025-02-12 09:00:00,2025-02-12 17:00:00,40.13679992675776
Station1,2025-02-12 12:00:00,2025-02-12 20:00:00,33.28959899902347
Station2,2025-02-12 12:00:00,2025-02-12 20:00:00,22.780800170898374
Station1,2025-02-12 15:00:00,2025-02-12 23:00:00,0.0
Station2,2025-02-12 15:00:00,2025-02-12 23:00:00,0.0
Station1,2025-02-12 18:00:00,2025-02-13 02:00:00,0.0
Station2,2025-02-12 18:00:00,2025-02-13 02:00:00,0.0
Station1,2025-02-12 21:00:00,2025-02-13 05:00:00,0.0
Station2,2025-02-12 21:00:00,2025-02-13 05:00:00,0.0
Station1,2025-02-13 00:00:00,2025-02-13 08:00:00,2.2220799827575504
Station2,2025-02-13 00:00:00,2025-02-13 08:00:00,3.2377599430084407
Station1,2025-02-13 03:00:00,2025-02-13 11:00:00,146.60479980468722
Station2,2025-02-13 03:00:00,2025-02-13 11:00:00,215.17280273437623
Station1,2025-02-13 06:00:00,2025-02-13 14:00:00,154.97856079101527
Station2,2025-02-13 06:00:00,2025-02-13 14:00:00,259.47327941894736
Station1,2025-02-13 09:00:00,2025-02-13 17:00:00,137.50800354003917
......
```
{% endfold %}

# `xarray`'s `interp` method with the `"linear"` option on a 2D array

When using `xarray`'s `interp` method with the `"linear"` option on a 2D array, the method performs linear interpolation along the specified dimensions. This allows you to compute interpolated values at new coordinate points based on existing data in two dimensions.

### How It Works for 2D Arrays

1. **Data Structure**: A 2D array in `xarray` is typically represented as a `DataArray` or a `Dataset`, where you have two dimensions (e.g., latitude and longitude, or time and another variable).
2. **Linear Interpolation**: The `"linear"` method calculates the interpolated values by creating linear relationships between existing data points. For each new coordinate point specified, it finds the nearest surrounding points in both dimensions and computes the interpolated value based on the linear ratio of the distances.

- For each new coordinate:
  - The method identifies the surrounding points in the original 2D array.
  - It calculates the interpolated value based on the linear relationship between the values at these surrounding points.
  - The interpolation is done separately for each dimension, effectively creating a **bilinear interpolation effect**.
- **2D Linear Interpolation**: The `"linear"` method allows you to compute intermediate values smoothly between known data points in a 2D grid.
- **Applications**: This is useful in various fields such as geospatial analysis, climate modeling, and any scenario where a grid of values needs to be estimated at non-integer coordinates.

Using `xarray`'s `interp` method in this way enables effective handling of multi-dimensional datasets, ensuring accurate and smooth transitions between data points.