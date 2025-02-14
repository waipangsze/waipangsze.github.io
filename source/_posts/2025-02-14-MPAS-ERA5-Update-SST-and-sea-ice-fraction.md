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
index_img: 
banner_img: 
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

## Results

{% note primary %}
The ERA5.grib can be **IC, LBC, FDDA** and **SST/Sea-Ice updated** as well.

And, **ERA5 corresponding interemediate file** can be used.
{% endnote %}

# For MPAS

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
    config_sfc_prefix = 'SST'
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

## Registry.xml

- [MPAS-Model/src/core_init_atmosphere/Registry.xml](https://github.com/MPAS-Dev/MPAS-Model/blob/41e9a3fb8ca6b9250a7405209a5c60996318409f/src/core_init_atmosphere/Registry.xml#L347)

```xml
<package name="sfc_update" description="Used by test cases that produce surface updates."/>

<var name="sst" type="real" dimensions="nCells Time" units="K"
        description="sea-surface temperature"
        packages="met_stage_out;sfc_update"/>
<var name="xice" type="real" dimensions="nCells Time" units="unitless"
        description="fractional area coverage of sea-ice"
        packages="met_stage_out;sfc_update"/>
```



