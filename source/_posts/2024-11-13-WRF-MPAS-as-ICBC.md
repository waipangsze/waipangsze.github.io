---
layout: post
title: "WRF | MPAS Output for WRF IC/BC"
categories: [WRF]
tags: [WRF,WPS,MPAS,IC/BC]
author: wpsze
date: 2024-11-13 11:52:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/647EQap.png
banner_img: https://i.imgur.com/647EQap.png
---

# Using MPAS Output for WRF Initial and Lateral Boundary Conditions 

{% note warning %}
[Using MPAS Output for WRF Initial and Lateral Boundary Conditions](https://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.2/users_guide_chap3.html#_Using_MPAS_Output)
{% endnote %}

**copy as record**


# MPAS output stream

**Beginning with the v3.9 release of the WPS**, the `metgrid.exe` program is capable of reading native, unstructured mesh output in netCDF format from the Model for Prediction Across Scales (**MPAS**; <https://mpas-dev.github.io/>); the metgrid.exe program can then horizontally interpolate the MPAS fields directly to any domain defined by the geogrid.exe program to produce output files that are usable by the WRF real.exe program in exatly the same way as metgrid output interpolated from intermediate files. In this way, output from MPAS may be used to provide initial and lateral boundary conditions for WRF. When running an MPAS simulation, an **output stream** must be set up to contain the minimum set of fields necessary to initialize a WRF simulation. **The following output stream should be sufficient with the MPAS v5.x and later code**. 

```namelist
<stream name="wrf_ic_bc"
        type="output"
        filename_template="MPAS.$Y-$M-$D_$h.nc"
        output_interval="3:00:00" >

 <var name="xtime"/>
 <var_array name="scalars"/>
 <var name="pressure"/>
 <var name="zgrid"/>
 <var name="theta"/>
 <var name="uReconstructZonal"/>
 <var name="uReconstructMeridional"/>
 <var name="u10"/>
 <var name="v10"/>
 <var name="q2"/>
 <var name="t2m"/>
 <var name="skintemp"/>
 <var name="surface_pressure"/>
 <var name="mslp"/>
 <var name="tslb"/>
 <var name="smois"/>
</stream>
```

# metgrid namelist

After having run MPAS with a **suitable output stream defined**, a set of netCDF files containing fields on the native MPAS mesh will have been produced. Because these files do not contain fields describing the locations, geometry, and connectivity of the MPAS grid cells, this information must be provided to the metgrid program with a `“static”` file from the MPAS simulation. Therefore, it is necessary to specify MPAS netCDF files (prefixed with `‘mpas:’`) in the &metgrid namelist record with both the `constants_name` and `fg_name` variables, e.g.,

```namelist
&metgrid
 constants_name = ‘mpas:static.nc’
 fg_name = ‘mpas:MPAS’
/
```

In the above example, the metgrid.exe program would first read the MPAS `‘static.nc’` file to read mesh information and compute remapping weights from the MPAS mesh to the WRF domain defined by the geogrid.exe program, **then all time periods of the MPAS files with a prefix of** `‘MPAS’` **(and a suffix of YYYY-MM-DD_HH.nc)** would be processed. The real.exe program can then be run as usual.

Data from intermediate files created by the ungrib.exe program can be combined with MPAS data by the metgrid program. This may be useful, e.g., to use SST, sea ice, or land-surface fields from another source. An example of combining MPAS data with ERA-Interim intermediate files with soil data (with the prefix ‘ERAI_SOIL’) is shown below.

```namelist
&metgrid
  constants_name = ‘mpas:static.nc’
  fg_name = ‘mpas:MPAS’, ‘ERAI_SOIL’
/
```

Because the MPAS `‘zgrid’` field does not change in time, it can be omitted from the MPAS periodic output stream; in this case, however, the ‘zgrid’ field must be placed in its own netCDF file that must also define the dimension ‘Time’ as a netCDF unlimited dimension. Then, this file (say, ‘zgrid.nc’) can be supplied to the metgrid program using the constants_name namelist variable, e.g.,

```namelist
&metgrid
 constants_name = ‘mpas:static.nc’, ‘mpas:zgrid.nc’
 fg_name = ‘mpas:MPAS’
/
```

Placing the ‘zgrid’ field in its own file can save considerable space when long MPAS simulations are run, or when the output stream to be used as WRF initial and boundary conditions is written out at high temporal frequency. The python script, below, may serve as an example of how to extract the ‘zgrid’ field to its own netCDF file.

```python
from netCDF4 import Dataset
fin = Dataset('init.nc')
fout = Dataset('zgrid.nc','w',format='NETCDF3_64BIT')
nCells = fin.dimensions['nCells'].size
nVertLevelsP1 = fin.dimensions['nVertLevelsP1'].size
fout.createDimension(dimname='Time',size=None)
fout.createDimension(dimname='nCells',size=nCells)
fout.createDimension(dimname='nVertLevelsP1',size=nVertLevelsP1)
fout.createVariable(varname='zgrid',datatype='f',dimensions=('nCells', 'nVertLevelsP1'))
fout.variables['zgrid'][:] = fin.variables['zgrid'][:]
fout.close()
fin.close()
```

{% note warning %}
**It is worth noting that the use of native MPAS output with metgrid.exe has not been thoroughly tested for parallel (i.e., “dmpar”) builds of the WPS; it is therefore recommended to run metgrid.exe in serial when processing MPAS datasets.**
{% endnote %}

- TODO: checking 'dmpar' and 'serial' of metgrid.exe

# For large MPAS meshes

Also, in cases of large MPAS meshes, it may be necessary to increase the value of two constants in the metgrid code that **are used to statically allocate several data structures used in the computation of remapping weights from the MPAS mesh to the WRF domain**. These two constants, shown below, are located in the `WPS/src/metgrid/remapper.F` file.

```fortran
! should be at least (earth circumference / minimum grid distance)
integer, parameter :: max_queue_length    = 2700

! should be at least (nCells/32)
integer, parameter :: max_dictionary_size = 82000  
```

After changing the value of these constants, **metgrid must be recompiled**.