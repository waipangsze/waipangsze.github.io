---
layout: post
title: MPAS | MPAS Version 8
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS]
author: wpsze
date: 2025-02-26 10:50:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# Timeline

- MPAS-Atmosphere Version 8.2.2 | 20 September 2024
- MPAS-Atmosphere Version 8.2.1 | 7 August 2024
- MPAS-Atmosphere Version 8.2.0 | 27 June 2024
- MPAS-Atmosphere Version 8.1.0 | 18 April 2024
- MPAS-Atmosphere Version 8.0.2 | 26 March 2024
- MPAS-Atmosphere Version 8.0.1 | 6 July 2023
- MPAS-Atmosphere Version 8.0.0 | 16 June 2023

# Links

1. [https://mpas-dev.github.io/](https://mpas-dev.github.io/)
2. [/MPAS-Model/releases](https://github.com/MPAS-Dev/MPAS-Model/releases)
3. [MPAS-Atmosphere Users' Guide V5.3](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_5.3.pdf)
4. [MPAS-Atmosphere Users' Guide V6.3](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_6.3.pdf)
5. [MPAS-Atmosphere Users' Guide V7.0](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_7.0.pdf)
6. [MPAS-Atmosphere Users' Guide V8.2.0](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_8.2.0.pdf)
7. [Joint WRF/MPAS Users Workshop 2024](https://www.mmm.ucar.edu/events/133129/agenda)

# MPAS-Atmosphere Version 8.0.0

- Changes to the initialization include:
  - **Enable parallel remapping of static fields** with arbitrary graph partition files; special CVT partition files are no longer required.  
    - ~~Run init atmosphere model with **only one MPI task** specified to create static.nc~~
    - ~~Note that it is critical for this step that the initialization core is **run serially**;~~
    - **Can parallel run ! But keep in mind that memory issue because NCAR they just set each core real all geo-dataset instead of needed partittion.**
  - Reset the default for the lower air-temperature extrapolation **(config_extrap_airtemp) from 'linear' to 'lapse-rate' in the namelist**. This applies to initialization and to lateral boundary condition generation for MPAS-A.
  - Set the condition for the lower extrapolation of the horizontal velocity such that it **returns the lowest analysis level value** instead of a linear extrapolation when the requested level is below the analysis level.
  - Create a new init case (13) for creating 3-d CAM-MPAS grids.
- Changes to the physics include:
  - Update the **Noah land surface scheme to the WRF 4.5** release.
  - Update the **MM5 surface layer scheme to the WRF 4.5** release.
  - Implemented the **CCPP-compliant version** of:
    - the revised MM5 surface layer scheme;
    - the parameterization of the gravity-wave drag over orography;
    - the YSU Planetary Boundary Layer scheme;
    - the scale-aware nTiedtke parameterization of convection; and
    - the WSM6 cloud microphysics parameterization.
  - Correct the initialization of the **maximum snow albedo over sea ice points (now set to 0.75** instead of 0).
  - Fix the option that defines the surface albedo over sea ice points. The default option is now set to zero and the default value for **the surface albedo over sea ice points is set to 0.65**.
  - In the dynamics, rework the computation of the advective tendency of potential temperature needed as forcing in the **nTiedtke** and **Grell-Freitas** parameterizations of deep convection. The advective tendency of potential temperature is **now computed the same way** for the nTiedtke and Grell-Freitas convection schemes.

{% note primary %}
IMPORTANT NOTE: The updated physics schemes require new look-up tables, in particular, for the Noah land-surface model. The **checkout_data_files.sh** script that is run at build time should correctly update these tables, but tables in other run directories will need to be manually updated.
{% endnote %}

- Include changes to the initialization and to MPAS-A such that this release can be **directly used in CESM/CAM**.

# Enable parallel remapping of static fields

```sh
##-- For spack init, (it takes longer startup time !!)
. /home/wpsze/spack/share/spack/setup-env.sh
source /home/wpsze/MPAS-A/intel/mpas_env_intel.sh
#ln -sf /home/wpsze/MPAS-A/intel/mpasv821/MPASv821/init_atmosphere_model
ln -sf /home/wpsze/MPAS-A/intel/mpasv822/MPASv822/init_atmosphere_model

# soft-link graph.info.part.3
mpirun -n 3 ./init_atmosphere_model
```

and, add below in namelist.init_atmosphere

```namelist.init_atmosphere
&decomposition
   config_block_decomp_file_prefix = 'graph.info.part.'
/
```

{% fold info @namelist.init_atmosphere %}
```namelist.init_atmosphere 
&nhyd_model
    config_init_case = 7
/
&dimensions
    config_nvertlevels = 1
    config_nsoillevels = 1
    config_nfglevels = 1
    config_nfgsoillevels = 1
/
&data_sources
    config_geog_data_path = '/home/wpsze/WRF/wrf_geog/WRF/WPS_GEOG/'
    config_landuse_data = 'MODIFIED_IGBP_MODIS_NOAH'
    config_topo_data = 'GMTED2010'
    config_vegfrac_data = 'MODIS'
    config_albedo_data = 'MODIS'
    config_maxsnowalbedo_data = 'MODIS'
    config_supersample_factor = 3
    config_use_spechumd = false
/
&preproc_stages
    config_static_interp = true
    config_native_gwd_static = true
    config_vertical_grid = false
    config_met_interp = false
    config_input_sst = false
    config_frac_seaice = false
/

&decomposition
   config_block_decomp_file_prefix = 'graph.info.part.'
/
```
{% endfold %}

# Error

## array 'global_list' above upper bound 

- Only encounter after `MPASv8.2.0`

{% fold info @log.init_atmosphere.0000.out %}
```log
----------------------------------------------------------------------
Beginning MPAS-init_atmosphere Output Log File for task       0 of       6
    Opened at 2025/03/05 15:54:12
----------------------------------------------------------------------

 
 MPAS Init-Atmosphere Version 8.2.2
 
 
 Output from 'git describe --dirty': unknown
 
 Compile-time options:
   Build target: gfortran
   OpenMP support: no
   OpenACC support: no
   Default real precision: single
   Compiler flags: debug
   I/O layer: PIO 2.x
 
 Run-time settings:
   MPI task count: 6
 
 Reading namelist from file namelist.init_atmosphere
 *** Encountered an issue while attempting to read namelist record physics
     The following values will be used for variables in this record:
 
         config_tsk_seaice_threshold = 100.000
 
 *** Encountered an issue while attempting to read namelist record io
     The following values will be used for variables in this record:
 
         config_pio_num_iotasks = 0
         config_pio_stride = 1
 
 
 ----- I/O task configuration: -----
 
     I/O task count  = 6
     I/O task stride = 1
 
 Initializing MPAS_streamInfo from file streams.init_atmosphere
 Reading streams configuration from file streams.init_atmosphere
 Found mesh stream with filename template static.nc
 Using io_type Parallel-NetCDF (CDF-5, large variable support) for mesh stream
  ** Attempting to bootstrap MPAS framework using stream: input
 Bootstrapping framework with mesh fields from input file 'static.nc'
```
{% endfold %}

{% fold info @log_error %}
```log
MPASv8.1.0/8.2.0
free(): invalid next size (normal)

Running init_atmosphere in directory: 
At line 183 of file mpas_block_decomp.F
Fortran runtime error: Index '30210' of dimension 1 of array 'global_list' above upper bound of 30209

Running init_atmosphere
init_atmosphere_model: malloc.c:4106: _int_malloc: Assertion `(unsigned long) (size) >= (unsigned long) (nb)' failed.

Running init_atmosphere
munmap_chunk(): invalid pointer
```
{% endfold %}

- [errors when running the baroclinic wave case for MPAS-A](https://forum.mmm.ucar.edu/threads/errors-when-running-the-baroclinic-wave-case-for-mpas-a.17200/)
  - `At line 183 of file mpas_block_decomp.F: Fortran runtime error: Index '40962' of dimension 1 of array 'global_list' above upper bound of 40961`
  - This is because with the clobber_mode = "'never_modify", the output file cannot be overwritten. Please delete the file 'x1.40962.init.nc' from your previous run and rerun your case.
  - I am not sure yet what caused this issue. Please rerun this case with the fix of the 1st issue and let me know if you still get the same error.
  - Your suggestion fixed the issue. I have also use the **"overwrite" option**, that works as well.
  
## `nSoilComps` in static.nc

```log
ERROR: At least one fields to be read from the 'input' stream is dimensioned
ERROR: by 'nSoilComps', but the 'nSoilComps' dimension is not defined
ERROR: in the file static.nc
CRITICAL ERROR: Please check the input file(s) to be read by the 'input' input stream.
```

{% fold info @src/core_init_atmosphere/Registry.xml %}
```src/core_init_atmosphere/Registry.xml
description="The number of atmospheric levels, always one more than the number of layers"/>
<dim name="nSoilComps" definition="8"
description="The number of soil textures as needed as input in the NOAH-MP land surface model"/>

<!-- SOIL COMPOSITION fields needed for the NOAH-MP land surface scheme -->
<var name="soilcomp" type="real" dimensions="nSoilComps nCells" units="percent"
description="soil composition needed as input in the NOAH-MP land surface model"/>
```
{% endfold %}

{% fold info @src/core_atmosphere/physics/Registry_noahmp.xml %}
```src/core_atmosphere/physics/Registry_noahmp.xml
<dim name="nSoilComps" definition="8"
description="The number of soil textures as needed as input in the NOAH-MP land surface model"/>

<var name="soilcomp" type="real" dimensions="nSoilComps nCells" units="unitless"
description="soil composition needed as input in the NOAH-MP land surface model"
```
{% endfold %}

- [Commit d4a1c7f on May 31, 2024](https://github.com/MPAS-Dev/MPAS-Model/commit/d4a1c7f57288c6fff287a914fb98135aca391467)
  - * In ./src/core_init_atmosphere, added the initialization of the static variables **soilcomp, soilcl1, soilcl2, soilcl3, and soilcl4** needed to run the Noah-MP land surface scheme.

{% note primary %}
The **static.nc** is different after **Noah-MP** is applied. (**v8.2.2 ... v8.2.0**). And, re-generate static.nc
{% endnote %}

## Solution

- **Fail** if using `gfortran` (v9.3.0) complier.
- **Sucessfull** if using `intel-mpi` complier.

{% fold info @gfortran: log %}
```
----------------------------------------------------------------------
Beginning MPAS-init_atmosphere Output Log File for task       0 of       6
    Opened at 2025/03/06 15:23:51
----------------------------------------------------------------------

 
 MPAS Init-Atmosphere Version 8.2.2
 
 
 Output from 'git describe --dirty': unknown
 
 Compile-time options:
   Build target: gfortran
   OpenMP support: no
   OpenACC support: no
   Default real precision: single
   Compiler flags: debug
   I/O layer: PIO 2.x
 
 Run-time settings:
   MPI task count: 6
 
 Reading namelist from file namelist.init_atmosphere
 *** Encountered an issue while attempting to read namelist record physics
     The following values will be used for variables in this record:
 
         config_tsk_seaice_threshold = 100.000
 
 *** Encountered an issue while attempting to read namelist record io
     The following values will be used for variables in this record:
 
         config_pio_num_iotasks = 0
         config_pio_stride = 1
 
 
 ----- I/O task configuration: -----
 
     I/O task count  = 6
     I/O task stride = 1
 
 Initializing MPAS_streamInfo from file streams.init_atmosphere
 Reading streams configuration from file streams.init_atmosphere
 Found mesh stream with filename template static.nc
 Using io_type Parallel-NetCDF (CDF-5, large variable support) for mesh stream
  ** Attempting to bootstrap MPAS framework using stream: input
 Bootstrapping framework with mesh fields from input file 'static.nc'
```
{% endfold %}

{% fold info @intel-mpi: log %}
```
----------------------------------------------------------------------
Beginning MPAS-init_atmosphere Output Log File for task       0 of       6
    Opened at 2025/03/06 17:08:04
----------------------------------------------------------------------

 
 MPAS Init-Atmosphere Version 8.2.2
 
 
 Output from 'git describe --dirty': unknown
 
 Compile-time options:
   Build target: intel-mpi
   OpenMP support: no
   OpenACC support: no
   Default real precision: single
   Compiler flags: optimize
   I/O layer: PIO 2.x
 
 Run-time settings:
   MPI task count: 6
 
 Reading namelist from file namelist.init_atmosphere
 *** Encountered an issue while attempting to read namelist record physics
     The following values will be used for variables in this record:
 
         config_tsk_seaice_threshold = 100.000
 
 *** Encountered an issue while attempting to read namelist record io
     The following values will be used for variables in this record:
 
         config_pio_num_iotasks = 0
         config_pio_stride = 1
 
 
 ----- I/O task configuration: -----
 
     I/O task count  = 6
     I/O task stride = 1

......

 --- isice_lu   = 15
 --- config_frac_seaice      : F
 --- xice_threshold          : 0.500000
 --- tsk_seaice_threshold    : 100.000
 
 number of seaice cells converted to land cells 2 =         210
 
 ********************************************************
    Finished running the init_atmosphere core
 ********************************************************
 
 
  Timer information:
     Globals are computed across all threads and processors
 
  Columns:
     total time: Global max of accumulated time spent in timer
     calls: Total number of times this timer was started / stopped.
     min: Global min of time spent in a single start / stop
     max: Global max of time spent in a single start / stop
     avg: Global max of average time spent in a single start / stop
     pct_tot: Percent of the timer at level 1
     pct_par: Percent of the parent timer (one level up)
     par_eff: Parallel efficiency, global average total time / global max total time
 
 
    timer_name                                            total       calls        min            max            avg      pct_tot   pct_par     par_eff
  1 total time                                           9.53601         1        9.53588        9.53601        9.53594   100.00       0.00       1.00
  2  initialize                                          0.85724         1        0.85061        0.85724        0.85206     8.99       8.99       0.99
 
 -----------------------------------------
 Total log messages printed:
    Output messages =                  522
    Warning messages =                   1
    Error messages =                     0
    Critical error messages =            0
 -----------------------------------------
 Logging complete.  Closing file at 2025/03/06 17:08:14
```
{% endfold %}


# An aerosol climatology file `QNWFA_QNIFA_SIGMA_MONTHLY.dat`

## MPAS Version 8.2.0

- The **aerosol-aware Thompson microphysics (as in WRF v4.1.4)** is available by setting `config_microp_scheme = 'mp_thompson_aerosols'` in the `&physics` namelist group.
- An aerosol climatology file **(QNWFA_QNIFA_SIGMA_MONTHLY.dat)** is used when running the `init_atmosphere_model` program to produce initial and lateral boundary conditions for `nifa` and `nwfa`.
- download: <https://mpas-dev.github.io/>

# `mpi_f08`

`mpi_f08` 是一種用於 Fortran 2008 的 MPI（Message Passing Interface）模組介面。 MPI 是一種用於平行運算的標準化訊息傳遞庫，廣泛用於高效能運算環境中。 `mpi_f08` 模組提供了一個現代化的接口，允許 Fortran 2008 程式使用 MPI 進行平行計算。

在 Fortran 2008 中，`mpi_f08` 模組支援新的 Fortran 語言特性，並提供了更好的與現代 Fortran 編譯器的兼容性。相較之下，傳統的 `mpi` 模組可能不支援所有 Fortran 2008 的特性，因此在支援 `mpi_f08` 的環境中，優先使用 `mpi_f08` 可以提高程式碼的可移植性和相容性。

例如，在 MPAS 模型中，如果偵測到 `mpi_f08` 模組，則會優先使用它來呼叫 Fortran MPI例程，而不是使用舊的 `mpi` 模組介面。

`mpi_f08` 和 `mpif90` 是兩個不同的概念，分別與 MPI（Message Passing Interface）和 Fortran 編譯器相關。

## `mpi_f08`

- **定義**：`mpi_f08` 是一種用於 Fortran 2008 的 MPI 模組介面。它提供了一個現代化的接口，支援 Fortran 2008 的新特性，如非同步操作和假設類型參數。
- **優點**：`mpi_f08` 提供了更好的類型檢查和非阻塞調用支持，且與 Fortran 2008 標準完全兼容。
- **應用**：推薦用於所有新的 MPI Fortran 應用程序，因為它提供了更強的類型安全性和更好的性能優化。

## `mpif90`

- **定義**：`mpif90` 是一種用於編譯 Fortran 90 MPI 程式的編譯器包裝器。它通常與 OpenMPI 等 MPI 實作一起使用。
- **狀態**：在 OpenMPI v1.7 中，`mpif90` 已被標記為過時，建議使用 `mpifort` 取代。
- **應用**：雖然仍然支援 `mpif90`，但建議使用 `mpifort` 來編譯所有版本的 Fortran MPI 應用程序，因為 `mpifort` 更加通用和現代化。

總之，`mpi_f08` 是一個現代化的 MPI Fortran 接口，而 `mpif90` 是一個較老的編譯器包裝器，已經被 `mpifort` 所取代。

# Referecnes

1. [MPAS v8.2 fails to compile GNU 13.2 Linux64 | Jul 2, 2024](https://forum.mmm.ucar.edu/threads/mpas-v8-2-fails-to-compile-gnu-13-2-linux64.17999/)