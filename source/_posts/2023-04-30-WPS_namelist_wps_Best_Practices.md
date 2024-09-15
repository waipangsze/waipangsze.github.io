---
layout: post
title: "WPS namelist.wps Best Practices"
categories: [WRF]
tags: [WRF,WPS,namelist.wps]
author: wpsze
---

# WPS namelist.wps: Best Practices

[https://www2.mmm.ucar.edu/wrf/users/namelist_best_prac_wps.html](https://www2.mmm.ucar.edu/wrf/users/namelist_best_prac_wps.html)

1. In namelist.wps, multiple columns are used for multiple domains; however max_domains determines how many columns will be used. For example, if you use 3 columns, but only set max_dom = 2, the last column will be ignored.
2. Not all parameters have mulitple columns.

## max_dom
Nested domains are necessary when scaling down from coarse first-guess (input) data to a fine resolution, and the recommended ratios for coarse data resolution to the parent domain are odd ratios of **3:1 or 5:1**. For example, if your domain of interest needs to have a resolution of 3 km, but your input data have a resolution of 27 km, and you are using a 3:1 ratio, your domain 01, should be around 9 km, and then you can have a nested domain with 3 km grid spacing. In this case, max_dom = 3.

## parent_grid_ratio
The nesting ratio relative to the domain's parent. **For the coarsest domain, parent_grid_ratio should be set to 1**.
**Recommended ratios are 3 or 5**. If you are unfamiliar with the model, it is best to leave this set to 3. It is not recommended to use an even number ratio since it is best to have a center grid cell for the fine domain that corresponds with the center of the coarse domain.
grid cell.

## i_, j_parent_start
It is good practice to keep nest boundaries away from coarse domain boundaries, and to keep them away from steep topography. As a rough approximation, **keep about 1/3 of the coarse-grid domain surrounding each side of the nest**. This does not have to be exact, and the nest does not have to be centered perfectly, but this helps to stay away from problems later. If the prevailing winds are westerly, it is better to place the nest further away from the west boundary, than the east.

## e_we, e_sn
It is recommended to have domains **no smaller than about 100x100**. Note that there will be about 10 grid points (minimum of 5) on each side, in the boundary zone. If domains are too small, the solution will be determined by forcing data.
When considering the size of the coarse domain, **there is no need to be economical. Doubling the size of the coarse grid domain will only result in a 25% nested forecast time increase**. **It's also important to consider the speed at which systems are moving through the simulated region. A multi-day forecast needs a much larger domain than a short forecast. If cost is prohibitive, consider adding an external domain.**

## geog_data_res
Prior to V3.8, if you wish to use the MODIS data, it would be necessary to specify:

> geog_data_res = 'modis_30s+10m', 'modis_30s+2m', 'modis_30s+30s'

Possible available resolutions are **10m (~19km), 5m (~9km), 2m (~4km), and 30s (~0.9km)**.
When there are multiple resolutions available for geographical input data, choose a resolution that is **slightly better** than your grid resolution. For example, **if your grid resolution is 9km, using 2 minute static data would be ideal**.

## dx, dy
This is given in meters for the Lambert, Polar, and Mercator projections. For the 'lat-lon' projection, this is given in degrees latitude. For namelist.wps it is only necessary to specify the coarse domain resolution, as nest domains will be calculated based on parent_grid_ratio.
It is recommended that **dx and dy have the same value** for Mercator, Polar, and Lambert projections.

## map_proj
- lambert: well-suited for mid-latitudes; domain cannot contain poles; domain cannot be periodic in west-east direction
- mercator: well-suited for low-latitudes; may be used for a "channel" domain (periodic in west-east direction).
- polar: good for high-latitude domains, especially if the domain contains a pole
- lat-lon: also called cylindrical equidistant; required for global domains (but does not have to be global); can be used in its normal or rotated aspect

It is best to check the range of the map scale factor (e.g., **MAPFRAC_M** for the map factor in the center of a grid cell) after running geogrid.exe (this can be checked easily with the ncview program). **Values should be close to 1 to ensure the projection is not causing distortion.**

## ref_lat, ref_lon
Real values specifying the latitude and longitude of a location whose (i,j) location in the simulation is known.
> For ARW, ref_lat and ref_lon give the latitude and longitude of a known location in the domain (it's recommended that these are set to the center of the coarse domain

## truelat1/2
- truelat1: A real value specifying the first true latitude for the Lambert conformal conic projection, or the true latitude for the polar stereographic projection, or the Mercator projection.
- truelat2: A real value specifying the second true latitude for the Lambert conformal conic projection.

## stand_lon
If this longitude is set to the **same value as ref_lon**, your coarsest domain will be centered.

## geog_data_path
It's best to set this to **the absolute path.**

## &ungrid
### out_format
If you are running ungrib in the WPS/ program, this should be set to **"WPS."**

### prefix
The prefix that will be assigned to intermediate files (produced from the ungrib.exe program). This can be left as set to 'FILE' or can be changed to anything you choose like GFS, ERA5, SST.

> If you will be using more than one type of input data, and it's necessary to run ungrib twice, you may consider setting this to something that is meaningful to the data type (e.g., "SST" for sea surface temperature data). If you do need to run ungrib twice, set this to only one prefix name per ungrib.exe run.

## &metgrid
### fg_name
This is the prefix that was used when the intermediate files were created (see above). To combine different data sources, **list all prefixes** (e.g., fg_name = 'GFS', 'SST'), that combine GFS and SST
