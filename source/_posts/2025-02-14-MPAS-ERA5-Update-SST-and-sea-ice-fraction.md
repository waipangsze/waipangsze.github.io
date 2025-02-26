---
layout: post
title: MPAS | ERA5| Update SST and sea-ice fraction
categories: [MPAS]
tags: [MPAS, NWP, WRF, PGW, ERA5, GFS, GRIB, NetCDF, CMIP6]
author: wpsze
date: 2025-02-14 11:49:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/Gs7ezqA.png
banner_img: https://i.imgur.com/Gs7ezqA.png
---

# Generating SST and sea-ice update files

- <https://www2.mmm.ucar.edu/projects/mpas/tutorial/Howard2024/index.html>
- [MPAS-Tutorial-NCAR-2023/mpas_tutorial | ftp ](https://ftp.cptec.inpe.br/pesquisa/das/MPAS-Tutorial-NCAR-2023/mpas_tutorial/)

Because MPAS-Atmosphere — at least, when run as a stand-alone model — does not contain prognostic equations for the SST and sea-ice fraction, these fields would remain constant if not updated from an external source; this is, of course, not realistic, and it will generally impact the quality of longer model simulations. Consequently, for MPAS-Atmosphere simulations longer than roughly a week, it is typically necessary to periodically update the sea-surface temperature (SST) field in the model. For real-time simulations, this is generally not an option, but it is feasible for retrospective simulations, where we have observed SST analyses available to us.

{% note primary %}
Prepare **intermediate files containing SST and sea-ice analyses**
{% endnote %}

# For ERA5 

## Intermediate files

- [Fortran script to convert netCDF to Intermediate format](https://www2.mmm.ucar.edu/wrf/users/tutorial/presentation_pdfs/202101/werner_data_util_pp.pdf)
  - Convert netCDF to Intermediate: [netcdf-to-intermediate.f](https://www2.mmm.ucar.edu/wrf/src/netcdf-to-intermediate.f)

### Reading intermediate files

- [WPS/util/src/rd_intermediate.F](https://github.com/hanschen/WPS/blob/master/util/src/rd_intermediate.F)
- `WPS/util/rd_intermediate.exe `
- USAGE: `rd_intermediate.exe <filename>`

```sh
#!/bin/bash
source /home/wpsze/WRF/WRFv440/wrf_env.sh
ln -sf /home/wpsze/WRF/WRFv440/wrf_install/WPS-4.4/bin/rd_intermediate.exe
ln -sf /home/wpsze/WRF/WRFv440/wrf_install/WPS-4.4/util/plotfmt.ncl
./rd_intermediate.exe $1
ncl plotfmt.ncl $1
```

and, on termianl,

```console
$ ./read_int.sh ERA5:2022-06-30_00
......
================================================
FIELD = SEAICE
UNITS = fraction DESCRIPTION = Sea-Ice Fraction
DATE = 2022-06-30_00:00:00 FCST = 0.000000
SOURCE = ECMWF
LEVEL = 200100.000000
I,J DIMS = 1440, 721
IPROJ = 0  PROJECTION = LAT LON
  REF_X, REF_Y = 1.000000, 1.000000
  REF_LAT, REF_LON = 90.000008, 0.000000
  DLAT, DLON = -0.250000, 0.250000
  EARTH_RADIUS = 6367.470215
DATA(1,1)=0.927246

================================================
FIELD = SST
UNITS = K DESCRIPTION = Sea-Surface Temperature
DATE = 2022-06-30_00:00:00 FCST = 0.000000
SOURCE = ECMWF
LEVEL = 200100.000000
I,J DIMS = 1440, 721
IPROJ = 0  PROJECTION = LAT LON
  REF_X, REF_Y = 1.000000, 1.000000
  REF_LAT, REF_LON = 90.000008, 0.000000
  DLAT, DLON = -0.250000, 0.250000
  EARTH_RADIUS = 6367.470215
DATA(1,1)=271.459961
================================================
......
```

### Plotting Intermediate Files: plotfmt.ncl

Plots the fields in the ungribbed intermediate files

> $ ncl plotfmt.ncl 'filename="FNL:2007-09-15_00"'

See above shell script.

{% gi 6 3-3 %}
![](https://i.imgur.com/Z3OysPt.png)
![](https://i.imgur.com/IuCdgnU.png)
![](https://i.imgur.com/698XQfv.png)
![](https://i.imgur.com/oZwecqP.png)
![](https://i.imgur.com/vSVNCUn.png)
![](https://i.imgur.com/EPqOweu.png)
{% endgi %}

## skintemp (skt) vs sst

Consider `skt - sst` form ERA5 grib file, they are different but why?

- ERA5-0p25-SL-2022063000.grib
- ERA5 grib: skt - sst 

```sh
ncvis ERA5-0p25-SL-2022063000.nc
cdo selname,sst ERA5-0p25-SL-2022063000.nc sst.nc
cdo selname,skt ERA5-0p25-SL-2022063000.nc skt.nc
cdo chname,skt,sst skt.nc skt-sst.nc
ncdiff skt-sst.nc sst.nc diff.nc
ncvis/ncview diff.nc 
```

![ERA5 grib | skt - sst |](https://i.imgur.com/WoSHTOP.png){width=500}

### Solutions

- [SST input resolution and LANDMASK | Feb 2024](https://forum.mmm.ucar.edu/threads/sst-input-resolution-and-landmask.15850/)
  - **SST in ERA5 only has valid values over the ocean**. When metgrid conducts horizontal interpolation, it may result in some discontinuity.
  - **TSK is skin temperature predicted by land model (over the ocean without sst update, it remains as skintemp at the initial time. if sst_update =1, tsk is updated by SST.**)
  - I am suspicious that SST is somehow not continuous over the coast, which affects tsk later.
  - To overcome this problem, **please use skintemp as SST**. You cam simply delete the line for SST in Vtable.ECMWF , then rerun ungrib and metgrid.
  - I would like to confirm that in ERA5, SKINTEMP and SST over the ocean are same. This will justify that we can use SKINTEMP to replace SST.
- [Metgrid SST interpolation artifacts | Nov 2023](https://forum.mmm.ucar.edu/threads/metgrid-sst-interpolation-artifacts.14755/)
  - One quick fix of the unrealistic SST along coastal areas is to **replace SST by SKINTEMP**. **Please delete the line below from Vtable.ECMWF**, then rerun ungrib.exe.
  - `34 | 1 | 0 | | SST | K | Sea-Surface Temperature |`
  - In this case SST will not be extracted from ERA5 and SKINTEMP will be used as SST. **We believe this is reasonable because SST and SKINTEMP is almost the same over ocean**.
  - In the case you attempt to use external SST instead of ERA5 SST, you need to provide landmask specifically for the external SST data, then modify METGRID.TBL so that metgrid.exe will know what landmask is used for horizontal interpolation.
    - A detailed explanation can be found at <https://www2.mmm.ucar.edu/wrf/users/tutorial/presentation_pdfs/201907/duda_wps_advanced.pdf>, and the slides 47-58 give an example how to process external soil data. The same approach is also applied to external SST.
    - the default fill-values overland is 0K (as in -273.15ºC). If you're running in high res, the interpolation of SST leads to some wonky numbers around the landmask. Probably you can set fill_missing = 285. or some other value to remedy the problem.
  - [does WRF needs TSK from global data | Nov 2020](https://forum.mmm.ucar.edu/threads/does-wrf-needs-tsk-from-global-data.9751/)
  - [Strong non-smooth temperature pattern over snow/ice/water land categories | Sep 2021](https://forum.mmm.ucar.edu/threads/strong-non-smooth-temperature-pattern-over-snow-ice-water-land-categories.10793/)
    -  I see you're using sst_update. Are you using a separate SST input data than the ERA5 data? If so, is it possible that the landsea mask for the two data types is inconsistent? Otherwise, there is a particular process necessary for processing landsea for ERA5 data. 
    -  **One suggestion is to use SKINTEMP only (instead of SST)**
- [Issue with ERA5 SST fields | Feb 2023](https://forum.mmm.ucar.edu/threads/issue-with-era5-sst-fields.12525/)
   - This is an old issue. If you **replace the SST by the skin temperature** you will have unrealistic warm waters near the coast in summer.
- [WRF regional climate simulation | Sep 2021](https://forum.mmm.ucar.edu/threads/wrf-regional-climate-simulation.10726/)
   - This will potentially lead to problem in WRF simulation, especially for long term climate simulation with sst_update.
   - To solve this issue, **please use skintemp to replace SST** following the steps below:
      - (1) in Vtable.ERA-I, **delete** the line below:
         - `34 | 1 | 0 | | SST | K | Sea-Surface Temperature |`
      - (2) rerun ungrib.exe and metgrid.exe
      - (3) run real.exe and wrf.exe
-  [Merge Vtable.GFS and Vtable.SST | Jan 2021](https://forum.mmm.ucar.edu/threads/merge-vtable-gfs-and-vtable-sst.9973/)
   - It is perfectly fine that the **SST field is derived from SKINTEMP**; however, if you are not using high-resolution time-varying SST data input, there really is no need to use the sst_update option. If you are only running for 4 days, it shouldn't be necessary.
- [Ungrib error: not reading SST | Dec 2022](https://forum.mmm.ucar.edu/threads/ungrib-error-not-reading-sst.12318/)
  - This means the code is not familiar with a level code of 160, and if you want to use that one, you'll need to modify the ungrib/src/rd_grib2.F file to allow it to work. You can search for that part of the code by looking for "Rd_grib2 does not know" in that file. After you make any modifications, you'll need to recompile ungrib.exe.


## Summary

{% note primary %}
The ERA5.grib can be **IC, LBC, FDDA** and **SST/Sea-Ice updated** as well.

And, **ERA5 corresponding interemediate file** can be used.
{% endnote %}

# Vtable

- Vtable.GFS
  - Skin temperature (can use for SST also)
{% fold info @Vtable.GFS %}
```
GRIB1| Level| From |  To  | metgrid  | metgrid | metgrid                                 |GRIB2|GRIB2|GRIB2|GRIB2|
Param| Type |Level1|Level2| Name     | Units   | Description                             |Discp|Catgy|Param|Level|
-----+------+------+------+----------+---------+-----------------------------------------+-----------------------+
  91 |   1  |   0  |      | SEAICE   | proprtn | Ice flag                                | 10  |  2  |  0  |   1 |
  81 |   1  |   0  |      | LANDSEA  | proprtn | Land/Sea flag (1=land, 0 or 2=sea)      |  2  |  0  |  0  |   1 |
  81 |   1  |   0  |      | LANDN    | proprtn |                                         |  2  |  0  | 218 |   1 |
   7 |   1  |   0  |      | SOILHGT  | m       | Terrain field of source analysis        |  0  |  3  |  5  |   1 |
  11 |   1  |   0  |      | SKINTEMP | K       | Skin temperature                        |  0  |  0  |  0  |   1 |
......
```
{% endfold %}

- Vtable.ERA-interim.pl
{% fold info @Vtable.ERA-interim.pl %}
```
GRIB | Level| Level| Level| metgrid  |  metgrid | metgrid                                  |
Code | Code |   1  |   2  | Name     |  Units   | Description                              |
-----+------+------+------+----------+----------+------------------------------------------+
 235 |  1   |   0  |      | SKINTEMP | K        | Sea-Surface Temperature                  |
  31 |  1   |   0  |      | SEAICE   | fraction | Sea-Ice Fraction                         |
  34 |  1   |   0  |      | SST      | K        | Sea-Surface Temperature                  |
......
```
{% endfold %}

- Vtable.SST
{% fold info @Vtable.SST %}
```
GRIB | Level| Level| Level| metgrid  |  metgrid | metgrid                                  |
Code | Code |   1  |   2  | Name     |  Units   | Description                              |
-----+------+------+------+----------+----------+------------------------------------------+
 172 |  1   |   0  |      | LANDSEA  | 0/1 Flag | Land/Sea flag                            |
 235 |  1   |   0  |      | SKINTEMP | K        | Sea-Surface Temperature                  |
  31 |  1   |   0  |      | SEAICE   | fraction | Sea-Ice Fraction                         |
-----+------+------+------+----------+----------+------------------------------------------+
```
{% endfold %}

## For `sst = skintemp`

- `Vtable.ERA-interim.pl` is modified as `Vtable.ERA5`
- **replace the SST by the skin temperature !!**
- **Remove** the row : `  34 |  1   |   0  |      | SST      | K        | Sea-Surface Temperature                  |`

## For using ERA5 as SST source

- use ERA5's `skintemp` as SST source instead of `sst` itself
- modify `Vtable.SST` with replace `11` by `235`
- try to keep three varibles (not only SST and SEAICE)

- Vtable.ERA5.SST
{% fold info @Vtable.ERA5.SST %}
```
GRIB | Level| Level| Level| metgrid  |  metgrid | metgrid                                  |
Code | Code |   1  |   2  | Name     |  Units   | Description                              |
-----+------+------+------+----------+----------+------------------------------------------+
 235 |  1   |   0  |      | SKINTEMP | K        | Sea-Surface Temperature                  |
  31 |  1   |   0  |      | SEAICE   | fraction | Sea-Ice Fraction                         |
 235 |  1   |   0  |      | SST      | K        | Sea-Surface Temperature                  |
```
{% endfold %}

{% note primary %}
ln -sf ${TEMPLATE_DIR}/Variable_Tables/Vtable.ERA5.SST Vtable
{% endnote %}

# For MPAS

## code

### MPAS init.nc SST issue

- `sst = skintemp` that sst values is from skintemp.
- `src/core_init_atmosphere/mpas_init_atm_cases.F`

```
if (config_met_interp) then

!ldf (2011-11-19): added initialization of the sea-surface temperature, seaice fraction, and
!seaice flag:
  sst = 0.0
  xice = 0.0
  seaice = 0.0
!ldf end.

! Set SST based on SKINTEMP field if it wasn't found in input data
if (minval(sst) == 0.0 .and. maxval(sst) == 0.0) then
    call mpas_log_write('Setting SST from SKINTEMP')
    !where (landmask == 0) sst = skintemp
    sst = skintemp
end if
```

But, ERA5's skintemp and sst are different as above.

![Left) init.nc, Right) sst.nc](https://i.imgur.com/EIkck30.png)
![init.nc - sst.nc, and same as above figure of ERA5 grib file](https://i.imgur.com/YHSha1w.png){width=500}
![It is init.nc: skintemp - sst that has shown sst is same as skintemp in init_atmosphere step in MPAS.](https://i.imgur.com/Fwn5ZjU.png){width=500}

## namelist.init_atmosphere

The key namelist options that must be set are shown below; other options can be ignored.

Note in particular that we have set the `config_init_case` **variable to 8!** This is the initialization case used to create surface update files, instead of real-data initial conditions files.

{% fold info @namelist.init_atmosphere %}
```namelist.init_atmosphere
&nhyd_model
    config_init_case = 8
    config_start_time = '2014-09-10_00:00:00'
    config_stop_time = '2014-09-20_00:00:00'
/

&data_sources
    config_sfc_prefix = 'SST' # SST or ERA5: the prefix of the intermediate data files containing SST and sea-ice
    config_fg_interval = 86400
/

&preproc_stages
    config_static_interp = false
    config_native_gwd_static = false
    config_vertical_grid = false
    config_met_interp = false
    config_input_sst = true
    config_frac_seaice = true
/
&decomposition
    config_block_decomp_file_prefix = 'x1.10242.graph.info.part.'
/
```
{% endfold %}

## streams.init_atmosphere

We have set both the `filename_template` and output_interval attributes of the "`surface`" stream. The `output_interval` should match the interval specified in the namelist for the `config_fg_interval` variable. The other streams ("`input`", "`output`", and "`lbc`") can remain unchanged — the input file should still be set to the name of the static file.

- Noted: can set `filename_template="sst.$Y-$M-$D_$h.$m.$s.nc"`

{% fold info @streams.init_atmosphere %}
```streams.init_atmosphere
<immutable_stream name="surface"
                  type="output"
                  filename_template="x1.10242.sfc_update.nc"
                  filename_interval="none"
                  packages="sfc_update"
                  output_interval="86400"/>
```
{% endfold %}

## results

{% fold info @ncdump -h sst.2022-06-30_00.00.00.nc %}
```console
 $ ncdump -h sst.2022-06-30_00.00.00.nc
netcdf sst.2022-06-30_00.00.00 {
dimensions:
	StrLen = 64 ;
	Time = UNLIMITED ; // (1 currently)
	nCells = 40962 ;
variables:
	char xtime(Time, StrLen) ;
		xtime:units = "YYYY-MM-DD_hh:mm:ss" ;
		xtime:long_name = "Model valid time" ;
	float sst(Time, nCells) ;
		sst:units = "K" ;
		sst:long_name = "sea-surface temperature" ;
	float xice(Time, nCells) ;
		xice:units = "unitless" ;
		xice:long_name = "fractional area coverage of sea-ice" ;
```
{% endfold %}

### Plot

- <https://ftp.cptec.inpe.br/pesquisa/das/MPAS-Tutorial-NCAR-2023/mpas_tutorial/240km_uniform/plot_delta_sst.py>
- `./plot_delta_sst.py x1.10242.static.nc x1.10242.sfc_update.nc`
- In this plot, we have masked out the SST differences over land, since the values of the field over land are not representative of actual SST differences, but may represent differences in, e.g., skin temperature or 2-m air temperature, depending on the source of the SST analyses.

{% fold info @plot_delta_sst.py %}
```python
#!/usr/bin/env python

import Ngl
import numpy as np
from netCDF4 import Dataset
import math
import sys


if __name__ == "__main__":

    #
    # Get the name of the file containing the static information
    #
    if len(sys.argv) < 2 or len(sys.argv) > 3:
        print('')
        print('Usage: '+sys.argv[0]+' [mesh filename] <field filename>')
        print('')
        exit(0)

    #
    # The (lat,lon) the plot is to be centered over
    #
    cenLat   = 0.0
    cenLon   = 0.0

    #
    # Projection to use for plot
    #
    projection = 'CylindricalEquidistant'


    r2d = 180.0 / math.pi             # radians to degrees

    if len(sys.argv) == 2:
        f = Dataset(sys.argv[1])
        g = f
    elif len(sys.argv) == 3:
        f = Dataset(sys.argv[2])
        g = Dataset(sys.argv[1])

    rlist = Ngl.Resources()
#    rlist.wkWidth = 1200
#    rlist.wkHeight = 1200

    rlist.wkColorMap = 'gui_default'

    wks_type = 'png'
    wks = Ngl.open_wks(wks_type, 'delta_sst', rlist)

    lonCell = g.variables['lonCell'][:] * r2d
    latCell = g.variables['latCell'][:] * r2d

    res = Ngl.Resources()

    res.sfXArray             = lonCell
    res.sfYArray             = latCell

    res.cnFillMode           = 'AreaFill'

    res.cnFillOn             = True
    res.cnLinesOn            = False
    res.cnLineLabelsOn       = False

    res.cnInfoLabelOn        = True

    res.lbLabelAutoStride    = True
    res.lbBoxLinesOn         = False

    res.mpProjection      = projection
    res.mpDataBaseVersion = 'MediumRes'
    res.mpCenterLatF      = cenLat
    res.mpCenterLonF      = cenLon
    res.mpGridAndLimbOn   = True
    res.mpGridAndLimbDrawOrder = 'PreDraw'
    res.mpGridLineColor   = 'Background'
    res.mpOutlineOn       = True
    res.mpDataBaseVersion = 'Ncarg4_1'
    res.mpDataSetName     = 'Earth..3'
    res.mpOutlineBoundarySets = 'Geophysical'
    res.mpPerimOn         = True
    res.mpLimitMode = 'LatLon'
    res.mpMinLonF = -180.0
    res.mpMaxLonF = 180.0
    res.mpMinLatF = -90.0
    res.mpMaxLatF = 90.0

    res.cnLevelSelectionMode = 'ManualLevels'
    res.cnMinLevelValF = -2.0
    res.cnMaxLevelValF = 2.0
    res.cnLevelSpacingF = 0.10
    res.lbAutoManage = False
    res.lbOrientation = 'Horizontal'
    res.lbBoxEndCapStyle = 'TriangleBothEnds'
    res.lbLabelAngleF = 90.0
    res.lbLabelFontHeightF = 0.01

    res.mpFillOn              = True            # Turn on map fill.
    res.mpFillAreaSpecifiers  = ['Land']
    res.mpSpecifiedFillColors = [0]
    res.mpAreaMaskingOn       = True            # Indicate we want to 
    res.mpMaskAreaSpecifiers  = ['Water']
    res.cnFillDrawOrder       = 'Predraw'       # Draw contours first.

    fld = f.variables['sst'][10,:] - f.variables['sst'][0,:]
    map = Ngl.contour_map(wks, fld, res)

    Ngl.end()
```
{% endfold %}

![](https://i.imgur.com/Gs7ezqA.png){width=400}

- or `$ ncdiff sst.nc init.nc diff-sst.nc`

## Registry.xml

- [MPAS-Model/src/core_init_atmosphere/Registry.xml](https://github.com/MPAS-Dev/MPAS-Model/blob/41e9a3fb8ca6b9250a7405209a5c60996318409f/src/core_init_atmosphere/Registry.xml#L347)

```xml
<package name="sfc_update" description="Used by test cases that produce surface updates."/>

<nml_option name="config_input_sst"             type="logical"       default_value="false"
      units="-"
      description="Whether to re-compute SST and sea-ice fields from surface input data set; should be set to .true. when running case 8"
      possible_values="true or false"/>

<var name="sst" type="real" dimensions="nCells Time" units="K"
        description="sea-surface temperature"
        packages="met_stage_out;sfc_update"/>
<var name="xice" type="real" dimensions="nCells Time" units="unitless"
        description="fractional area coverage of sea-ice"
        packages="met_stage_out;sfc_update"/>
```

## streams.atmosphere

- stream_list.atmosphere.surface

{% fold info @streams.atmosphere %}
```streams.atmosphere
<stream name="surface"
        type="input"
        filename_template="x1.10242.sfc_update.nc"
        filename_interval="none"
        input_interval="86400">

    <file name="stream_list.atmosphere.surface"/>

</stream>
```
{% endfold %}

## namelist.atmosphere

{% fold info @namelist.atmosphere %}
```namelist.atmosphere
&physics
    config_sst_update = true
```
{% endfold %}

## Plot diff

![](https://i.imgur.com/7shbwIg.png){width=400}

# For WRF

- [Input SST Data into the Model | SST Example from our WRF Online Tutorial](http://www2.mmm.ucar.edu/wrf/OnLineTutorial/CASES/SST/index.php)

The WRF model physics **do not predict** sea-surface temperature, vegetation fraction, albedo or sea ice. For long simulations, the model provides an alternative to read-in the time-varying data and update these fields (the sst_update option). In order to use this option, one must have access to time-varying SST and sea ice fields. Twelve monthly values of vegetation fraction and albedo are available from the geogrid program. Once these fields are processed via WPS, one may activate the sst_update option within the namelist.input file.

You will need to input your SST data during the WPS process. Take a look at the [SST Example from our WRF Online Tutorial](http://www2.mmm.ucar.edu/wrf/OnLineTutorial/CASES/SST/index.php) for information on how to do this. Because your SST data are in **netcdf format**, however, you will need to write them to intermediate format in order for metgrid to incorporate the data. To do so, you can follow the instructions in [Chapter 3 of the WRF Users' Guide](http://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.1/users_guide_chap3.html#_Writing_Meteorological_Data). You can also find an example [script for writing a netcdf file to intermediate format here](http://www2.mmm.ucar.edu/wrf/users/special_code.html). Just note that this script was provided to us from a user who wrote it specifically for their application, so you will need to make modifications for your own use.

- SST's are typically added to the model:
  - a. Use the SST at the initial time as a constant field for all time periods (this is good for short runs, like real-time runs, where SST is not updated during the WRF model run)
  - b. As an extra input **at each model input time** (this is good for long-months-model runs)
  - c. **SST:* intermediate files** generated from the SST input data (if you do not have these files, first run ungrib.exe)
    - `ln -sf ungrib/Variable_Tables/Vtable.SST Vtable`

### Interpolate the input data onto our model domain (metgrid.exe)

```namelist
start_date = '2016-10-06_00:00:00',
end_date = '2016-10-08_00:00:00',
interval_seconds = 21600,
fg_name = 'FILE', 'SST',
```

- `./metgrid.exe`
- generate `met_em.d01.xxx.nc`

### Run model

- Run `real.exe`

```namelist
&time_control
auxinput4_inname = "wrflowinp_d<domain>",
auxinput4_interval = 360,
io_form_auxinput4 = 2
 
&physics
sst_update = 1,
```

**Note**: Do not change the syntax "wrflowinp_d\<domain\>", to "wrflowinp_d01". The syntax should be left exactly as above. Input interval is in minutes.

- generate one more file as `wrflowinp_d01` that contains `SST`, `VEGFRA`, `ALBBCK` and `SEAICE` (if available) for each input time.
- Run `wrf.exe`
- To check if `SST` is updated in the model, look at field `TSK` over water.

# WRF&MPAS-A Support Forum

- [MPAS tutorial questions about ungrid and SST data | Jun 2023](https://forum.mmm.ucar.edu/threads/mpas-tutorial-questions-about-ungrid-and-sst-data.13510/)
  - (1) Starting from WPSV4.3, a configure option, --nowrf, has been added to allow for configuration of the WPS without reference to a compiled WRF model. if you only need the ungrib component of the WPS, you can compile WPS with this option.
  - (2) Most of the reanalysis products like GFS, ERA5 etc include SST. When you run ungrib to extract variables from the reanalysis data, SST will be included in the intermediate file.
- [SST update on the lakes | Jul 2023](https://forum.mmm.ucar.edu/threads/sst-update-on-the-lakes.13717/)
  - SST_UPDATE only works over water points, i.e., those grid points where landmask =0. Those points with lakemask =1 should correspond to points with landmask=0, and thus sst_update should apply to such points.
- [MPAS SST update file | Feb 2020](https://forum.mmm.ucar.edu/threads/mpas-sst-update-file.8874/)
- [Updating SST not working | Sep 2018](https://forum.mmm.ucar.edu/threads/updating-sst-not-working.200/)
  - forrtl: severe (64): input conversion error, unit -5, file Internal List-Directed Read
  - The issue seems to be that parts of the timestamp string ('xtime') that are read from input files contain garbage, which confuses the parsing code in the timekeeping module.