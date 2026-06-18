---
layout: post
title: MPAS | MPAS-JEDI | ioda | observation | .h5  
categories: [MPAS-JEDI]
tags: [MPAS, DA, MPAS-JEDI, ioda, observation, h5, Spack, Spack-Stack]
author: wpsze
date: 2026-06-18 06:25:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/6oGdLJk.png
banner_img: https://i.imgur.com/6oGdLJk.png
---

- [**NWP | BUFR data | GFS - Binary Universal Form for the Representation of meteorological data**](https://waipangsze.github.io/2026/02/27/NWP-BUFR-data/)
- [MPAS | MPAS-JEDI](https://waipangsze.github.io/2024/05/29/MPAS-JEDI_note/)
- [**MPAS | MPAS-JEDI V3.0.2**](https://waipangsze.github.io/2026/02/13/MPAS-MPAS-JEDI-V302/)
- [**MPAS | MPAS-JEDI | spack-stack 1.9.3**](https://waipangsze.github.io/2026/04/28/MPAS-MPAS-JEDI-spack-stack-1-9-3/)
- [MPAS | Joint MPAS/WRF Users Workshop 2025 | MPASv8.3.0](https://waipangsze.github.io/2025/06/05/MPAS-Joint-MPAS-WRF-Users-Workshop-2025/)
- [**Spack-Stack: building JEDIbundles on your own machine | 202506NCAS** ](https://www2.mmm.ucar.edu/projects/mpas-jedi/tutorial/202506NCAS/lectures/12-spackstack.pdf)

---

- [MPAS | MPAS-JEDI | **obs2ioda**](https://waipangsze.github.io/2026/05/05/MPAS-MPAS-JEDI-obs2ioda/)

---

# For v3 format of ioda

## surface station (sfc)

- `$ ncdump -h sfc_obs_2025060106-v3.h5`

{% fold info @ncdump -h sfc_obs_2025060106-v3.h5 %}
```log
netcdf sfc_obs_2025060106-v3 {
dimensions:
	Location = 169922 ;
	nvars = 6 ;
variables:
	int Location(Location) ;
		Location:suggested_chunk_dim = 169922LL ;
	float nvars(nvars) ;
		nvars:suggested_chunk_dim = 100LL ;

// global attributes:
		string :_ioda_layout = "ObsGroup" ;
		:_ioda_layout_version = 0 ;
		:nvars = 6 ;
		:nlocs = 169922 ;
		:nstring = 50 ;
		:min_datetime = "2025-06-01T03:00:00Z" ;
		:max_datetime = "2025-06-01T08:58:59Z" ;

group: MetaData {
  variables:
  	int64 dateTime(Location) ;
  		dateTime:_FillValue = -9223372036854775806LL ;
  		dateTime:units = "seconds since 1970-01-01T00:00:00Z" ;
  	float height(Location) ;
  		height:_FillValue = -999.f ;
  	float latitude(Location) ;
  		latitude:_FillValue = -999.f ;
  	float longitude(Location) ;
  		longitude:_FillValue = -999.f ;
  	float pressure(Location) ;
  		pressure:_FillValue = -999.f ;
  	float stationElevation(Location) ;
  		stationElevation:_FillValue = -999.f ;
  	string stationIdentification(Location) ;
  		string stationIdentification:_FillValue = "" ;
  	string variable_names(nvars) ;
  		string variable_names:_FillValue = "" ;
  } // group MetaData

group: ObsError {
  variables:
  	float airTemperature(Location) ;
  		airTemperature:_FillValue = -999.f ;
  		airTemperature:units = "K" ;
  	float specificHumidity(Location) ;
  		specificHumidity:_FillValue = -999.f ;
  		specificHumidity:units = "kg/kg" ;
  	float stationPressure(Location) ;
  		stationPressure:_FillValue = -999.f ;
  		stationPressure:units = "Pa" ;
  	float virtualTemperature(Location) ;
  		virtualTemperature:_FillValue = -999.f ;
  		virtualTemperature:units = "K" ;
  	float windEastward(Location) ;
  		windEastward:_FillValue = -999.f ;
  		windEastward:units = "m/s" ;
  	float windNorthward(Location) ;
  		windNorthward:_FillValue = -999.f ;
  		windNorthward:units = "m/s" ;
  } // group ObsError

group: ObsType {
  variables:
  	int airTemperature(Location) ;
  		airTemperature:_FillValue = -999 ;
  	int specificHumidity(Location) ;
  		specificHumidity:_FillValue = -999 ;
  	int stationPressure(Location) ;
  		stationPressure:_FillValue = -999 ;
  	int virtualTemperature(Location) ;
  		virtualTemperature:_FillValue = -999 ;
  	int windEastward(Location) ;
  		windEastward:_FillValue = -999 ;
  	int windNorthward(Location) ;
  		windNorthward:_FillValue = -999 ;
  } // group ObsType

group: ObsValue {
  variables:
  	float airTemperature(Location) ;
  		airTemperature:_FillValue = -999.f ;
  		airTemperature:units = "K" ;
  	float specificHumidity(Location) ;
  		specificHumidity:_FillValue = -999.f ;
  		specificHumidity:units = "kg/kg" ;
  	float stationPressure(Location) ;
  		stationPressure:_FillValue = -999.f ;
  		stationPressure:units = "Pa" ;
  	float virtualTemperature(Location) ;
  		virtualTemperature:_FillValue = -999.f ;
  		virtualTemperature:units = "K" ;
  	float windEastward(Location) ;
  		windEastward:_FillValue = -999.f ;
  		windEastward:units = "m/s" ;
  	float windNorthward(Location) ;
  		windNorthward:_FillValue = -999.f ;
  		windNorthward:units = "m/s" ;
  } // group ObsValue

group: PreQC {
  variables:
  	int airTemperature(Location) ;
  		airTemperature:_FillValue = -999 ;
  	int specificHumidity(Location) ;
  		specificHumidity:_FillValue = -999 ;
  	int stationPressure(Location) ;
  		stationPressure:_FillValue = -999 ;
  	int virtualTemperature(Location) ;
  		virtualTemperature:_FillValue = -999 ;
  	int windEastward(Location) ;
  		windEastward:_FillValue = -999 ;
  	int windNorthward(Location) ;
  		windNorthward:_FillValue = -999 ;
  } // group PreQC
}
```
{% endfold %}

and,

```log
Available groups/variables might include:
  - Location
  - MetaData/dateTime
  - MetaData/height
  - MetaData/latitude
  - MetaData/longitude
  - MetaData/pressure
  - MetaData/stationElevation
  - MetaData/stationIdentification
  - MetaData/variable_names
  - ObsError/airTemperature
  - ObsError/specificHumidity
  - ObsError/stationPressure
  - ObsError/virtualTemperature
  - ObsError/windEastward
  - ObsError/windNorthward
  - ObsType/airTemperature
  - ObsType/specificHumidity
  - ObsType/stationPressure
  - ObsType/virtualTemperature
  - ObsType/windEastward
  - ObsType/windNorthward
  - ObsValue/airTemperature
  - ObsValue/specificHumidity
  - ObsValue/stationPressure
  - ObsValue/virtualTemperature
  - ObsValue/windEastward
  - ObsValue/windNorthward
  - PreQC/airTemperature
  - PreQC/specificHumidity
  - PreQC/stationPressure
  - PreQC/virtualTemperature
  - PreQC/windEastward
  - PreQC/windNorthward
  - nvars
```

### Read h5 file


- `$ h5ls -r sfc_obs_2025061106-v3.h5`

{% fold info @h5ls -r sfc_obs_2025061106-v3.h5 %}
```log
/                        Group
/Location                Dataset {172608}
/MetaData                Group
/MetaData/dateTime       Dataset {172608}
/MetaData/height         Dataset {172608}
/MetaData/latitude       Dataset {172608}
/MetaData/longitude      Dataset {172608}
/MetaData/pressure       Dataset {172608}
/MetaData/stationElevation Dataset {172608}
/MetaData/stationIdentification Dataset {172608}
/MetaData/variable_names Dataset {6}
/ObsError                Group
/ObsError/airTemperature Dataset {172608}
/ObsError/specificHumidity Dataset {172608}
/ObsError/stationPressure Dataset {172608}
/ObsError/virtualTemperature Dataset {172608}
/ObsError/windEastward   Dataset {172608}
/ObsError/windNorthward  Dataset {172608}
/ObsType                 Group
/ObsType/airTemperature  Dataset {172608}
/ObsType/specificHumidity Dataset {172608}
/ObsType/stationPressure Dataset {172608}
/ObsType/virtualTemperature Dataset {172608}
/ObsType/windEastward    Dataset {172608}
/ObsType/windNorthward   Dataset {172608}
/ObsValue                Group
/ObsValue/airTemperature Dataset {172608}
/ObsValue/specificHumidity Dataset {172608}
/ObsValue/stationPressure Dataset {172608}
/ObsValue/virtualTemperature Dataset {172608}
/ObsValue/windEastward   Dataset {172608}
/ObsValue/windNorthward  Dataset {172608}
/PreQC                   Group
/PreQC/airTemperature    Dataset {172608}
/PreQC/specificHumidity  Dataset {172608}
/PreQC/stationPressure   Dataset {172608}
/PreQC/virtualTemperature Dataset {172608}
/PreQC/windEastward      Dataset {172608}
/PreQC/windNorthward     Dataset {172608}
/nvars                   Dataset {6}
```
{% endfold %}

- `$ h5dump -d "MetaData/stationIdentification" sfc_obs_2025061106-v3.h5`