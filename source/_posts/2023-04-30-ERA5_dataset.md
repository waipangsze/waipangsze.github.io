---
layout: post
title: "ERA5 dataset"
categories: [NWP]
tags: [WRF,WPS,MPAS,IC/BC,ERA5,ECMWF]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# ERA5 dataset

It is imperative to complete the registration process on the Copernicus Climate Data Store by creating an account.

Some related links,

[WRF using ERA5 data](https://forum.mmm.ucar.edu/threads/wrf-using-era5-data.12116/)

- [Climate Data Store - Copernicus](https://cds.climate.copernicus.eu/)
- [ERA5 (UCAR) ERA5 Reanalysis (0.25 Degree Latitude-Longitude Grid) ds633.0 DOI: 10.5065/BH6N-5N20](https://rda.ucar.edu/datasets/ds633.0/dataaccess/)
- [ERA5 hourly data on single levels from 1940 to present](https://cds.climate.copernicus.eu/cdsapp#!/dataset/reanalysis-era5-single-levels?tab=overview)
- [ERA5 hourly data on pressure levels from 1940 to present](https://cds.climate.copernicus.eu/cdsapp#!/dataset/reanalysis-era5-pressure-levels?tab=overview)

## Required Meteorological Fields for Running WRF

> [user_guide_v4/v4.1/users_guide_chap3.html#_Required_Meteorological_Fields](https://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.1/users_guide_chap3.html#_Required_Meteorological_Fields)

```sh
3D Data (e.g. data on pressure levels)
	Temperature
	U and V components of Wind
	Geopotential Height
	Relative Humidity (the code can calculate RH if Specific Humidity is available;this is controlled in the Vtable)

2D Data
	Surface Pressure
	Mean Sea Level Pressure
	Skin Temperature/SST
	2-meter Temperature
	2-meter Relative or Specific Humidity
	10-meter U and V components of wind
	Soil data (temperature and moisture) and soil height	
If any masked field is ingested, then a LANDSEA field is recommended
Water equivalent snow depth (SNOW) is a nice field to have, but not required.
SEAICE is good to have for a high latitude winter case, but it is not required.
```

## Shell script
```sh
#!/bin/sh
# source requried packages
source /home/wpsze/anaconda3/bin/activate venv

yyyy='2022'
mm='06'
dd='26'
hh='18'

for next_hh in $(seq 0 3 36); do # Forecast time = 12, 24, 36, 48, 60, 72, 84, 96, 108, 130
	NEXT_DATE=$(TZ=UTC date -d "${yyyy}-${mm}-${dd}T${hh}:00:00Z +$next_hh hour" +%Y%m%d%H)
	echo ${NEXT_DATE}

	echo "downloading " ${NEXT_DATE}
	SLoutfile="ERA5-0p25-SL-${NEXT_DATE}.grib"
	PLoutfile="ERA5-0p25-PL-${NEXT_DATE}.grib"
	resolution="0.25"

	python ERA5-sl.py ${NEXT_DATE} ${SLoutfile} ${resolution}
	python ERA5-pl.py ${NEXT_DATE} ${PLoutfile} ${resolution}
done
```
## ERA5-sl.py
```python
#!/usr/bin/env python
# Retrieve ERA5 data on single levels(sl)
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("datetime_string", help="Datetime string of the downloaded data in format YYYYMMDDHH", type=str)
parser.add_argument("ofile", help="Output file of the downloaded data, e.g. ERA5-SL-YYYYMMDDHH.grib", type=str)
parser.add_argument("resoln", help="Horizontal resolution of the downloaded data (in degree), e.g. 0.25", type=str)
args = parser.parse_args()

import datetime
datetime_object = datetime.datetime.strptime(args.datetime_string, "%Y%m%d%H")
yyyy = datetime_object.strftime("%Y")
mm = datetime_object.strftime("%m")
dd = datetime_object.strftime("%d")
hh = datetime_object.strftime("%H:00")

print("================", args)

import sys
import logging
import cdsapi
c = cdsapi.Client()
try:
    c.retrieve(
        "reanalysis-era5-single-levels",
        {
            "product_type": "reanalysis",
            "format": "grib",
            "variable": [
                "10m_u_component_of_wind",
                "10m_v_component_of_wind",
                "2m_dewpoint_temperature",
                "2m_temperature",
                "land_sea_mask",
                "mean_sea_level_pressure",
                "sea_ice_cover",
                "sea_surface_temperature",
                "skin_temperature",
                "snow_density",
                "snow_depth",
                "soil_temperature_level_1",
                "soil_temperature_level_2",
                "soil_temperature_level_3",
                "soil_temperature_level_4",
                "surface_pressure",
                "volumetric_soil_water_layer_1",
                "volumetric_soil_water_layer_2",
                "volumetric_soil_water_layer_3",
                "volumetric_soil_water_layer_4",
		"geopotential"
            ],
            "grid": [args.resoln, args.resoln],
            "year": yyyy,
            "month": [
                mm
            ],
            "day": [
                dd
            ],
            "time": [
                hh
            ],
        #    "area":[15, 90, 10, 120], # North, West, South, East. Default: global
        },
        args.ofile)
except Exception as err:
    if str(err) == "no data is available within your requested subset. Request returned no data.":
        logging.exception(err)
        sys.exit(96)
    else:
        raise err
```
> Remark: + geopotential? Yes for SOILHGT field not found in intermediate file ERA5

## ERA5-pl.py
```sh
#!/usr/bin/env python
# Retrieve ERA5 data on pressure level(pl)
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("datetime_string", help="Datetime string of the downloaded data in format YYYYMMDDHH", type=str)
parser.add_argument("ofile", help="Output file of the downloaded data, e.g. ERA5-PL-YYYYMMDDHH.grib", type=str)
parser.add_argument("resoln", help="Horizontal resolution of the downloaded data (in degree), e.g. 0.25", type=str)
args = parser.parse_args()

import datetime
datetime_object = datetime.datetime.strptime(args.datetime_string, "%Y%m%d%H")
yyyy = datetime_object.strftime("%Y")
mm = datetime_object.strftime("%m")
dd = datetime_object.strftime("%d")
hh = datetime_object.strftime("%H:00")

print("================", args)

import sys
import logging
import cdsapi
c = cdsapi.Client()
try:
    c.retrieve(
        "reanalysis-era5-pressure-levels",
        {
            "product_type": "reanalysis",
            "format": "grib",
            "variable": [
                "geopotential",
                "relative_humidity",
                "specific_humidity",
                "temperature",
                "u_component_of_wind",
                "v_component_of_wind"
            ],
            "pressure_level": [
                '1', '2', '3',
                '5', '7', '10',
                '20', '30', '50',
                '70', '100', '125',
                '150', '175', '200',
                '225', '250', '300',
                '350', '400', '450',
                '500', '550', '600',
                '650', '700', '750',
                '775', '800', '825',
                '850', '875', '900',
                '925', '950', '975',
                '1000'
            ],
            "grid": [args.resoln, args.resoln],
            "year": yyyy,
            "month": [
                mm
            ],
            "day": [
                dd
            ],
            "time": [
                hh
            ],
        #    "area":[15, 90, 10, 120], # North, West, South, East. Default: global
        },
        args.ofile)
except Exception as err:
    if str(err) == "no data is available within your requested subset. Request returned no data.":
        logging.exception(err)
        sys.exit(96)
    else:
        raise err


```

## Vtable

The Vtable that comes with WRF includes not only Vtable.ECMWF, but also Vtable.ERA-interim.pl and Vtable.ERA-interim.ml, where Vtable.ERA-interim.pl corresponds to the ERA-Interim data of the pressure level, Vtable.ERA-interim.ml corresponds to the ERA-Interim data of the model level.

### Vtable.ERA-interim.pl
**Latest commit b5df64a on Apr 12, 2011**
For use with ERA-interim pressure-level output
For ERA-interim data at NCAR, use the pl (sc and uv) and sfc sc files. 
> [Vtable.ERA-interim.pl](https://github.com/openwfm/wrf-fire/blob/master/WPS/ungrib/Variable_Tables/Vtable.ERA-interim.pl)

### Vtable.ECMWF
**Latest commit 5c01f57 on Jun 12, 2010**
> [Vtable.ECMWF](https://github.com/openwfm/wrf-fire/blob/master/WPS/ungrib/Variable_Tables/Vtable.ECMWF)

### Vtable.ERA-interim.ml
**Latest commit b5df64a on Apr 12, 2011**
For use with ERA-interim model-level output.
For ERA-interim data at NCAR, use the ml (sc and uv) and sfc sc files. 
> [Vtable.ERA-interim.ml](https://github.com/openwfm/wrf-fire/blob/master/WPS/ungrib/Variable_Tables/Vtable.ERA-interim.ml)

```sh
ln -s /xxx/ERA-interim.pl Vtable

# If Vtable.ERA-interim.pl works for you, I don't think you need to switch to Vtable.ECMWRF.
# If not, Please use Vtable.ECMWF to ungrib ERA5 data. 
ln -s /xxx/Vtable.ECMWF Vtable
```

## ./link_grib.csh
```sh
$ ./link_grib.csh ../DATA/ERA5/*.grib
```

## namelist.wps, setup &ungrib 
```sh
&ungrib
 out_format = 'WPS',
 prefix = 'FILE',
/
```

## ./ungrib.exe
```sh
$ ./ungrib.exe
```
Outputs are format of **FILE:2022-06-27_15** that is intermediate file that are ready for WRF metgrid or MPAS init.

## Error: Missing/incomplete configuration file: /home/xx/.cdsapirc??

The reason is that the .cdsapirc file under /home/xx/ is missing. This file contains the downloaded Key. We go to this link (https://cds.climate.copernicus.eu/api-how-to) to get it as required To our Key, if there is no registration, the key will appear directly on the right after registration. At this time, we will copy the things on the right
Copy the code shown next to the file $HOME/.cdsapirc (in your Unix/Linux environment).

check,
> ll -a /home/wpsze/.cdsapirc

## SOILHGT field not found in intermediate file ERA5

Besides processing the time-dependent atmospheric and land-surface ERA5 fields, I think you will also need to use ungrib to process the ERA5 invariant fields, including SOILHGT. Once you've created an intermediate file with invariant fields, you can concatenate the contents of that file to each of your time-varying intermediate files. [link](https://forum.mmm.ucar.edu/threads/error-while-interpolating-regional-initial-conditions-using-era-5-grib-data.12532/)

The HGT_M value from geogrid comes from the static topographic data. The met_em files contain both that HGT_M value, which is identical to the value from geogrid, and **SOILHGT, which is the terrain information coming from the input/first-guess data (e.g., GFS)**. During real.exe, both are used again. The HGT value in the wrfinput* files comes from the HGT_M value and should still be the same at that time (if using the default namelist setting surface_input_source=3). **real.exe also checks for SOILHGT, which is used for smoothing the coarse grid topography on domain 1 during the wrf process.** [link](https://forum.mmm.ucar.edu/threads/soilhgt.11582/)

Smooth_cg_topo and ERA-5 / ERA-Interim data?
> For ECMWF and ERA, the Vtable says it uses grib code 129 level 0 for both SOILGEO and SOILHGT.
Please download ERA5 terrain data from
/FS/DECS/DS630.0/e5.oper.invariant/201601/e5.oper.invariant.128_129_z.regn320sc.2016010100_2016010100.grb
ERA-I terrain can be found in https://rda.ucar.edu/datasets/ds627.0/, where if you download any sfc dataset, you can find terrain data in it.[link](https://forum.mmm.ucar.edu/threads/smooth_cg_topo-and-era-5-era-interim-data.9323/)
