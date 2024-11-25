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

**Please use 1 core (serial) to run metgrid.exe.** Otherwise, the wrfout will be fail even (dmpar) of metgrid seems to be done.

{% note danger %}
**sbatch -N 1 -n 1 run_metgrid.sh**
{% endnote %}

# For large MPAS meshes

Also, in cases of large MPAS meshes, it may be necessary to increase the value of two constants in the metgrid code that **are used to statically allocate several data structures used in the computation of remapping weights from the MPAS mesh to the WRF domain**. These two constants, shown below, are located in the `WPS/src/metgrid/remapper.F` file.

```fortran
! should be at least (earth circumference / minimum grid distance)
integer, parameter :: max_queue_length    = 2700

! should be at least (nCells/32)
integer, parameter :: max_dictionary_size = 82000  
```

After changing the value of these constants, **metgrid must be recompiled**.

# Hands-on

## MPAS output nc files

File name format of MPAS* and static.nc are,

```console
MPAS.2024-05-01_00.nc  MPAS.2024-05-01_09.nc  MPAS.2024-05-01_18.nc
MPAS.2024-05-01_01.nc  MPAS.2024-05-01_10.nc  MPAS.2024-05-01_19.nc
MPAS.2024-05-01_02.nc  MPAS.2024-05-01_11.nc  MPAS.2024-05-01_20.nc
MPAS.2024-05-01_03.nc  MPAS.2024-05-01_12.nc  MPAS.2024-05-01_21.nc
MPAS.2024-05-01_04.nc  MPAS.2024-05-01_13.nc  MPAS.2024-05-01_22.nc
MPAS.2024-05-01_05.nc  MPAS.2024-05-01_14.nc  MPAS.2024-05-01_23.nc
MPAS.2024-05-01_06.nc  MPAS.2024-05-01_15.nc  MPAS.2024-05-02_00.nc
MPAS.2024-05-01_07.nc  MPAS.2024-05-01_16.nc  static.nc
MPAS.2024-05-01_08.nc  MPAS.2024-05-01_17.nc  
```

{% note primary %}
**MPAS.2024-05-01_00.nc**
{% endnote %}

## WRF metgrid

```namelist
&metgrid
 constants_name = 'mpas:static.nc',
 fg_name = 'mpas:MPAS',
 io_form_metgrid = 2, 
 opt_metgrid_tbl_path = './'
/
```

Note: There is no ungrid part.

In WPS,

- [WPS/metgrid/src/process_domain_module.F](https://github.com/wrf-model/WPS/blob/335c76a111f84503e8b963abaf273ea8053645bb/metgrid/src/process_domain_module.F#L764)

```fortran
         if (index(input_name, 'mpas:') == 1) then
            call process_mpas_fields(input_name, do_const_processing, temp_date, fg_data, got_this_field, &
                                     landmask, process_bdy_width, &
                                     u_field, v_field, &
                                     dom_dx, dom_dy, ...... )
```

e.g. log file

```log
Processing domain 1 of 2
    mpas:static.nc
 Processing 2024-05-01_00
    mpas:MPAS
 Processing 2024-05-01_03
    mpas:MPAS
 Processing 2024-05-01_06
    mpas:MPAS
Processing domain 2 of 2
    mpas:static.nc
 Processing 2024-05-01_00
    mpas:MPAS
 Processing 2024-05-01_03
    mpas:MPAS
 Processing 2024-05-01_06
    mpas:MPAS
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!  Successful completion of metgrid.  !
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
```

## WRF real/wrf

- SIZE MISMATCH: num_metgrid_levels --> 56 (default of MPAS)

```log
d01 2024-05-01_00:00:00  Yes, this special data is acceptable to use: OUTPUT FROM METGRID V4.3
d01 2024-05-01_00:00:00  Input data is acceptable to use: met_em.d01.2024-05-01_00:00:00.nc
 metgrid input_wrf.F first_date_input = 2024-05-01_00:00:00
 metgrid input_wrf.F first_date_nml = 2024-05-01_00:00:00
d01 2024-05-01_00:00:00  input_wrf.F: SIZE MISMATCH:  namelist num_metgrid_levels           =           34
d01 2024-05-01_00:00:00  input_wrf.F: SIZE MISMATCH:  input file BOTTOM-TOP_GRID_DIMENSION  =           56
```

- p_top_requested < grid%p_top

```log
rsl.error.0020:25:p_top_requested < grid%p_top possible from data
rsl.out.0016:31: p_top_requested =    1000.00000    
rsl.out.0016:32: allowable grid%p_top in data   =    1281.68921    
rsl.out.0016:35:p_top_requested < grid%p_top possible from data

-------------- FATAL CALLED ---------------
FATAL CALLED FROM FILE:  <stdin>  LINE:    1219
p_top_requested < grid%p_top possible from data
-------------------------------------------
```

How? try to set `p_top_requested = 5000`. (leave homework)

### `real.exe`

```log
d01 2024-05-01_06:00:00 real_em: SUCCESS COMPLETE REAL_EM INIT
```

### `wrf.exe `

- Segmentation fault - invalid memory reference

```log
==> rsl.out.0025 <==
d01 2024-05-01_00:00:00  Input data is acceptable to use:
 Tile Strategy is not specified. Assuming 1D-Y
WRF TILE   1 IS      1 IE     20 JS     84 JE    100
WRF NUMBER OF TILES =   1
 Flerchinger USEd in NEW version. Iterations=          10
 Flerchinger USEd in NEW version. Iterations=          10

==> rsl.error.0028 <==
d01 2024-05-01_00:00:00  Input data is acceptable to use:
 Tile Strategy is not specified. Assuming 1D-Y
WRF TILE   1 IS     61 IE     80 JS     84 JE    100
WRF NUMBER OF TILES =   1

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Backtrace for this error:
#0  0x1488066c7b4f in ???

==> rsl.out.0000 <==
d01 2024-05-01_00:00:00        14241  points exceeded w_critical_cfl in domain d01 at time 2024-05-01_00:00:00 hours
d01 2024-05-01_00:00:00 Max   W:     74     24      2 W: *******  w-cfl:     Inf  dETA:    0.01
```

Note: Be aware of the stability (here, MPAS-480km mesh is used).

Successful,

```console
 $ ls wrf*
wrfbdy_d01  wrf.exe  wrfinput_d01  wrfout_d01_2024-05-01_00:00:00  wrfrst_d01_2024-05-01_06:00:0

==> rsl. <==
Timing for Writing restart for domain        1:   14.65449 elapsed seconds
d01 2024-05-01_06:00:00 wrf: SUCCESS COMPLETE WRF
```