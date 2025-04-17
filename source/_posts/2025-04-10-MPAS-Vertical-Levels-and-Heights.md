---
layout: post
title: MPAS | Vertical Levels and Heights
categories: [MPAS]
tags: [WRF, MPAS, HPC, NWP, UCM]
author: wpsze
date: 2025-04-10 14:19:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/ueKU0IE.png
banner_img: https://i.imgur.com/ueKU0IE.png
---

[WRF | Vertical Levels and Heights](https://waipangsze.github.io/2025/01/27/WRF-Vertical-Levels-and-Heights/)

---

# MPAS's user guide

A description of the compressible nonhydrostatic atmospheric solver can be found in Skamarock et al, 2012. The fully compressible nonhydrostatic equations are cast in terms of a **geometric-height vertical coordinate**, and the solver makes use of a split-explicit time integration scheme that is described in Klemp et al, 2007. The time-integration scheme employs a 3rd-order Runge-Kutta method, and large time step, for the meteorologically significant modes and a forward-backward method with smaller time steps for the acoustic modes (See Wicker and Skamarock, 2002). 

## $\mathbf{C. 2}$ Vertical grid

The vertical coordinate in MPAS-Atmosphere is $\zeta$ and has units of length, where $0\leq\zeta\leq z_{t}$ and $z_{t}$ is the height of the model top. The relationship between the vertical coordinate and height in the physical domain is given as

$$z=\zeta+Ah_{s}(x,y,\zeta)$$

where $(x,y)$ denotes a location on the horizontal mesh and $\zeta$ is the vertical coordinate $(\zeta$ is directed radially outward from the surface of the sphere, or perpendicular to the horizontal $(x,y)$ plane in a Cartesian coordinate MPAS-A configuration). MPAS-A can be configured with the traditional Gal-Chen and Somerville terrain-following coordinate by setting $h_s(x,y,\zeta)=h(x,y)$ and $A=1-\zeta/z_t$, where $h(x,y)$ is the terrain height. Alternatively, A can be modified to allow a more rapid or less rapid transition to the constant-height upper boundary condition. Additionally, a constant-height coordinate can be specified at some intermediate ${\mathrm{height~below~}}h_{t}.$

The influence of the terrain on any coordinate surface $\zeta$ can be influenced by the specification of$h_s( x, y, \zeta ) .$ Specifically$, h_s$ can be set such that $h_s( x, y, 0) = h( x, y)$ ( ie. terrain following at the sur- face), and progressively flltered fields o features in the topography are quickly filtered from the coordinate. Example MPAS-A vertical meshes are given in Figure C.4. 

Further information about the vertical coordinate can be found in
Klemp, J. B. (2011). A Terrain-Following Coordinate with Smoothed Coordinate Surfaces. Mon.Wea Rev. , 139, 2163$-2169. doi:10.1175/MWR-D-10-05046.1

# Differences between WRF and MPAS

| WRF                                                        	| MPAS                                                               	|
|------------------------------------------------------------	|--------------------------------------------------------------------	|
| **Pressure-based** terrain-following sigma vertical coordinate 	| **Height-based** hybrid smoothed terrain-following vertical coordinate 	|

{% gi 4 1-3 %}
![](https://i.imgur.com/ueKU0IE.png)
![](https://i.imgur.com/SVeBD3M.png)
![](https://i.imgur.com/Wowl0HL.png)
{% endgi %}

# Literature Review

- [Skamarock, W. C., C. Snyder, J. B. Klemp, and S. Park, 2019: Vertical Resolution Requirements in Atmospheric Simulation. Mon. Wea. Rev., 147, 2641–2656, https://doi.org/10.1175/MWR-D-19-0043.1.](https://journals.ametsoc.org/view/journals/mwre/147/7/mwr-d-19-0043.1.xml)

# MPASv8.1.0

## Separate Stream for Invariant Fields

**By default, the MPAS-Atmosphere model reads time-invariant fields (e.g., latCell, lonCell, areaCell, $\mathbf{zgrid,zz,etc.)}$ from the“input”and“restart" streams (for cold-start and restart runs, respectively), and it writes time-invariant fields to the “restart” stream.** In the case of large ensembles, the time-invariant fields that are replicated in the restart files for all ensemble members can account for a substantial amount of storage. In principle, since these time-invariant fields do not change in time or across ensemble members, only one copy of these felds needs to be stored.
**MPAS-Atmosphere v8.1.0 introduces a capability to omit time-invariant fields from model restart files. When the model restarts, a new “invariant" stream may be used to read time-invariant fields from a separate file, and many ensemble members can share this file.**
In order to make use of the new“invariant" stream, several changes to the standard MPAS-Atmosphere
workflow are needed.

## Preparing an Invariant File

Through the use of the init atmosphere model program, a file containing all required time-invariant fields must be prepared. Of course, since the model initial conditions (typically referred to as the init.nc file) file contains time-invariant felds, the initial conditions file from any ensemble member may be used. If a file containing purely time-invariant fields is desired, the output from the **init\_atmosphere\_model** program after the following pre-processing stages have been run will suffice:

- **config\_static\_interp = true**
- **config\_native\_gwd\_static = true**
- **config-vertical-grid = true**

> **Note that the pre-processing stages do not need to be run all at once. It is possible, for example, to first produce a static.nc file using the first two of these pre-processing stages, and to then produce an invariant file (e.g., invariant.nc) by running the vertical grid generation stage using the static.nc file as input.**

##  Activating the Invariant Stream

When running the model itself (atmosphere model), the use of the new invariant stream may be activated by simply defining the "invariant" immutable stream in the streams.atmosphere file as follows:

```xml
<immutable_stream name="invariant"
                  type="input"
                  filename_template="invariant.nc"
                  input_interval="initial_only" />
```

In the definition of the **"invariant" stream**, the filename_template attribute should be set to the actual name of the invariant file.

**By the existence of the "invariant" stream in the streams.atmosphere file, the model will omit all time-invariant fields from any restart files that are written. When the model restarts, all time-invariant fields will be read from the "invariant" stream rather than from the "restart" stream.**

- **invariant.nc**

{% fold info @namelist.init_atmosphere %}
```namelist.init_atmosphere
&nhyd_model
    config_init_case = 7
/

&dimensions
    config_nvertlevels = 55
    config_nsoillevels = 1
    config_nfglevels = 1
    config_nfgsoillevels = 1
/
&data_sources
	config_geog_data_path = '/EM/emsdm/data/geog'
    config_landuse_data = 'MODIFIED_IGBP_MODIS_NOAH'
/

&vertical_grid
    config_ztop = 30000.0
    config_nsmterrain = 1
    config_smooth_surfaces = true
    config_dzmin = 0.3
    config_nsm = 30
    config_tc_vertical_grid = true
    config_blend_bdy_terrain = false
    config_specified_zeta_levels = 'z166m_zeta_levels.txt' !--'default_zeta_levels.txt'
/

&interpolation_control
    config_extrap_airtemp = 'linear'
/

&preproc_stages
    config_static_interp = false
    config_native_gwd_static = false
    config_vertical_grid = true
    config_met_interp = false
    config_input_sst = false
    config_frac_seaice = false
/

&io
    config_pio_num_iotasks = 0
    config_pio_stride = 1
/

&decomposition
   config_block_decomp_file_prefix = 'graph.info.part.'
/
```
{% endfold %}

- **restart.nc**
  - atm/streams.atmosphere
  - setup the restart interval

{% fold info @streams.atmosphere %}
```xml
<streams>
<immutable_stream name="input"
                  type="input"
                  io_type="pnetcdf,cdf5"
                  filename_template="MPAS-120km-MPASv822_2022063000.init.nc"
                  input_interval="initial_only" />

<immutable_stream name="restart"
                  type="input;output"
                  io_type="pnetcdf,cdf5"
                  filename_template="restart.$Y-$M-$D_$h.$m.$s.nc"
                  input_interval="initial_only"
                  output_interval="01:00:00" />

<immutable_stream name="invariant"
					type="input"
					filename_template="invariant.nc"
					io_type="pnetcdf,cdf5" 
					input_interval="initial_only" />

<immutable_stream name="lbc_in"
                  type="input"
                  io_type="netcdf4"
                  filename_template="lbc.$Y-$M-$D_$h.$m.$s.nc"
                  filename_interval="input_interval"
                  packages="limited_area"
                  input_interval="none" />

<stream name="surface"
        type="input"
        io_type="netcdf4"
        filename_template="sst.$Y-$M-$D_$h.$m.$s.nc"
        input_interval="none">
        <file name="stream_list.atmosphere.surface"/>
</stream>

<stream name="mesh"
        type="output"
        filename_template="mesh.nc"
        output_interval="initial_only"
        io_type="pnetcdf,cdf5"
        clobber_mode="replace_files" >
        <file name="stream_list.atmosphere.mesh"/>
</stream>

<stream name="output"
        type="output"
        filename_template="output.$Y-$M-$D_$h.$m.$s.nc"
        output_interval="none"
        io_type="pnetcdf,cdf5"
        clobber_mode="replace_files" >
        <file name="stream_list.atmosphere.output"/>
</stream>

<stream name="diagnostics"
        type="output"
        io_type="pnetcdf,cdf5"
        filename_template="diag.$Y-$M-$D_$h.$m.$s.nc"
        output_interval="01:00:00"
        clobber_mode="replace_files" >
        <file name="stream_list.atmosphere.diagnostics"/>
</stream>

</streams>
```
{% endfold %}


```console
- MPAS-120km

/static/
    78M static.nc
/invariant/
    230M invariant.nc
/init/
    348M init.nc (still includes zgrid...)
/atm/
    798M default_restart.nc
    578M restart.nc
```