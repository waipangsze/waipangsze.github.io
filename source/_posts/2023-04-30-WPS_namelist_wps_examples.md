---
layout: post
title: WRF | WPS namelist.wps Examples
categories: [WRF]
tags: [WRF,WPS,namelist.wps]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# WPS namelist.wps Examples

## namelist.wps
[Git namelist.wps](https://github.com/wrf-model/WPS/blob/master/namelist.wps)

```sh
&share
 wrf_core = 'ARW',
 max_dom = 2,
 start_date = '2019-09-04_12:00:00','2019-09-04_12:00:00',
 end_date   = '2019-09-06_00:00:00','2019-09-04_12:00:00',
 interval_seconds = 10800
/

&geogrid
 parent_id         =   1,   1,
 parent_grid_ratio =   1,   3,
 i_parent_start    =   1,  53,
 j_parent_start    =   1,  25,
 e_we              =  150, 220,
 e_sn              =  130, 214,
 geog_data_res = 'default','default',
 dx = 15000,
 dy = 15000,
 map_proj = 'lambert',
 ref_lat   =  33.00,
 ref_lon   = -79.00,
 truelat1  =  30.0,
 truelat2  =  60.0,
 stand_lon = -79.0,
 geog_data_path = '/glade/work/wrfhelp/WPS_GEOG/'
/

&ungrib
 out_format = 'WPS',
 prefix = 'FILE',
/

&metgrid
 fg_name = 'FILE'
/
```

## namelist.wps.all_options
[Git namelist.wps.all_options](https://github.com/wrf-model/WPS/blob/master/namelist.wps.all_options)

```sh
&share
 wrf_core = 'ARW',
 max_dom = 2,
 start_date = '2006-08-16_12:00:00','2006-08-16_12:00:00',
 end_date   = '2006-08-16_18:00:00','2006-08-16_12:00:00',
 interval_seconds = 21600
 active_grid = .true., .true.,
 subgrid_ratio_x = 1
 subgrid_ratio_y = 1
 io_form_geogrid = 2,
 opt_output_from_geogrid_path = './',
 debug_level = 0
/
 start_date = '2000-01-24_12:00:00','2000-01-24_12:00:00',
 end_date   = '2000-01-25_12:00:00','2000-01-24_12:00:00',
 start_year   = 2006, 2006,
 start_month  =   08,   08,
 start_day    =   16,   16,
 start_hour   =   12,   12,
 start_minute =   00,   00,
 start_second =   00,   00,
 end_year     = 2006, 2006,
 end_month    =   08,   08,
 end_day      =   16,   16,
 end_hour     =   18,   12,
 end_minute   =   00,   00,
 end_second   =   00,   00,

&geogrid
 parent_id         =   1,   1,
 parent_grid_ratio =   1,   3,
 i_parent_start    =   1,  31,
 j_parent_start    =   1,  17,
 s_we              =   1,   1,
 e_we              =  74, 112,
 s_sn              =   1,   1,
 e_sn              =  61,  97,
 !
 !!!!!!!!!!!!!!!!!!!!!!!!!!!! IMPORTANT NOTE !!!!!!!!!!!!!!!!!!!!!!!!!!!!
 ! The default datasets used to produce the MAXSNOALB and ALBEDO12M
 ! fields have changed in WPS v4.0. These fields are now interpolated
 ! from MODIS-based datasets.
 !
 ! To match the output given by the default namelist.wps in WPS v3.9.1,
 ! the following setting for geog_data_res may be used:
 !
 ! geog_data_res = 'maxsnowalb_ncep+albedo_ncep+default', 'maxsnowalb_ncep+albedo_ncep+default',
 !
 !!!!!!!!!!!!!!!!!!!!!!!!!!!! IMPORTANT NOTE !!!!!!!!!!!!!!!!!!!!!!!!!!!!
 !
 geog_data_res = 'default','default',
 dx        = 30000,
 dy        = 30000,
 map_proj  = 'lambert',
 ref_lat   =  34.83,
 ref_lon   = -81.03,
 ref_x     =  37.0,
 ref_y     =  30.5,
 truelat1  =  30.0,
 truelat2  =  60.0,
 stand_lon = -98.0,
 geog_data_path = '/glade/work/wrfhelp/WPS_GEOG/'
 opt_geogrid_tbl_path = 'geogrid/'
/
 geog_data_res     = 'modis_lakes+10m','modis_lakes+2m',
 geog_data_res     = 'usgs_lakes+10m','usgs_lakes+2m',

&ungrib
 out_format = 'WPS',
 prefix     = 'FILE',
 ec_rec_len = 26214508,
 pmin = 100.
/

&metgrid
 fg_name         = 'FILE'
 constants_name  = './TAVGSFC'
 io_form_metgrid = 2, 
 opt_output_from_metgrid_path = './',
 opt_metgrid_tbl_path         = 'metgrid/',
 process_only_bdy = 5,
/

&mod_levs
 press_pa = 201300 , 200100 , 100000 , 
             95000 ,  90000 , 
             85000 ,  80000 , 
             75000 ,  70000 , 
             65000 ,  60000 , 
             55000 ,  50000 , 
             45000 ,  40000 , 
             35000 ,  30000 , 
             25000 ,  20000 , 
             15000 ,  10000 , 
              5000 ,   1000
/

&plotfmt
 ix = 100
 jx = 100
 ioff = 30
 joff = 30
/
```

## namelist.wps.global
[Git namelist.wps.global](https://github.com/wrf-model/WPS/blob/master/namelist.wps.global)

```sh
&share
 wrf_core = 'ARW',
 max_dom = 2,
 start_date = '2006-08-16_12:00:00','2006-08-16_12:00:00',
 end_date   = '2006-08-16_12:00:00','2006-08-16_12:00:00',
 interval_seconds = 21600
 io_form_geogrid = 2,
/

&geogrid
 parent_id         =   1,   1,
 parent_grid_ratio =   1,   3,
 i_parent_start    =   1,  31,
 j_parent_start    =   1,  17,
 e_we              =  129, 127,
 e_sn              =  65,  64,
 !
 !!!!!!!!!!!!!!!!!!!!!!!!!!!! IMPORTANT NOTE !!!!!!!!!!!!!!!!!!!!!!!!!!!!
 ! The default datasets used to produce the MAXSNOALB and ALBEDO12M
 ! fields have changed in WPS v4.0. These fields are now interpolated
 ! from MODIS-based datasets.
 !
 ! To match the output given by the default namelist.wps in WPS v3.9.1,
 ! the following setting for geog_data_res may be used:
 !
 ! geog_data_res = 'maxsnowalb_ncep+albedo_ncep+default', 'maxsnowalb_ncep+albedo_ncep+default',
 !
 !!!!!!!!!!!!!!!!!!!!!!!!!!!! IMPORTANT NOTE !!!!!!!!!!!!!!!!!!!!!!!!!!!!
 !
 geog_data_res = 'default','default',
 map_proj = 'lat-lon',
 stand_lon = 0.
 pole_lat = 90.0
 pole_lon = 0.0
 geog_data_path = '/glade/work/wrfhelp/WPS_GEOG/'
/
 ref_lat = 45.0
 ref_lon = -98
 dx = 1.0
 dy = 1.0

&ungrib
 out_format = 'WPS',
 prefix = 'FILE',
/

&metgrid
 fg_name = 'FILE'
 io_form_metgrid = 2, 
/

&mod_levs
 press_pa = 201300 , 200100 , 100000 , 
             95000 ,  90000 , 
             85000 ,  80000 , 
             75000 ,  70000 , 
             65000 ,  60000 , 
             55000 ,  50000 , 
             45000 ,  40000 , 
             35000 ,  30000 , 
             25000 ,  20000 , 
             15000 ,  10000 , 
              5000 ,   1000
/
```


