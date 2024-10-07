---
layout: post
title: Global map of Local Climate Zones (LCZ) 
categories: [NWP, WRF, MPAS]
tags: [WRF, MPAS, Urban, UCM]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://imgur.com/Ufph6lQ
banner_img: https://imgur.com/TDDLm40
---

# Global map of Local Climate Zones

- <https://zenodo.org/records/8419340>

## Description

A global 100 m spatial resolution Local Climate Zone (LCZ) map, derived from multiple earth observation datasets and expert LCZ class labels.

The LCZ map is based on the LCZ typology (Stewart and Oke, 2012) that distinguish urban surfaces accounting for their typical combination of micro-scale land-covers and associated physical properties. The LCZ scheme is distinguished from other land use / land cover schemes by its focus on urban and rural landscape types, which can be described by any of the 17 classes in the LCZ scheme.

Out of the 17 LCZ classes, 10 reflect the 'built' environment, and each LCZ type is associated with generic numerical descriptions of key urban canopy parameters critical to model atmospheric responses to urbanisation. In addition, since LCZs were originally designed as a new framework for urban heat island studies (Stewart and Oke, 2012), they also contain a limited set (7) of 'natural' land-cover classes that can be used as 'control' or 'natural reference' areas. As these seven natural classes in the LCZ scheme can not capture the heterogeneity of the world’s existing natural ecosystems, we advise users - if required - to combine the built LCZ classes with any other land-cover product that provides a wider range of natural land-cover classes.

- Stewart ID, Oke TR. (2012). Local Climate Zones for Urban Temperature Studies. Bull Am Meteorol Soc. 93(12):1879-1900. doi:10.1175/BAMS-D-11-00019.1

## Versions

If there are there multiple versions of the dataset, list the file updated, when and why update was made:

- _v1: initial release of the data (20220222)
- _v2: add LCZ map of Iceland, consistent with the global LCZ map procedure (20221115)
- _v3: Mosaicing two Global Forest Canopy Height products two cover remote areas (eg. islands) (20231008)

## Files and brief description

Files and brief description:

1. **lcz_filter_v3.tif**

{% note primary %}
The recommended global LCZ map, compressed as GeoTIFF, with LCZ classes indicated by numbers 1-17
{% endnote %}

**The recommended global LCZ map, compressed as GeoTIFF, with LCZ classes indicated by numbers 1-17**. LCZ labels are obtained after applying the morphological Gaussian filter described in Demuzere et al. (2020). The official color scheme - as indicated in the table below - is embedded into the GeoTIFF.

The table below describes,

| LCZ class number  | Official class description  | Official hex color |
|:------------- |:---------------:|:-------------|
LCZ 1		|	Compact highrise	|	'#910613'	
LCZ 2		|	Compact midrise		|	'#D9081C'
LCZ 3		|	Compact lowrise		|	'#FF0A22'
LCZ 4		|	Open highrise		|	'#C54F1E'
LCZ 5		|	Open midrise		|	'#FF6628'
LCZ 6		|	Open lowrise		|	'#FF985E'
LCZ 7		|	Lightweight low-rise	|	'#FDED3F'
LCZ 8		|	Large lowrise		|	'#BBBBBB'
LCZ 9		|	Sparsely built		|	'#FFCBAB'
LCZ 10		|	Heavy Industry		|	'#565656'
LCZ 11 (A)	|	Dense trees		|	'#006A18'
LCZ 12 (B)	|	Scattered trees		|	'#00A926'
LCZ 13 (C)	|	Bush, scrub		|	'#628432'
LCZ 14 (D)	|	Low plants		|	'#B5DA7F'
LCZ 15 (E)	|	Bare rock or paved	|	'#000000'
LCZ 16 (F)	|	Bare soil or sand	|	'#FCF7B1'
LCZ 17 (G)	|	Water			|	'#656BFA'

	
Other characteristics:
- Projection: World Geodetic System 1984 (EPSG:4326)
- Size: 389620, 155995
- Spatial Resolution: 0.000898° (~ 100 m)
- Representative for the nominal year of 2018


