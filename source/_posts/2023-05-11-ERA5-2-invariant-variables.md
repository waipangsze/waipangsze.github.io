---
layout: post
title: "ERA5 dataset (2) contains invariant variables"
categories: [NWP]
tags: [IC/BC, grib, ERA5, WRF, WPS]
author: wpsze
---

# ERA5 dataset (2) contains invariant variables

[Post](https://forum.mmm.ucar.edu/threads/error-while-running-real-exe-with-era5-data-in-wrf-4-3-3.11500/)
[Downlaod](https://rda.ucar.edu/datasets/ds633.0/dataaccess/)
Ref: [Initializing the WRF model with ECMWF ERA-Interim reanalysis](https://dreambooker.site/2017/12/20/initializing-the-wrf-model-with-ecmwf-era-interim-reanalysis/#/plpy)

> I am suspicious that you don't have the file that contains invariant variables, especially landmask and terrain height, which are two important variables for WRF/WPS.

> Please let me know if I am wrong. Otherwise, please download the following two files (from NCAR CISL)

> e5.oper.invariant.128_172_lsm.regn320sc.2016010100_2016010100.grb
> e5.oper.invariant.128_129_z.regn320sc.2016010100_2016010100.grb


> These files are not 'built-in' files. You have to download them, then go through the ungrib and metgrid processes.

> Note that the two files need to be ungribbed separately (with start_date and end_date = '2016-01-01_00:00:00', and prefix = 'FILE2' in &unribThese files

> Then rename FILE2:2016-01-01_00 to be FILE2, and set
```sh
&metgrid
fg_name = 'FILE1',
io_form_metgrid = 2,
constants_name = './FILE2'
```
From here you can continue to run metgrid.exe.

## Download
[Downlaod](https://rda.ucar.edu/datasets/ds633.0/dataaccess/)

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
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_026_cl.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_027_cvl.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_028_cvh.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_029_tvl.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_030_tvh.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_043_slt.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_074_sdfor.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_129_z.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_160_sdor.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_161_isor.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_162_anor.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_163_slor.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.128_172_lsm.ll025sc.1979010100_1979010100.grb  \
  https://data.rda.ucar.edu/ds633.0/e5.oper.invariant/197901/e5.oper.invariant.228_007_dl.ll025sc.1979010100_1979010100.grb  \
)
while($#filelist > 0)
  set syscmd = "wget $cert_opt $opts $filelist[1]"
  echo "$syscmd ..."
  $syscmd
  shift filelist
end
```
Two are important,

terrain height,
> e5.oper.invariant.128_129_z.ll025sc.1979010100_1979010100.grb

landmask,
> e5.oper.invariant.128_172_lsm.ll025sc.1979010100_1979010100.grb

## Ungrib

### source WRF env

### soft-link
1. link_grib.csh
2. namelist.wps
3. ungrib.exe
4. Vtable.ERA-interim.pl as Vtable
5. int2nc.exe

```sh
&share
 wrf_core = 'ARW',
 max_dom = 1,
 start_date = '1979-01-01_00:00:00',
 end_date   = '1979-01-01_00:00:00',
 interval_seconds = 10800,
 io_form_geogrid = 2
/

&ungrib
 out_format = 'WPS',
 prefix = 'FIX',
/
```
and link_grib.csh

```sh
$ ./link_grib.csh e5.oper.invariant.*
GRIBFILE.AAA -> e5.oper.invariant.128_129_z.ll025sc.1979010100_1979010100.grb
GRIBFILE.AAB -> e5.oper.invariant.128_172_lsm.ll025sc.1979010100_1979010100.grb
```

then ungrib.exe

```sh
$ ./ungrib.exe
FIX:1979-01-01_00
```

### check results
Plot Intermediate Files in netCDF Format
Using **int2nc.exe (Found in WPS/util/)**
```sh
$ ./int2nc.exe FIX:1979-01-01_00
FIX:1979-01-01_00.nc
$ ncview FIX:1979-01-01_00.nc
```

## For metgrid.exe

namelist.wps is written as

```sh
&share
 start_date = '2015-08-07_00:00:00','2015-08-07_00:00:00',
 end_date   = '2015-08-08_12:00:00','2015-08-08_12:00:00',
&metgrid
 fg_name = 'ERA5',
 constants_name = '/xxx/xxx/FIX:1979-01-01_00',
 io_form_metgrid = 2,
```

Finally,
```sh
$ ./metgrid.exe
```

