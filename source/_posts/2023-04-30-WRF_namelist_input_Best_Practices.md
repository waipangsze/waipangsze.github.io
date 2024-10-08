---
layout: post
title: "WRF | WRF namelist.input: Best Practices"
categories: [WRF]
tags: [WRF,namelist.input]
author: wpsze
index_img: 
banner_img: 
---

# WRF namelist.input: Best Practices

[https://www2.mmm.ucar.edu/wrf/users/namelist_best_prac_wrf.html](https://www2.mmm.ucar.edu/wrf/users/namelist_best_prac_wrf.html)

[Git: WRF/run/README.namelist](https://github.com/wrf-model/WRF/blob/master/run/README.namelist)

1. In namelist.input, multiple columns are used for multiple domains; however max_dom determines how many columns will be used. For example, if you use 3 columns, but only set max_dom = 2, the last column will be ignored.
2. Not all parameters have mulitple columns. In the descriptions below, it has been specified if a parameter is to be used for multiple domains (max_dom). If this is not shown, then you must only specify one value (in the first column), or you will receive a namelist error upon running.

## &domains

### time_step 
It is recommended to use a value of **5-6xDX (in km)** for a typical case. If you are using many vertical levels and/or with map-scale-factors much larger than 1, you will need to use a smaller time_step. If you are getting CFL errors that stop your run, this means your run has become unstable and **you may need to decrease this value to about 4xDX (or perhaps even 3xDX)**. It makes things easier if you use a time step that evenly divides into your history_interval, so that your output times will be evenly spaced.

### e_vert (max_dom)
The number of vertical (or Eta) levels onto which real.exe will interpolate the incoming data. This number must be the same for each domain.
It is not recommended to have fewer than 35 levels.** Typically 40-60 levels is recommended**.

### p_top_requested
The pressure top (in Pa) to use in the model. This level must be available in the incoming WPS data.
> The **default and recommended value is 5000 Pa**, and it is not suggested to use a level any lower (in the atmosphere) than this.

### num_metgrid_levels
look for the value near the top of the log.ncdump file.
> ncdump -h met_em.d01.<date> >& log.ncdump

### feedback
This determines whether feedback will be used when using nesting, and should be used to determine whether the run will be a one-way or two-way nested run.
When feedback is on, the values of the coarse domain are overwritten by the values of the variables (average of cell values for mass points, and average of the cell-face values for horizontal momentum points) in the nest at the coincident points. For masked fields, only the single point value at the collocating points is fed back. If the parent_grid_ratio is even, an arbitrary choice of the southwest corner point value is used for feedback. This is the reason it is recommended to use an odd parent_grid_ratio with this option. When feedback is off , it is equivalent to a one-way nested run, since nest results are not reflected in the parent domain.

**usually, set is feedback = 0 (off)**

### smooth_option
Determines whether smoothing is to be used for the parent domain in the area of the nest **if feedback is on (feedback = 1)**. Three options are available:
- 0 = no smoothing
- 1 = 1-2-1 smoothing
- 2 = smoothing-desmoothing.
**It is typically recommended to keep this option set to 0.**

## &physics

### mp_physics (max_dom)
WSM6 or ...

### ra_lw_physics (max_dom)
This should be set to the same option for each domain. **We recommend using the RRTMG option (option 4).**

### ra_sw_physics (max_dom)
This should be set to the same option for each domain. **We recommend using the RRTMG option (option 4).**

### radt (max_dom)
Minutes between radiation physics calls.
It is recommended to **use 1 minute per km of dx** (e.g., **set to 10 for 10km parent domain**). If you are running a high-resolution domain and compute time is a concern, then it is okay to use radt = 10-15 min, along with setting 'swint_opt'.**Use the same value for all domains**.

### sf_sfclay_physics (max_dom)
Surface layer physics option.
A given option may only work with a certain PBL option.
This should be set to **the same option for each domain**.

### sf_surface_physics (max_dom)
Land surface model (LSM) option.
This choice must be made prior to running real.exe. num_soil_layers must also match this option.
This should be set to the **same option for each domain**. If you decide to change this option later, **make sure num_soil_layers is set correctly for the new option, and you will need to re-run real.exe**.

### num_soil_layers
If you choose to use a different LSM later, you must make sure this value is set correctly for the new model, and you will need to re-run real.exe.

### bl_pbl_physics (max_dom)
This option **should be used when your domain grid size is > 500m**.

### bldt (max_dom)
Minutes between boundary layer physics calls.
This should be set to the **same option for each domain**. **The typical/recommended value is 0 (every time step)**.

> **bldt (max_dom)                      = 0,       ! minutes between boundary-layer physics calls**

### cu_physics (max_dom)
- For a grid size >10 km, a cumulus scheme is necessary. 
- It is not necessary when the grid size is < 4km. 
- In **between 4km and 10km is a somewhat gray area**, so avoid this range if possible. 
    - If you must use this size, however, you can try the **GF and MSKF schemes (options 3 and 11), as these schemes are scale-aware**.

### cudt ?
Minutes between cumulus physics calls.
The**typical/recommended value is every time step**, as **most cumulus schemes are cheap to run**. Note: for the KF scheme this is also used for averaging time for vertical velocity trigger. This options is not used by the G3 or GD schemes.

> **cudt (max_dom)                      = 0,       ! minutes between cumulus physics calls**

### isfflx ?
Option that determines heat and moisture fluxes from the surface.
Use this option if you wish to turn off surface sensible and latent heat fluxes for some of the surface physics options. It may also be used to control how surface fluxes are used in idealized simulations (e.g., LES).

```sh
 isfflx                              = 1,	! heat and moisture fluxes from the surface
                                                  (only works for sf_sfclay_physics = 1,5,7,11)
                                                  1 = with fluxes from the surface 
                                                  0 = no flux from the surface
                                                      with bl_pbl_physics=0 this uses tke_drag_coefficient
                                                      and tke_heat_flux in vertical diffusion
                                                  2 = use drag from sf_sfclay_physics and heat flux from
                                                      tke_heat_flux with bl_pbl_physics=0
```

> **isfflx = 1,**

### ifsnow ?
This option can be turned off to remove snow effects for the simple soil model (sf_surface_physics = 1).

```sh
 ifsnow                              = 1,	! snow-cover effects
                                                  (only works for sf_surface_physics = 1)
                                                  1 = with snow-cover effect
                                                  0 = without snow-cover effect
```

> **ifsnow = 1,**

### icloud ?
Option to compute cloud fraction for radiation.

```sh
 icloud                              = 1,	! cloud effect to the optical depth in radiation
                                                  (only works for ra_sw_physics = 1,4 and ra_lw_physics = 1,4)
                                                  Since 3.6, this also controls the cloud fraction options
                                                  1 = with cloud effect, and use cloud fraction option 1
                                                      (Xu-Randall method) 
                                                  0 = without cloud effect
                                                  2 = with cloud effect, and use cloud fraction option 2 (0/1 based
                                                      on threshold
                                                  3 = with cloud effect, and use cloud fraction option 3, based on
                                                      Sundqvist et al. (1989) (since 3.7)
```

> **icloud = 1,**

or consider the icloud = 3 that may be better.

### surface_input_source
Where landuse and soil category data come from. Available options are:
```sh
 surface_input_source                = 3,	! where landuse and soil category data come from:
                                                  1 = WPS/geogrid but with dominant categories recomputed
                                                  2 = GRIB data from another model (only possible
                                                      (VEGCAT/SOILCAT are in met_em files from WPS)
                                                  3 = use dominant land and soil categories from WPS/geogrid (default since 3.8)
```

### num_land_cat
The number of land categories in the input static/geogrid data. This must match what's produced in WPS/geogrid. Available options are:
```sh
 num_land_cat                        = 21,      ! number of land categories in input data.
                                                  24 - for USGS (default); 20 for MODIS
                                                  28 - for USGS if including lake category
                                                  21 - for MODIS if including lake category (default since 3.8)
						                                      40 - for NCLD
```
and may be 5x for LCZ dataset (Urban).

### sf_urban_physics (max_dom)
This value **must be the same for each domain, and it must be set for each domain.**

## &dynamics

### w_damping ??
Vertical velocity damping flag. Options are 1=with damping; 0=without damping.
**For real-time/operational runs, this should always be turned on for model stability** (it is only activated when required.)
For budget studies, do not use this option.

```sh
 w_damping                           = 0,       ! vertical velocity damping flag (for operational use)
                                                  0 = without damping
                                                  1 = with    damping
```

### diff_opt (max_dom) ?
Turbulence and mixing option.

```sh
 diff_opt(max_dom)                   = 1,	! turbulence and mixing option:
                                                  0 = no turbulence or explicit
                                                      spatial numerical filters (km_opt IS IGNORED).
                                                  1 = evaluates 2nd order
                                                      diffusion term on coordinate surfaces.
                                                      uses kvdif for vertical diff unless PBL option
                                                      is used. may be used with km_opt = 1 and 4.
                                                      (= 1, recommended for real-data cases)
                                                  2 = evaluates mixing terms in
                                                      physical space (stress form) (x,y,z).
                                                      turbulence parameterization is chosen
                                                      by specifying km_opt.
```

> **diff_opt = 1, 1, 1,**

### km_opt (max_dom) ?
Eddy coefficient option.

```sh
 km_opt(max_dom)                     = 4,	! eddy coefficient option
                                                  1 = constant (use khdif kvdif)
                                                  2 = 1.5 order TKE closure (3D)
                                                  3 = Smagorinsky first order closure (3D)
                                                      Note: option 2 and 3 are not recommended for DX > 2 km
                                                  4 = horizontal Smagorinsky first order closure
                                                      (recommended for real-data cases)
                                                  5 = SMS-3DTKE scale-adaptive LES/PBL scheme. It must be
                                                      used with diff_opt = 2. PBL schemes must be turned off
                                                      (bl_pbl_physics=0). It can work with sf_sfclay_physics = 1, 5, 91.
```

> **km_opt = 4, 4, 4,**

### diff_6th_opt ?
6th order numerical diffusion option.

```sh
 diff_6th_opt (max_dom)              = 0,       ! 6th-order numerical diffusion
                                                  0 = no 6th-order diffusion (default)
                                                  1 = 6th-order numerical diffusion (not recommended)
                                                  2 = 6th-order numerical diffusion but prohibit up-gradient diffusion
```

> **diff_6th_opt = 0, 0, 0,**

### diff_6th_factor ?
6th order numerical diffusion non-dimensional rate.
The maximum value is 1.0, which corresponds to complete removal of 2dx wave in one timestep.

```sh
 diff_6th_factor (max_dom)           = 0.12,    ! 6th-order numerical diffusion non-dimensional rate (max value 1.0
                                                      corresponds to complete removal of 2dx wave in one timestep)
```

> **diff_6th_factor = 0.12, 0.12, 0.12,**

### base_temp ?
Base state sea-level (surface) temperature (in K).
This option can help to improve simulations when the model top is higher than 20 km (~ 50 mb). Note: This option is only available for real data, and em-only. This is a representative temperature at sea-level in the middle of your domain, regardless of the topography height at that point. **Typical values range from 270-300 K**. This value must stay the same through initialization, model runs, and restarts.

```sh
 base_temp                           = 290.,    ! real-data, em ONLY, base sea-level temp (K)
```

> **base_temp = 290.,**

### damp_opt ?
Upper-level damping flag.

```sh
 damp_opt                            = 0,	! upper level damping flag 
                                                  0 = without damping
                                                  1 = with diffusive damping, maybe used for real-data cases 
                                                      (dampcoef nondimensional ~0.01-0.1)
                                                  2 = with Rayleigh  damping (dampcoef inverse time scale [1/s] e.g. .003; idealized case only
                                                      not for real-data cases)
                                                  3 = with w-Rayleigh damping (dampcoef inverse time scale [1/s] e.g. .2; 
                                                      for real-data cases)
```

> **damp_opt = 0,**

### zdamp (max_dom) ?
Damping depth (in meters) from model top. **(default is 5000.)**

```sh
 zdamp (max_dom)                     = 5000.,	! damping depth (m) from model top
```

> **z_damp = 5000., 5000., 5000.,**

### dampcoef (max_dom) ??
Damping coefficient (also see damp_opt above)

```sh
dampcoef (max_dom)                  = 0.,	! damping coefficient (see above)
```

> **dampcoef = 0.2, 0.2, 0.2,**

### khdif (max_dom) ?
Horizontal diffusion constant (m^2/s)

```sh
khdif (max_dom)                     = 0,	; horizontal diffusion constant (m^2/s). A typical value should be 0.1*DX in meters.
```

### kvdif (max_dom) ?
Vertical diffusion constant (m^2/s)

```sh
 kvdif (max_dom)                     = 0,	! vertical diffusion constant (m^2/s). A typical value should be 100.
```

### non_hydrostatic (max_dom) ??
Whether running the model in hydrostatic or non-hydrotstatic (default) mode.

```sh
 non_hydrostatic (max_dom)           = .true.,	! whether running the model in hydrostatic or non-hydro mode
```

### moist_adv_opt (max_dom)
Positive-definite or monotonic advection to help eliminate over/under prediction of moisture. Option are:
- 0 = advection with no bounds or filters in place
- **1 = positive-definite advection of moisure (default)**
- 2 = monotonic option

### scalar_adv_opt (max_dom)
Positive-definite advection of scalars. Options are:
- 0 = advection with no bounds or filters in place
- **1 = positive-definite advection of moisure (default)**
- 2 = monotonic option

## &bdy_control

### spec_bdy_width
The number of rows for specified boundary value nudging. This value is the total of 'spec_zone' and 'relax_zone' (see below).
**A wider boundary zone may work better for coarser driving data**.

> **spec_bdy_width = 5,**

### spec_zone
The number of points in specified zone (specific boundary condition option).
Leave this **set to the default of 1**.

> **spec_zone = 1,**

### relax_zone
The number of points in relaxation zone (specific boundary condition option).
A **wider boundary zone may work better for coarser driving data**.

> **relax_zone = 4,**

### specified (max_dom)
Specified boundary conditions.

> The first row and column are specified with external model values (spec_zone = 1, which should not be changed). The rows and columns in relax_zone have values blended from an external model and WRF. The value of relax_zone may be changed, as long as spec_bdy_width = spec_zone + relax_zone. This can be used with periodic_x in tropical channel simulations.

**This can only be turned on (.true.) for domain 01 - must be false for all other domains. This is for real data cases only**.

> **specified = .true., .false., .false.,**

### nested (max_dom)
Nested boundary conditions.
This **must be set to .true. for nests.** This can be used for real or idealized cases.

> **nested = .false., .true., .true.,**