1. lcz_v3.tif

Same as 1., but presenting the raw LCZ map, **before applying the morphological Gaussian filter.**


3. lcz_probability_v3.tif

A probability layer (%) that identifies how often the modal LCZ was chosen per pixel (e.g. a probability of 60% means that the modal LCZ class was mapped 30 times out of 50 LCZ models). This is a pixel-based probability, derived from the lcz_v3.tif map.

# Convert tif to netcdf

GDAL is a translator library for raster and vector geospatial data formats that is released under an MIT style Open Source License by the Open Source Geospatial Foundation

https://gdal.org/ 


```sh
micromamba env create -n gdal
micromamba install -c conda-forge gdal=3.6.1
```
and then,

```sh
whereis gdal_translate

# for checking
gdal_translate --long-usage

gdal_translate -of netCDF -co "FOMRAT=NC4" lcz_filter_v3.tif lcz_filter_v3.nc 
```

The detail of file,

```text
netcdf lcz_filter_v3 {
dimensions:
	lon = 389620 ;
	lat = 155995 ;
variables:
	char crs ;
		crs:grid_mapping_name = "latitude_longitude" ;
		crs:long_name = "CRS definition" ;
		crs:longitude_of_prime_meridian = 0. ;
		crs:semi_major_axis = 6378137. ;
		crs:inverse_flattening = 298.257223563 ;
		crs:spatial_ref = "GEOGCS[\"WGS 84\",DATUM[\"WGS_1984\",SPHEROID[\"WGS 84\",6378137,298.257223563,AUTHORITY[\"EPSG\",\"7030\"]],AUTHORITY[\"EPSG\",\"6326\"]],PRIMEM[\"Greenwich\",0,AUTHORITY[\"EPSG\",\"8901\"]],UNIT[\"degree\",0.0174532925199433,AUTHORITY[\"EPSG\",\"9122\"]],AXIS[\"Latitude\",NORTH],AXIS[\"Longitude\",EAST],AUTHORITY[\"EPSG\",\"4326\"]]" ;
		crs:crs_wkt = "GEOGCS[\"WGS 84\",DATUM[\"WGS_1984\",SPHEROID[\"WGS 84\",6378137,298.257223563,AUTHORITY[\"EPSG\",\"7030\"]],AUTHORITY[\"EPSG\",\"6326\"]],PRIMEM[\"Greenwich\",0,AUTHORITY[\"EPSG\",\"8901\"]],UNIT[\"degree\",0.0174532925199433,AUTHORITY[\"EPSG\",\"9122\"]],AXIS[\"Latitude\",NORTH],AXIS[\"Longitude\",EAST],AUTHORITY[\"EPSG\",\"4326\"]]" ;
		crs:GeoTransform = "-170.0007776279147 0.000898315284119518 0 80.03809518448114 0 -0.000898315284119518 " ;
	double lat(lat) ;
		lat:standard_name = "latitude" ;
		lat:long_name = "latitude" ;
		lat:units = "degrees_north" ;
	double lon(lon) ;
		lon:standard_name = "longitude" ;
		lon:long_name = "longitude" ;
		lon:units = "degrees_east" ;
	byte Band1(lat, lon) ;
		Band1:long_name = "GDAL Band Number 1" ;
		Band1:_Unsigned = "true" ;
		Band1:valid_range = 0s, 255s ;
		Band1:_FillValue = 0b ;
		Band1:grid_mapping = "crs" ;

// global attributes:
		:GDAL_AREA_OR_POINT = "Area" ;
		:Conventions = "CF-1.5" ;
		:GDAL = "GDAL 3.6.1, released 2022/12/14" ;
		:history = "Mon Oct 07 12:05:03 2024: GDAL CreateCopy( lcz_filter_v3.nc, ... )" ;
}
```


# References

1. Zhao, N., Ma, A., Zhong, Y., Zhao, J., & Cao, L. (2019). Self-training classification framework with spatial-contextual information for local climate zones. Remote Sensing, 11(23), 2828.
