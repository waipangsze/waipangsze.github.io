---
layout: post
title: WRF | MPAS | README.physics_files
categories: [WRF]
tags: [WRF, MPAS, HPC, NWP, MPI]
author: wpsze
date: 2025-01-24 09:56:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/9nI34Sb.png
banner_img: https://i.imgur.com/9nI34Sb.png
---


While browsing the WRF repository, I came across an introduction file in version 4.6.1, located at `WRF/run/README.physics_files`. This file provides a list of external files used by various physical schemes in the WRF model.

In this post, I'll give a brief overview for everyone to learn together.


When using certain schemes, we may wonder what data they rely on. The official documentation included a summary of the calling files in the **update for version 4.3** in **April 2021**, making it easier for everyone to reference. Subsequent minor versions have also included additional information,**with the latest details primarily pertaining to version 4.6.1**.

Initially, these files were located in the `run` directory and would then be linked to the `test/em_real` directory after compilation.

It's important to note that the files ending with "DBL" are only needed when using the real*8 double precision run mode.

You can check which fixed files are utilized in the parameterization schemes you commonly use.

- <https://github.com/wrf-model/WRF/blame/master/run/README.physics_files>

{% fold info @README.physics_files %}
```text
This is a list of all the physics options that require additional files.
Necessary files are listed below each option.
Those files must be linked to the directory where WRF is run.

Note: the files with DBL at the end are only used when running the model with real*8

----------------------------------------------------------------------------------
Microphysics

Eta/Ferrier (Option 5):
	ETAMPNEW_DATA
	ETAMPNEW_DATA_DBL
	ETAMPNEW_DATA.expanded_rain
	ETAMPNEW_DATA.expanded_rain_DBL

Thompson (Option 8):
	CCN_ACTIVATE.BIN

SBM (Options 30 and 32):
	bulkdens.asc_s_0_03_0_9
	bulkradii.asc_s_0_03_0_9
	capacity.asc
	coeff_p.asc
	coeff_q.asc
	constants.asc
	kernels.asc_s_0_03_0_9
	kernels_z.asc
	masses.asc
	termvels.asc

P3 (Options 50, 51, and 52)
	p3_lookupTable_1.dat-3momI_v5.1.7
	p3_lookupTable_1.dat-5.3-2momI
	p3_lookupTable_2.dat-2momI_v5.2.3

Jensen (Option 55):
	ishmael-gamma-tab.bin
	ishmael-qi-qc.bin
	ishmael-qi-qr.bin

----------------------------------------------------------------------------------
Surface

Slab 5-Layer (Option 1):
	LANDUSE.TBL

Noah (Option 2):
	LANDUSE.TBL
	GENPARM.TBL
	SOILPARM.TBL
	VEGPARM.TBL

RUC (Option 3):
	LANDUSE.TBL
	GENPARM.TBL
	SOILPARM.TBL
	VEGPARM.TBL

NoahMP (Option 4):
	LANDUSE.TBL
	GENPARTM.TBL
	MPTABLE.TBL
	SOILPARM.TBL

CLM (Option 5):
	LANDUSE.TBL
	CLM_ALB_ICE_DFS_DATA
	CLM_ALB_ICE_DRC_DATA
	CLM_ASM_ICE_DFS_DATA
	CLM_ASM_ICE_DRC_DATA
	CLM_DRDSDT0_DATA
	CLM_EXT_ICE_DFS_DATA
	CLM_EXT_ICE_DRC_DATA
	CLM_DRDSDT0_DATA
	CLM_EXT_ICE_DFS_DATA
	CLM_EXT_ICE_DRC_DATA
	CLM_KAPPA_DATA
	CLM_TAU_DATA

PX (Option 7):
	LANDUSE.TBL
	VEGPARM.TBL

SSiB (Option 8):
	LANDUSE.TBL

Wind-farm SFC Layer (windturbine_spec = 1)
	wind-turbine-1.tbl

----------------------------------------------------------------------------------
Radiation

RRTM (ra_lw_physics Option 1): 
	RRTM_DATA
	RRTM_DATA_DBL
        (Since V4.4, using these files becomes a runtime option. *.SSP245 is linked by default)
	        CAMtr_volume_mixing_ratio.A1B
        	CAMtr_volume_mixing_ratio.A2
        	CAMtr_volume_mixing_ratio.RCP4.5
        	CAMtr_volume_mixing_ratio.RCP6
        	CAMtr_volume_mixing_ratio.RCP8.5
        	CAMtr_volume_mixing_ratio.SSP119
        	CAMtr_volume_mixing_ratio.SSP126
        	CAMtr_volume_mixing_ratio.SSP245
        	CAMtr_volume_mixing_ratio.SSP370
        	CAMtr_volume_mixing_ratio.SSP585

CAM (Option 3):
        aerosol.formatted
        aerosol_lat.formatted
        aerosol_lon.formatted
        aerosol_plev.formatted
        CAM_ABS_DATA
        CAM_AEROPT_DATA
        (if o3input=2 - default)
        	ozone.formatted
        	ozone_lat.formatted
        	ozone_plev.formatted
        (Since V4.4, using these files becomes a runtime option. *.SSP245 is linked by default)
        	CAMtr_volume_mixing_ratio.A1B
        	CAMtr_volume_mixing_ratio.A2
        	CAMtr_volume_mixing_ratio.RCP4.5
        	CAMtr_volume_mixing_ratio.RCP6
        	CAMtr_volume_mixing_ratio.RCP8.5
        	CAMtr_volume_mixing_ratio.SSP119
        	CAMtr_volume_mixing_ratio.SSP126
        	CAMtr_volume_mixing_ratio.SSP245
        	CAMtr_volume_mixing_ratio.SSP370
        	CAMtr_volume_mixing_ratio.SSP585

RRTMG (Option 4,24,14):
	RRTMG_LW_DATA
	RRTMG_LW_DATA_DBL
	RRTMG_SW_DATA
	RRTMG_SW_DATA_DBL
        aerosol.formatted
        aerosol_lat.formatted
        aerosol_lon.formatted
        aerosol_plev.formatted
        (if o3input=2 - default)
        	ozone.formatted
        	ozone_lat.formatted
        	ozone_plev.formatted
        (Since V4.4, using these files becomes a runtime option. *.SSP245 is linked by default)
        	CAMtr_volume_mixing_ratio.A1B
        	CAMtr_volume_mixing_ratio.A2
        	CAMtr_volume_mixing_ratio.RCP4.5
        	CAMtr_volume_mixing_ratio.RCP6
        	CAMtr_volume_mixing_ratio.RCP8.5
        	CAMtr_volume_mixing_ratio.SSP119
        	CAMtr_volume_mixing_ratio.SSP126
        	CAMtr_volume_mixing_ratio.SSP245
        	CAMtr_volume_mixing_ratio.SSP370
        	CAMtr_volume_mixing_ratio.SSP585

Goddard (Option 5):
	BROADBAND_CLOUD_GODDARD.bin

GFDL (Option 99):
	co2_trans
	tr49t67
	tr49t85
	tr67t85

Eclipse option (ra_sw_eclipse = 1)
        eclipse_besselian_elements.dat

----------------------------------------------------------------------------------
Other Options:

Urban Physics, no LCZ (sf_urban_physics = 1, 2, & 3)
	URBPARM.TBL

Urban Physics (sf_urban_physics = 1, 2, and 3) with LCZ (use_wudapt_lcz = 1)
	URBPARM_LCZ.TBL

Chemistry (chem_opt = 112, 114, 111, 201, 202)
	HLC.TBL
```
{% endfold %}



