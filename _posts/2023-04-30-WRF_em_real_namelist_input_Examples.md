---
layout: post
title: "WRF em_real namelist.input Examples"
categories: [WRF]
tags: [WRF,namelist.input]
author: wpsze
---

* content
{:toc}

# WRF em_real namelist.input Examples

For em_real

## namelist.input.default

The default namelist.input file for real cases had several settings that go against best practices and recommendations.

[WRF/test/em_real/namelist.input.default](https://github.com/wrf-model/WRF/blob/master/test/em_real/namelist.input.default)
```sh
 &time_control
 run_days                            = 0,			! Number of simulation days. DO NOT ADD ADDITIONAL COLUMNS.
 run_hours                           = 36,			! Number of simulation hours. DO NOT ADD ADDITIONAL COLUMNS.
 run_minutes                         = 0,			! Number of simulation minutes. DO NOT ADD ADDITIONAL COLUMNS.
 run_seconds                         = 0,			! Number of simulation seconds. DO NOT ADD ADDITIONAL COLUMNS.
								! *NOTE: real.exe does not read run_days/hours/minutes/seconds. Only read by wrf.exe
									! real.exe only reads start_* and end_* parameters.
 start_year                          = 2019, 2019, 2019,	! Starting year of the simulation.
 start_month                         = 09,   09,   09,		! Starting month of the simulation.
 start_day                           = 04,   04,   24,		! Starting day of the simulation.
 start_hour                          = 12,   12,   12,		! Starting hour of the simulation.
 end_year                            = 2019, 2019, 2019,	! Ending year of the simulation.
 end_month                           = 09,   09,   09,		! Ending month of the simulation.
 end_day                             = 06,   06,   06,		! Ending day of the simulation.
 end_hour                            = 00,   00,   00,		! Ending hour of the simulation.
 interval_seconds                    = 21600			! Time interval (s) between input data. DO NOT ADD ADDITIONAL COLUMNS.
 input_from_file                     = .true.,.true.,.true.,	! Logical - whether input files (e.g., met_em*) will be used.
 history_interval                    = 180,  60,   60,		! Interval between history output (mins). *Note: to change the units of history_interval, add _s (secs), _h (hours)
 frames_per_outfile                  = 1000, 1000, 1000,	! Number of history output times printed to each wrf output (wrfout) file.
 restart                             = .false.,			! Logical - whether the run is a "restart" simulation. DO NOT ADD ADDITIONAL COLUMNS.
 restart_interval                    = 7200,			! Interval between restart (wrfrst) files (min). DO NOT ADD ADDITIONAL COLUMNS.
 io_form_history                     = 2			! File format for history (wrfout) files. See Users' Guide for options. DO NOT ADD ADDITIONAL COLUMNS.
 io_form_restart                     = 2			! File format for restart (wrfrst) files. See Users' Guide for options. DO NOT ADD ADDITIONAL COLUMNS.
 io_form_input                       = 2			! File format for the input (wrfinput) files. See Users' Guide for options. DO NOT ADD ADDITIONAL COLUMNS.
 io_form_boundary                    = 2			! File format for the boundary (wrfbdy) file. See Users' Guide for options. DO NOT ADD ADDITIONAL COLUMNS.
 /

 &domains
 time_step                           = 90,			! Time step for model integration (s). Must use 6xDX (or less). DO NOT ADD ADDITIONAL COLUMNS.
 time_step_fract_num                 = 0,			! Numerator if using fractional time step. DO NOT ADD ADDITIONAL COLUMNS.
 time_step_fract_den                 = 1,			! Denomenator if using fractional time step. DO NOT ADD ADDITIONAL COLUMNS.
 max_dom                             = 1,			! Total number of domains for the simulation. DO NOT ADD ADDITIONAL COLUMNS.
 e_we                                = 150,    220,   200,	! Number of grid spaces in the x direction (west-east).
 e_sn                                = 130,    214,   210,	! Number of grid spaces in the y direction (south-north).
 e_vert                              = 45,     45,    45,	! Number of full vertical levels. must be the same for all domains.
 dzstretch_s                         = 1.1			! Surface stretch factor when auto_levels_opt = 2 (which is default). DO NOT ADD ADDITIONAL COLUMNS.
 p_top_requested                     = 5000,			! Pressure top (in Pa) to use for the simulation. The input data must go up to this value. DO NOT ADD ADDITIONAL COLUMNS.
 num_metgrid_levels                  = 34,			! Number of vertical levels in the WPS output. DO NOT ADD ADDITIONAL COLUMNS.
 num_metgrid_soil_levels             = 4,			! Number of soil levels in the WPS output. DO NOT ADD ADDITIONAL COLUMNS.
 dx                                  = 15000,			! Length (m) of each grid in the x (west-east) direction (resolution). DO NOT ADD ADDITIONAL COLUMNS. 
 dy                                  = 15000,			! Length (m) of each grid in the y (south-north) direction (resolution). DO NOT ADD ADDITIONAL COLUMNS.  
 grid_id                             = 1,     2,     3,		! Identifier number of each domain.
 parent_id                           = 0,     1,     2,		! The ID of the parent of each domain.
 i_parent_start                      = 1,     53,    30,	! Starting location of the lower left corner i-indice within the parent domain.
 j_parent_start                      = 1,     25,    30,	! Starting location of the lower left corner j-indice within the parent domain.
 parent_grid_ratio                   = 1,     3,     3,		! Ratio of the parent to nest grid size (resolution).
 parent_time_step_ratio              = 1,     3,     3,		! Time_step ratio of the parent to nest.
 feedback                            = 1,			! Switch for feedback from the nest to it's parent at corresponding grid points (1=on;0=off). DO NOT ADD ADDITIONAL COLUMNS.
 smooth_option                       = 0			! Smoothing option for the boundary between parent and nest domains (when feedback=1). DO NOT ADD ADDITIONAL COLUMNS.
 /

 &physics
 physics_suite                       = 'CONUS'			! Physics suite option (CONUS or tropical). See Users' Guide for details. DO NOT ADD ADDITIONAL COLUMNS.
 mp_physics                          = -1,    -1,    -1,	! Microphysics scheme. See Users' Guide for options (-1=option from suite). Every domain must be the same.
 cu_physics                          = -1,    -1,     0,	! Cumulus scheme. See Users' Guide for options (-1=option from suite).
 ra_lw_physics                       = -1,    -1,    -1,	! Longwave radiation scheme. See Users' Guide for options (-1=option from suite). Every domain must be the same.
 ra_sw_physics                       = -1,    -1,    -1,	! Shortwave radiation scheme. See Users' Guide for options (-1=option from suite). Every domain must be the same.
 bl_pbl_physics                      = -1,    -1,    -1,	! PBL scheme. See Users' Guide for options (-1=option from suite).
 sf_sfclay_physics                   = -1,    -1,    -1,	! Surface layer scheme. See Users' Guide for options (-1=option from suite). Every domain must be the same.
 sf_surface_physics                  = -1,    -1,    -1,	! LSM scheme. See Users' Guide for options (-1=option from suite). Every domain must be the same.
 radt                                = 15,    15,    15,	! Minutes between radiation physics calls (recommended 1 min per km of d01 dx). Every domain must be the same.
 bldt                                = 0,     0,     0,		! Minutes between boundary-layer physics calls (recommended 0=every time step). Every domain must be the same.
 cudt                                = 0,     0,     0,		! Minutes between cumulus physics calls. Set to 0 (called every time step) for all cumulus schemes except Kain-Fritsch.  Every domain must be the same.
 icloud                              = 1,			! Option to couple subgrid-scale clouds from the PBL scheme to radiation schemes (only works with RRTM/RRTMG rad schemes). See Users' Guide for options. DO NOT ADD ADDITIONAL COLUMNS.
 num_land_cat                        = 21,			! Number of land categories from WPS input data. See Users' Guide for details. DO NOT ADD ADDITIONAL COLUMNS.
 sf_urban_physics                    = 0,     0,     0,		! Option for activating urban canopy model (only for Noah LSM). See Users' Guide for details. Use same value for all domains. 
 /

 &fdda
 /

 &dynamics
 hybrid_opt                          = 2,			! Hybrid coordinate option (2=Klemp cubic form with etac; default beginning V4.0). DO NOT ADD ADDITIONAL COLUMNS. 
 w_damping                           = 0,			! Vertical velocity damping flag (0=off;1=on). DO NOT ADD ADDITIONAL COLUMNS.
 diff_opt                            = 2,      2,      2,	! Turbulence and mixing option. See Users' Guide for options. 
 km_opt                              = 4,      4,      4,	! Eddy coefficient option. See Users' Guide for options.
 diff_6th_opt                        = 0,      0,      0,	! 6th order numerical diffusion option. See Users' Guide for options.
 diff_6th_factor                     = 0.12,   0.12,   0.12,	! 6th order numerical diffusion non-dimensional rate (max 1.0 = complete removal of 2dx wave in 1 timestep). 
 base_temp                           = 290.			! Base state temp (K). For real cases only. DO NOT ADD ADDITIONAL COLUMNS.
 damp_opt                            = 3,			! Upper-level damping option. See Users' Guide for options. DO NOT ADD ADDITIONAL COLUMNS.
 zdamp                               = 5000.,  5000.,  5000.,	! Damping depth (m) from model top.
 dampcoef                            = 0.2,    0.2,    0.2,	! Damping coefficient corresponding to damp_opt. See Users' Guide for details.
 khdif                               = 0,      0,      0,	! Horizontal diffusion constant (m^2/s)	
 kvdif                               = 0,      0,      0,	! Vertical diffusion constant (m^2/s)
 non_hydrostatic                     = .true., .true., .true.,	! Set to false to run the model hydrostatically.
 moist_adv_opt                       = 1,      1,      1,     	! Advection option for moisture. See Users' Guide for details.
 scalar_adv_opt                      = 1,      1,      1,     	! Advection option for scalars. See Users' Guide for details.
 gwd_opt                             = 1,      1,      0,	! Switch for gravity wave drag option (1=on;0=off)
 /

 &bdy_control
 spec_bdy_width                      = 5,			! Total number of rows for specified boundary value nudging for real cases only. DO NOT ADD ADDITIONAL COLUMNS.
 specified                           = .true.			! Specified boundary condition for real cases only. DO NOT ADD ADDITIONAL COLUMNS.
 /

 &grib2
 /

 &namelist_quilt
 nio_tasks_per_group = 0,					! Number of I/O tasks per group for quilting. 0=no quilting. DO NOT ADD ADDITIONAL COLUMNS.
 nio_groups = 1,						! Number of I/O groups for quilting. DO NOT ADD ADDITIONAL COLUMNS.
 /
```

## examples.namelist (Many cases)
[Git em_real/examples.namelist](https://github.com/wrf-model/WRF/blob/master/test/em_real/examples.namelist)

> Note, this is not a namelist.input file. **Find what interests you, and cut and paste them 
      to your own namelist.input file.** For more information on these namelist parameters,
      please see run/README.namelist or Chapter 5 of the User's Guide.

```sh
Note, this is not a namelist.input file. Find what interests you, and cut and paste them 
      to your own namelist.input file. For more information on these namelist parameters,
      please see run/README.namelist or Chapter 5 of the User's Guide.

** More options for real in namelist record &domains:

 p_top_requested                     = 5000
 interp_type                         = 2
 extrap_type                         = 2
 t_extrap_type                       = 2
 lowest_lev_from_sfc                 = .false.
 use_levels_below_ground             = .true.
 use_surface                         = .true.
 lagrange_order                      = 2
 force_sfc_in_vinterp                = 1
 zap_close_levels                    = 500
 sfcp_to_sfcp                        = .false.
 adjust_heights                      = .false.
 smooth_cg_topo                      = .false.
 eta_levels                          = 1.000, 0.990, 0.978, 0.964, 0.946,
                                       0.922, 0.894, 0.860, 0.817, 0.766,
                                       0.707, 0.644, 0.576, 0.507, 0.444,
                                       0.380, 0.324, 0.273, 0.228, 0.188,
                                       0.152, 0.121, 0.093, 0.069, 0.048,
                                       0.029, 0.014, 0.000,

** Using sst_update option (add these to namelist records &time_control and
   &physics respectively):

 &time_control
 auxinput4_inname                    = "wrflowinp_d<domain>"
 auxinput4_interval                  = 360, 360, 360, 
 io_form_auxinput4                   = 2

 &physics
 sst_update                          = 1,

** Using qna_update option (add these to namelist records &time_control and
   &physics respectively):

 &time_control
 auxinput17_inname                   = "wrfqnainp_d<domain>"
 auxinput17_interval                 = 360, 360, 360,
 io_form_auxinput17                  = 2

 &physics
 qna_update                          = 1,

** Using Noah-MP option (Use sf_surface_physics option = 4, and add
   &noah_mp namelist record)

&physics
   sf_surface_physics = 4

&noah_mp
 dveg     = 4,
 opt_crs  = 1,
 opt_btr  = 1,
 opt_run  = 1,
 opt_sfc  = 1, 
 opt_frz  = 1, 
 opt_inf  = 1, 
 opt_rad  = 3, 
 opt_alb  = 2,
 opt_snf  = 1,
 opt_tbot = 2, 
 opt_stc  = 1,
/

** Using UCM urban model
&physics
 sf_urban_physics                    = 1,      1,     1,

** Using BEP urban model
&physics
 sf_urban_physics                    = 2,      2,     2,

** Using BEM urban model
&physics
 sf_urban_physics                    = 3,      3,     3,

** Using lake model
&physics
 sf_lake_physics                     = 1,      1,     1,
 lakedepth_default                   = 50.,    50.,   50.,
 lake_min_elev                       = 5.,     5.,    5.,

** Using shallow water roughness (constant depth)
&physics
shalwater_z0                        = 1,       1,     1, 
shalwater_depth                     = 40.0,

** Using shallow water roughness (with bathymetry data)
&physics
shalwater_z0                        = 1,       1,     1, 
shalwater_depth                     = 0.0,

** Using stochastic backscatter scheme (new namelist record since v3.6)

&stoch
 stoch_force_opt                     = 1,        1,        1,
 stoch_vertstruc_opt                 = 0,        0,        0,
 tot_backscat_psi                    = 1.E-05,   1.E-05,   1.E-05,
 tot_backscat_t                      = 1.E-06,   1.E-06,   1.E-06,
 nens                                = 1,
 ztau_psi                            = 10800.0,
 ztau_t                              = 10800.0,
 rexponent_psi                       =-1.83,
 rexponent_t                         =-1.83,
 zsigma2_eps                         = 0.0833,
 zsigma2_eta                         = 0.0833,
 kminforc                            = 1,
 kminforc                            = 1,
 kminforct                           = 1,
 lminforct                           = 1,
 kmaxforc                            = 1000000,
 lmaxforc                            = 1000000,
 kmaxforct                           = 1000000,
 lmaxforct                           = 1000000,
 perturb_bdy                         = 0,


** Using DFI options (note this is a separate namelist record):

 &dfi_control
 dfi_opt                             = 3,
 dfi_nfilter                         = 7,
 dfi_cutoff_seconds                  = 3600,
 dfi_write_filtered_input            = .true.
 dfi_write_dfi_history               = .false.
 dfi_bckstop_year                    = 2000,
 dfi_bckstop_month                   = 01,
 dfi_bckstop_day                     = 24,
 dfi_bckstop_hour                    = 10,
 dfi_bckstop_minute                  = 00,
 dfi_bckstop_second                  = 00,
 dfi_fwdstop_year                    = 2000,
 dfi_fwdstop_month                   = 01,
 dfi_fwdstop_day                     = 24,
 dfi_fwdstop_hour                    = 13,
 dfi_fwdstop_minute                  = 00,
 dfi_fwdstop_second                  = 00,
 /

 &domains
 time_step_dfi                       = 60

** Using gridded nudging option (note this is a separate namelist record) for
   upperair nudging

** Upper air gridded nudging requires an file generated by the real
   program.  Activating grid_fdda for the real program is adequate.

 &fdda
 grid_fdda                           = 1,     1,     1,
 gfdda_inname                        = "wrffdda_d<domain>",
 gfdda_end_h                         = 24,    24,    24,
 gfdda_interval_m                    = 360,   360,   360,
 fgdt                                = 0,     0,     0,
 if_no_pbl_nudging_uv                = 0,     0,     0,
 if_no_pbl_nudging_t                 = 1,     1,     1,
 if_no_pbl_nudging_q                 = 1,     1,     1,
 if_zfac_uv                          = 0,     0,     0,
  k_zfac_uv                          = 10,   10,    10,
 if_zfac_t                           = 0,     0,     0,
  k_zfac_t                           = 10,   10,    10,
 if_zfac_q                           = 0,     0,     0,
  k_zfac_q                           = 10,   10,    10,
 guv                                 = 0.0003,     0.0003,     0.0003,
 gt                                  = 0.0003,     0.0003,     0.0003,
 gq                                  = 0.0003,     0.0003,     0.0003,
 if_ramping                          = 1,
 dtramp_min                          = 60.0,
 io_form_gfdda                       = 2,

** Using gridded surface nudging option (note this is a separate namelist 
   record)

** Note that upper-air and surface gridded nudging may be used together 
   or separately.  Surface nudging requires an input file generated by the
   obsgrid program.

 &fdda
 grid_sfdda                          = 1,     1,     1,
 sgfdda_inname                       = "wrfsfdda_d<domain>",
 sgfdda_end_h                        = 24,    24,    24,
 sgfdda_interval_m                   = 360,   360,   360,
 io_form_sgfdda                      = 2,
 guv_sfc                             = 0.0003,     0.0003,     0.0003,
 gt_sfc                              = 0.0003,     0.0003,     0.0003,
 gq_sfc                              = 0.0003,     0.0003,     0.0003,
 rinblw                              = 250.,       250.,       250., 

** Using observation nudging option (note &fdda is a separate namelist record):

 &time_control
 auxinput11_interval_s               = 180 , 180 , 180
 auxinput11_end_h                    = 6   , 6   , 6

 &fdda
 obs_nudge_opt                       = 1,1,1,
 max_obs                             = 150000,
 fdda_start                          = 0.,  0.,  0.,
 fdda_end                            = 720.,720.,720.,
 obs_nudge_wind                      = 1,1,1,
 obs_coef_wind                       = 6.E-4,6.E-4,6.E-4,
 obs_nudge_temp                      = 1,1,1,
 obs_coef_temp                       = 6.E-4,6.E-4,6.E-4,
 obs_nudge_mois                      = 1,1,1,
 obs_coef_mois                       = 6.E-4,6.E-4,6.E-4,
 obs_rinxy                           = 240.,240.,180.,
 obs_rinsig                          = 0.1,
 obs_twindo                          = 0.6666667,0.6666667,0.6666667,
 obs_npfi                            = 10,
 obs_ionf                            = 2, 2, 2,
 obs_idynin                          = 0,
 obs_dtramp                          = 40.,
 obs_prt_freq                        = 10, 10, 10,
 obs_prt_max                         = 10
 obs_ipf_errob                       = .true.
 obs_ipf_nudob                       = .true.
 obs_ipf_in4dob                      = .true
 obs_no_pbl_nudge_uv                 = 0
 obs_no_pbl_nudge_t                  = 0
 obs_no_pbl_nudge_q                  = 0
 obs_sfc_scheme_horiz                = 0
 obs_sfc_scheme_vert                 = 0
 obs_max_sndng_gap                   = 20
 obs_nudgezfullr1_uv                 = 50
 obs_nudgezrampr1_uv                 = 50
 obs_nudgezfullr2_uv                 = 50
 obs_nudgezrampr2_uv                 = 50
 obs_nudgezfullr4_uv                 = -5000
 obs_nudgezrampr4_uv                 = 50  
 obs_nudgezfullr1_t                  = 50 
 obs_nudgezrampr1_t                  = 50
 obs_nudgezfullr2_t                  = 50
 obs_nudgezrampr2_t                  = 50
 obs_nudgezfullr4_t                  = -5000
 obs_nudgezrampr4_t                  = 50  
 obs_nudgezfullr1_q                  = 50 
 obs_nudgezrampr1_q                  = 50
 obs_nudgezfullr2_q                  = 50
 obs_nudgezrampr2_q                  = 50
 obs_nudgezfullr4_q                  = -5000
 obs_nudgezrampr4_q                  = 50
 obs_nudgezfullmin                   = 50
 obs_nudgezrampmin                   = 50
 obs_nudgezmax                       = 3000
 obs_sfcfact                         = 1.0
 obs_sfcfacr                         = 1.0
 obs_dpsmx                           = 7.5
 /

** Using spectral nudging option 

 &fdda
 grid_fdda                           = 2,     2,     2,
 gfdda_inname                        = "wrffdda_d<domain>",
 gfdda_end_h                         = 24,    24,    24,
 gfdda_interval_m                    = 360,   360,   360,
 fgdt                                = 0,     0,     0,
 fgdtzero                            = 0,     0,     0,
 if_no_pbl_nudging_uv                = 0,     0,     0,
 if_no_pbl_nudging_t                 = 0,     0,     0,
 if_no_pbl_nudging_ph                = 0,     0,     0,
 if_zfac_uv                          = 0,     0,     0,
  k_zfac_uv                          = 10,   10,    10,
 if_zfac_t                           = 0,     0,     0,
  k_zfac_t                           = 10,   10,    10,
 if_zfac_ph                          = 0,     0,     0,
  k_zfac_ph                          = 10,   10,    10,
 dk_zfac_uv                          = 1,     1,     1,
 dk_zfac_t                           = 1,     1,     1,
 dk_zfac_ph                          = 1,     1,     1,
 guv                                 = 0.0003,     0.0003,     0.0003,
 gt                                  = 0.0003,     0.0003,     0.0003,
 gph                                 = 0.0003,     0.0003,     0.0003,
 xwavenum                            = 3,
 ywavenum                            = 3,
 if_ramping                          = 1,
 dtramp_min                          = 60.0,
 io_form_gfdda                       = 2,

** Using adaptive time step option (add these in namelist record &domains):

 use_adaptive_time_step              = .true.,
 step_to_output_time                 = .true.,
 target_cfl                          = 1.2, 1.2, 1.2,
 target_hcfl                         = .84, .84, .84,
 max_step_increase_pct               = 5,   51,  51,
 starting_time_step                  = -1,  -1,  -1,
 max_time_step                       = 360, 120, 40,
 min_time_step                       =  90,  30, 10,
 adaptation_domain                   =  1,

** Using automatic vortex-following option (tropical storm tracking only;
   add these in namelist record &domains):

 vortex_interval                     = 15, 15, 15,
 max_vortex_speed                    = 40, 40, 40,
 corral_dist                         =  8, 15, 15,
 track_level                         = 50000,

** Miscellaneous physics options for namelist record &physics:

Topographic shading (only effective when grid sizes are a few kilometers)

 slope_rad                           = 1,     1,     1,
 topo_shading                        = 1,     1,     1,
 shadlen                             = 25000, 

Setting threshold value for defining seaice if seaice is not in the input file:

 seaice_threshold                    = 271,

Switching off latent heating from a microphysics scheme (must also set cu_physics = 0):

 no_mp_heating                       = 0,

** Using precipiatiion bucket in a time interval (minutes):

 prec_acc_dt                         = 60.

** Using bucket accumulations for multi-year simulations (guideline: mean monthly accumulation)

 bucket_mm                           = 100.
 bucket_J                            = 1.e9

** Optional gravitational settling of fog/cloud droplets (MYNN PBL only)

 grav_settling                       = 1,       ; default 0

** Using implicit gravity-wave damping option (add these in namelist record &dynamics):

 damp_opt                            = 3,
 zdamp                               = 5000.,  5000.,  5000.,
 dampcoef                            = 0.2,    0.2,    0.2

** Using expanded boundary zone and exponential decay option (add or modify these in
    namelist record &bdy_control). 
    spec_zone is ALWAYS = 1 
    relax_zone is ALWAYS = spec_bdy_width - spec_zone

&bdy_control
 spec_bdy_width                      = 10,
 specified                           = .true.,
 spec_exp                            = 0.33

For a tropical channel configuration, set the following:

 specified                           = .true.,
 periodic_x                          = .true.,

** Using split lateral boundary files
     The run-time flag multi_bdy_files must be set to TRUE (default is false), and the
     lateral boundary files must have a date associated. When using the split LBC option,
     there is ALWAYS and ONLY a single time LBC time in each file.

 multi_bdy_files                     = .true.

Also requires
&time_control
 bdy_inname                          = "wrfbdy_d<domain>_<date>"

** using io quilting option to improve output efficiency for large domain runs
    (note that this is a separate namelist record):

 &namelist_quilt
 nio_tasks_per_group = 2,
 nio_groups = 1,

** for tc bogusing:
   Helpful hints
   1) The TC bogus *MUST* only be run with a single processor: serial 
      (with or without a nest build option), or as a DM build with np=1.
   2) Run the TC program for only the initial model time. Remember to copy 
      your metgrid file to a safe location and then let the TC scheme 
      generate the auxiliary files.
   3) The TC program does not handle soil moisture or soil temperature 
      correctly. After running the TC program, use an NCL script to put the 
      modified fields back into the original metgrid file (remember that you
      are keeping a pristine copy elsewhere). The modified fields to copy
      are:  "RH", "TT", "UU", "VV", "GHT", "PRES", "PMSL", "PSFC".
   4) The TC program runs quickly, only a few seconds. It is a good idea to 
      process multiple runs, and vary the TC initialization. It would be 
      reasonable to select the maximum wind speed (vmax_meters_per_second) 
      from 30 to 60 by 5, select the radius of maximum winds ((m), rmax) = 
      50000 to 200000 by 50000, and select a tuning parameter for the 
      vortex (vmax_ratio) = 0.5 to 0.9 by 0.1. Look at the resulting 
      bogus storms (sea level pressure, surface wind speeds, and 
      overall storm size), and choose the best.  
   5) Remember to also consider starting the TC program at a later or
      earlier time during your parameter space exploration.
   6) The bogus storm will organize better if the initialization of the
      tropical storm is entirely over water, and if the storm is able to 
      develop over water for several days.
   7) After the metgrid file is finalized, and the real program is run,
      use DFI in WRF. Removing and then introducing an entire bogus typhoon
      is the definition of unbalanced. Running DFI for 2-3 hours back and forth
      will be a benefit. For a successful implementation of a TC bogus storm in 
      WRF, the storm should not oscillate in size, should not be radiating 
      massive amounts of gravity waves, and should not rapidly weaken.

 &tc
 insert_bogus_storm                  = .true.,
 remove_storm                        = .false.,
 num_storm                           = 1,
 latc_loc                            = 15.,
 lonc_loc                            = -90.,
 vmax_meters_per_second              = 30,
 rmax                                = 50000,
 vmax_ratio                          = 0.5,
 /

** for regional climate surface diagnostics such as max/min/mean/std of T2/Q2/wind/rainfall
between selected output times (e.g. daily) in auxhist3

 &time_control
 output_diagnostics      = 1
 auxhist3_outname        = 'wrfxtrm_d<domain>_<date>'
 io_form_auxhist3        = 2
 auxhist3_interval       = 1440
 frames_per_auxhist3     = 1
 /


** for pressure-level (and some surface) diagnostics, output is on stream 23,
where the listed pressure levels are in Pa.  For the height level interpolation,
the unit is stream 22.

 &time_control
 io_form_auxhist23                   =  2,
 auxhist23_interval                  = 30,   30,    30,
 frames_per_auxhist23                =  1,    1,     1, 
 auxhist23_outname                   = "PLEVS_d<domain>_<date>"
 /

 &diags
 p_lev_diags                         = 1
 num_press_levels                    = 4
 press_levels                        = 85000, 70000, 50000, 25000
 use_tot_or_hyd_p                    = 2
 /


** for height-level diagnostics, output is on stream 22 (negative values
for z_levels means AGL, so -500 is 500 m AGL, and 10000 is 10 km).

 &time_control
 io_form_auxhist22                   =  2,
 auxhist22_interval                  = 30,   30,    30,
 frames_per_auxhist22                =  1,    1,     1, 
 auxhist22_outname                   = "ZLEVS_d<domain>_<date>"
 /

 &diags
 z_lev_diags                         = 1
 num_z_levels                        = 2
 z_levels                            = -500, 10000
 /


** For solar diagnostics, 2-D fields of variables relevant to solar forecasting are output 
when option solar_diagnostics is activated in diags section of namelist, as shown below.
Also, if tslist is present when solar_diagnostics is activated, then these same variables
are output to the time series files for the location(s) specified in tslist.
All variables are calculated in phys/module_diag_solar.F and defined in registry.solar_fields

 &diags
 solar_diagnostics                   =  1
 /


** Using different flux formulation for tropical storm simulations
   (best for grid spacing less than 2 km) 
   simple 1-D ocean mixed layer, or University of Miami 3DPWP ocean model
   (add these in namelist record &physics):

 isftcflx                            = 1,
 sf_ocean_physics                    = 0 (off), 1 (OML), 2 (3D PWP)
 oml_hml0                            = 50,
 oml_gamma                           = 0.14
 omdt                                = 1

 Add these in the &domains section.  Note that the example here is for
 a warm ocean: ocean_z is the depth of each layer (m), from the surface
 downward; the associated temperature is ocean_t (K); and the associated 
 salinity is ocean_s (ppt).

 ocean_levels                        = 30,
 ocean_z                             =       5.,       15.,       25.,       35.,       45.,       55.,
                                            65.,       75.,       85.,       95.,      105.,      115.,
                                           125.,      135.,      145.,      155.,      165.,      175.,
                                           185.,      195.,      210.,      230.,      250.,      270.,
                                           290.,      310.,      330.,      350.,      370.,      390.
 ocean_t                             = 302.3493,  302.3493,  302.3493,  302.1055,  301.9763,  301.6818,
                                       301.2220,  300.7531,  300.1200,  299.4778,  298.7443,  297.9194,
                                       297.0883,  296.1443,  295.1941,  294.1979,  293.1558,  292.1136,
                                       291.0714,  290.0293,  288.7377,  287.1967,  285.6557,  284.8503,
                                       284.0450,  283.4316,  283.0102,  282.5888,  282.1674,  281.7461
 ocean_s                             =  34.0127,   34.0127,   34.0127,   34.3217,   34.2624,   34.2632,
                                        34.3240,   34.3824,   34.3980,   34.4113,   34.4220,   34.4303,
                                        34.6173,   34.6409,   34.6535,   34.6550,   34.6565,   34.6527,
                                        34.6490,   34.6446,   34.6396,   34.6347,   34.6297,   34.6247,
                                        34.6490,   34.6446,   34.6396,   34.6347,   34.6297,   34.6247


References for 3D ocean circulation model:
Lee, C.-Y., and S. S. Chen, 2013: Stable Boundary Layer and Its Impact on Tropical Cyclone Structure in a Coupled Atmosphere-Ocean Model, Mon. Wea. Rev.
Price, J. F., T. B. Sanford, and G. Z. Forristal, 1994: Forced stage response to a moving hurricane. J. Phy. Oceanogr., 24, 233-260.



** Using the ACOM Forward Lagrangian trajectory calculation:

&domains
 num_traj                            = 25,

&physics
 traj_opt                            = 1,
 dm_has_traj                         = .true.,  ..true.,  .true.
 

For domain #1, the file must "wrfinput_traj_d01" exist in the working directory. Similarly for domain 2, 3, etc. Each domain
has a separate file for a namelist.

&traj_default
 traj_def%start_time        = '2000-01-24_12:00:00',
 traj_def%stop_time         = '2000-01-25_12:00:00',
 traj_def%dyn_name(1:6)     = 'p', 'T', 'z', 'u', 'v', 'w',
 traj_def%hyd_name(1)       = 'QVAPOR',
/
  
&traj_spec
 traj_type%start_time = '2000-01-24_12:00:00',  '2000-01-24_12:00:00',  
                        '2000-01-24_12:00:00',  '2000-01-24_12:00:00',  
                        '2000-01-24_12:00:00',  '2000-01-24_12:00:00',  
                        '2000-01-24_12:00:00',  '2000-01-24_12:00:00',  
                        '2000-01-24_12:00:00',  '2000-01-24_12:00:00',  
                        '2000-01-24_12:00:00',  
 traj_type%stop_time  = '2000-01-25_12:00:00',  '2000-01-25_12:00:00',  
                        '2000-01-25_12:00:00',  '2000-01-25_12:00:00',  
                        '2000-01-25_12:00:00',  '2000-01-25_12:00:00',  
                        '2000-01-25_12:00:00',  '2000-01-25_12:00:00',  
                        '2000-01-25_12:00:00',  '2000-01-25_12:00:00',  
                        '2000-01-25_12:00:00',  
 traj_type%lev        = 60., 60., 60., 60., 60., 60., 60., 60., 60., 60., 60., 
 traj_type%lon        = -79.88470, -79.74551, -79.60422, -79.46072, 
                        -79.31503, -79.16708, -79.01682, -78.86417, 
                        -78.70911, -78.55151, -78.39142,
 traj_type%lat        =  29.18063,  29.70515,  30.23069,  30.75718,  
                         31.28461,  31.81292,  32.34208,  32.87204,  
                         33.40276,  33.93421,  34.46631,
/



** Vertical nesting

The WRF model now supports vertical nesting for a coincident (online)
model simulation (during a single model run, differing numbers of
vertical levels may be used per domain).  This is activated with a switch
to turn on the option (vert_refine_method).  The namelist array eta_levels
is manually filled in for each domain.  Below is an example for two 
domains.  

NOTE: The user is restricted to using the RRTM LW and Dudhia SW radiation schemes.
NOTE: The user is restricted from using the hybrid vertical coordinate.

 &domains
 max_dom                             = 2,
 e_vert                              = 35,    45,
 eta_levels(1:35)                    = 1., 0.993, 0.983, 0.97, 0.954, 0.934, 0.909, 0.88, 0.8406663, 0.8013327, 
                                       0.761999, 0.7226653, 0.6525755, 0.5877361, 0.5278192, 0.472514, 
                                       0.4215262, 0.3745775, 0.3314044, 0.2917579, 0.2554026, 0.2221162, 
                                       0.1916888, 0.1639222, 0.1386297, 0.1156351, 0.09525016, 0.07733481, 
                                       0.06158983, 0.04775231, 0.03559115, 0.02490328, 0.0155102, 0.007255059, 0.
 eta_levels(36:81)                   = 1.0000, 0.9946, 0.9875, 0.9789, 0.9685, 0.9562, 0.9413, 0.9238, 0.9037, 0.8813, 0.8514,
                                       0.8210, 0.7906, 0.7602, 0.7298, 0.6812, 0.6290, 0.5796, 0.5333, 0.4901, 0.4493, 0.4109,
                                       0.3746, 0.3412, 0.3098, 0.2802, 0.2524, 0.2267, 0.2028, 0.1803, 0.1593, 0.1398, 0.1219,
                                       0.1054, 0.0904, 0.0766, 0.0645, 0.0534, 0.0433, 0.0341, 0.0259, 0.0185, 0.0118, 0.0056, 0.    
 vert_refine_method                  = 0,     2,



** Tropopause data level of max winds data, for program real.exe only

When information (mostly NCEP supplied through GFS or NAM) is available 
in the Grib2 file, AND extracted with ungrib, the metgrid program inserts
flag information into the data stream that is input by real.  The real
program is able to use the available u, v, T, height fields (each may be on
a tropopause .OR. the level of max winds) in the vertical interpolation.
To "tune" the data, the user may select a level below which the max wind 
inforamtion is ignored.  The default is 300 hPa.  The user may also 
select the level that when exceeded the trop and maxw fields are ignored
(due to the horizontal pressure difference detecting a user-defined
discontinuity, and the metgrid horizontal interpolation across the 
discontinuity would be suspect).  By default, this option is turned off.
The user may choose to individually activate the vertical interpolation of
either the level of max winds .OR. the tropopause level.

Default (all pressure units Pa):
 &domains
 maxw_horiz_pres_diff                = 5000
 trop_horiz_pres_diff                = 5000
 maxw_above_this_level               = 30000
 use_maxw_level                      = 0 (0=do not use level, 1 = use level)
 use_trop_level                      = 0 (0=do not use level, 1 = use level)




** Using aerosol option aer_opt = 2:

&physics
 aer_opt            = 2,
 aer_type           = 1,
 aer_aod550_opt     = 1,
 aer_aod550_val     = 0.12,
 aer_angexp_opt     = 1,
 aer_angexp_val     = 1.3,
 aer_ssa_opt        = 1,
 aer_ssa_val        = 0.85,
 aer_asy_opt        = 1,
 aer_asy_val        = 0.90,


** Using Jimenez wind-farm scheme

In the &physics namelist record, set the MAX_DOM value:
 windfarm                            = 1,    1,    1

Also in the directory with namelist.input, is the file defining the
specifics of the wind turbine type: wind-turbine-1.tbl

The location of wind turbines are specified as lat lon, and 
the turbine type: windturbines.txt

** Using stochastic schemes
&stoch
 skebs              = 1, 1, 1, 
 rand_perturb       = 1, 1, 1, 


** Using kfcupscheme cu_physics (cumulus scheme option). This has been tested with
the CAM radiation scheme and is not recommended with other radiation schemes.
Regarding cu_rad_feedback, users want the parameterized clouds to affect radiation.  
Turning it off is only useful as a sensitivity study to determine the importance of 
that effect. Regarding shallowcu_forced_ra option, setting it to true will override 
the cloud fraction calculations to a prescribed maximum cloud fraction (a value of 0.36)
which can be changed by the user for sensitivity testing purposes.

&physics
 cu_physics          = 10,    10,    10,
 ra_lw_physics       = 3,     3,     3,
 ra_sw_physics       = 3,     3,     3,
 cu_rad_feedback     =.true.,.true.,.true.,
 shallowcu_forced_ra = .false.,.false.,.false.,
 numBins             = 21,    21,    21,
 thBinSize           = 0.1,   0.1,   0.1,
 rBinSize            = 0.0001,0.0001,0.0001,
 minDeepFreq         = 0.333, 0.333, 0.333,
 minShallowFreq      = 0.01,  0.01,  0.01, 


** To write out an input file during the model simulation.

&time_control
 write_input                         = .TRUE.,
 inputout_interval_m                 = 60, 60, 60
 input_outname                       = "wrfinput_out_d<domain>_<date>"
 inputout_begin_h                    = 3, 6, 6
 inputout_end_h                      = 9, 6, 6

** To use climatological data in Thompson microphysics option 28: in addition to
process additional data in metgrid, use these:

&domains
 wif_input_opt                       = 1
 num_wif_levels                      = 30

&physics
 mp_physics                          = 28,    28,    28,
 use_aero_icbc                       = .true.


** The hybrid vertical coordinate (HVC) requires three pieces:
1. The WRF and real codes must be built with HVC activated (./configure -hyb)
2. The eta location (etac) at which the eta levels higher up in the atmosphere
   become isobaric must be defined (suggested default in Registry).
3. The run-time option to select the HVC (hybrid_opt, default is OFF): 
&dynamics
 hybrid_opt          = 2,
 etac                = 0.2,

** New mechanism to specify physics:

&physics
 physics_suite = 'CONUS'

which is equivalent to

 mp_physics         = 8,
 cu_physics         = 6,
 ra_lw_physics      = 4,
 ra_sw_physics      = 4,
 bl_pbl_physics     = 2,
 sf_sfclay_physics  = 2,
 sf_surface_physics = 2,

&physics
 physics_suite = 'TROPICAL'

which is equivalent to

 mp_physics         = 6,
 cu_physics         = 16,
 ra_lw_physics      = 4,
 ra_sw_physics      = 4,
 bl_pbl_physics     = 1,
 sf_sfclay_physics  = 91,
 sf_surface_physics = 2,


To overwrite the cu_physics option for a second nest, set 

 &physics
 physics_suite                       = 'tropical'
 cu_physics                          = -1,    -1,     0,
 ...

** New way to automatically choose levels (default auto_levels_opt=2)
 max_dz                              = 1000.
 auto_levels_opt                     = 2
 dzbot                               = 50.
 dzstretch_s                         = 1.3
 dzstretch_u                         = 1.1

** Using the scale-adaptive SMS-3DTKE subgrid turbulent mixing scheme: 
 &physics
   bl_pbl_physics = 0,
   sf_sfclay_physics = 1(or 5, 91), 
 /
 &dynamics
   diff_opt       = 2,
   km_opt         = 5, 
 /


** Using lightning option
 &physics
 lightning_option                    = 2,
 iccg_method                         = 1,
 iccg_prescribed_num                 = 2,
 iccg_prescribed_den                 = 1,
 cldtop_adjustment                   = 0,
 lightning_dt                        = 5,   ; default: time_step
 lightning_start_seconds             = 600,
 flashrate_factor                    = 1.0,
 ltng_temp_upper                    = -45., ; used by lnox_opt=2
 ltng_temp_lower                    = -15., ; used by lnox_opt=2
 /
 
 &chem
 lnox_opt                           = 2,
 n_ic                               = 200,
 n_cg                               = 200,
 lnox_passive                       = .false.,
 /


** Using the irrigation parameterizations:
&physics
 sf_surf_irr_scheme                  = 3,
 irr_daily_amount                    = 5.7,
 irr_start_hour                      = 2,
 irr_num_hours                       = 3,
 irr_start_julianday                 = 135,
 irr_end_julianday                   = 350,
 irr_ph                              = 0,
 irr_freq                            = 1,
/
```

## namelist.input d01=15km
[Git em_real/namelist.input](https://github.com/wrf-model/WRF/blob/master/test/em_real/namelist.input)

```sh
 &time_control
 run_days                            = 0,
 run_hours                           = 36,
 run_minutes                         = 0,
 run_seconds                         = 0,
 start_year                          = 2019, 2019,
 start_month                         = 09,   09, 
 start_day                           = 04,   04,
 start_hour                          = 12,   12,
 end_year                            = 2019, 2019,
 end_month                           = 09,   09,
 end_day                             = 06,   06,
 end_hour                            = 00,   00,
 interval_seconds                    = 10800
 input_from_file                     = .true.,.true.,
 history_interval                    = 60,  60,
 frames_per_outfile                  = 1, 1,
 restart                             = .false.,
 restart_interval                    = 7200,
 io_form_history                     = 2
 io_form_restart                     = 2
 io_form_input                       = 2
 io_form_boundary                    = 2
 /

 &domains
 time_step                           = 90,
 time_step_fract_num                 = 0,
 time_step_fract_den                 = 1,
 max_dom                             = 2,
 e_we                                = 150,    220,
 e_sn                                = 130,    214,
 e_vert                              = 45,     45,
 dzstretch_s                         = 1.1
 p_top_requested                     = 5000,
 num_metgrid_levels                  = 34,
 num_metgrid_soil_levels             = 4,
 dx                                  = 15000,
 dy                                  = 15000,
 grid_id                             = 1,     2,
 parent_id                           = 0,     1,
 i_parent_start                      = 1,     53,
 j_parent_start                      = 1,     25,
 parent_grid_ratio                   = 1,     3,
 parent_time_step_ratio              = 1,     3,
 feedback                            = 1,
 smooth_option                       = 0
 /

 &physics
 physics_suite                       = 'CONUS'
 mp_physics                          = -1,    -1,
 cu_physics                          = -1,    -1,
 ra_lw_physics                       = -1,    -1,
 ra_sw_physics                       = -1,    -1,
 bl_pbl_physics                      = -1,    -1,
 sf_sfclay_physics                   = -1,    -1,
 sf_surface_physics                  = -1,    -1,
 radt                                = 15,    15,
 bldt                                = 0,     0,
 cudt                                = 0,     0,
 icloud                              = 1,
 num_land_cat                        = 21,
 sf_urban_physics                    = 0,     0,
 fractional_seaice                   = 1,
 /

 &fdda
 /

 &dynamics
 hybrid_opt                          = 2, 
 w_damping                           = 0,
 diff_opt                            = 2,      2,
 km_opt                              = 4,      4,
 diff_6th_opt                        = 0,      0,
 diff_6th_factor                     = 0.12,   0.12,
 base_temp                           = 290.
 damp_opt                            = 3,
 zdamp                               = 5000.,  5000.,
 dampcoef                            = 0.2,    0.2,
 khdif                               = 0,      0,
 kvdif                               = 0,      0,
 non_hydrostatic                     = .true., .true.,
 moist_adv_opt                       = 1,      1,
 scalar_adv_opt                      = 1,      1,
 gwd_opt                             = 1,      0,
 /

 &bdy_control
 spec_bdy_width                      = 5,
 specified                           = .true.
 /

 &grib2
 /

 &namelist_quilt
 nio_tasks_per_group = 0,
 nio_groups = 1,
 /
```

## namelist.input d01=4km
[Git em_real/namelist.input.4km](https://github.com/wrf-model/WRF/blob/master/test/em_real/namelist.input.4km)

```sh
 &time_control
 run_days                            = 0,
 run_hours                           = 12,
 run_minutes                         = 0,
 run_seconds                         = 0,
 start_year                          = 2001, 2001, 2001,
 start_month                         = 06,   06,   06,
 start_day                           = 11,   11,   11,
 start_hour                          = 12,   12,   12,
 end_year                            = 2001, 2001, 2001,
 end_month                           = 06,   06,   06,
 end_day                             = 12,   12,   12,
 end_hour                            = 12,   12,   12,
 interval_seconds                    = 10800
 input_from_file                     = .true.,.true.,.true.,
 history_interval                    = 60,   60,   60,
 frames_per_outfile                  = 1,    1,    1,
 restart                             = .false.,
 restart_interval                    = 1440,
 io_form_history                     = 2
 io_form_restart                     = 2
 io_form_input                       = 2
 io_form_boundary                    = 2
 /

 &domains
 time_step                           = 24,
 time_step_fract_num                 = 0,
 time_step_fract_den                 = 1,
 max_dom                             = 1,
 e_we                                = 225,   112,   94,
 e_sn                                = 202,   97,    91,
 e_vert                              = 35,    35,    35,
 p_top_requested                     = 5000,
 num_metgrid_levels                  = 32
 num_metgrid_soil_levels             = 4,
 dx                                  = 4000,
 dy                                  = 4000,
 grid_id                             = 1,     2,     3,
 parent_id                           = 0,     1,     2,
 i_parent_start                      = 1,     31,    30,
 j_parent_start                      = 1,     17,    30,
 parent_grid_ratio                   = 1,     3,     3,
 parent_time_step_ratio              = 1,     3,     3,
 feedback                            = 1,
 smooth_option                       = 0
 /

 &physics
 mp_physics                          = 8,     8,     8,
 ra_lw_physics                       = 4,     4,     4,
 ra_sw_physics                       = 4,     4,     4,
 radt                                = 10,    10,    10,
 sf_sfclay_physics                   = 1,     1,     1,
 sf_surface_physics                  = 2,     2,     2,
 bl_pbl_physics                      = 1,     1,     1,
 bldt                                = 0,     0,     0,
 ysu_topdown_pblmix                  = 1,
 cu_physics                          = 0,     0,     0,
 cudt                                = 0,     0,     0,
 icloud                              = 1,
 num_land_cat                        = 21,
 sf_urban_physics                    = 0,     0,     0,
 /

 &fdda
 /

 &dynamics
 hybrid_opt                          = 2, 
 w_damping                           = 0,
 diff_opt                            = 1,      1,      1,
 km_opt                              = 4,      4,      4,
 diff_6th_opt                        = 0,      0,      0,
 diff_6th_factor                     = 0.12,   0.12,   0.12,
 base_temp                           = 290.
 damp_opt                            = 3,
 zdamp                               = 5000.,  5000.,  5000.,
 dampcoef                            = 0.2,    0.2,    0.2
 khdif                               = 0,      0,      0,
 kvdif                               = 0,      0,      0,
 non_hydrostatic                     = .true., .true., .true.,
 moist_adv_opt                       = 2,      2,      2,
 scalar_adv_opt                      = 2,      2,      2,
 /

 &bdy_control
 spec_bdy_width                      = 5,
 specified                           = .true.,
 /

 &grib2
 /

 &namelist_quilt
 nio_tasks_per_group = 0,
 nio_groups = 1,
 /
```

## namelist.input.PBL-LES
[Git PBL LES](https://github.com/wrf-model/WRF/blob/master/test/em_real/namelist.input.pbl-les)

```sh
 &time_control
 run_days                            = 0,
 run_hours                           = 12,
 run_minutes                         = 0,
 run_seconds                         = 0,
 start_year                          = 2001, 2001, 2001,
 start_month                         = 06,   06,   06,
 start_day                           = 11,   11,   11,
 start_hour                          = 12,   12,   12,
 start_minute                        = 00,   00,   00,
 start_second                        = 00,   00,   00,
 end_year                            = 2001, 2001, 2001,
 end_month                           = 06,   06,   06,
 end_day                             = 12,   12,   12,
 end_hour                            = 12,   12,   12,
 end_minute                          = 00,   00,   00,
 end_second                          = 00,   00,   00,
 interval_seconds                    = 10800
 input_from_file                     = .true.,.true.,.true.,
 history_interval                    = 60,   60,   60,
 frames_per_outfile                  = 1,    1,    1,
 restart                             = .false.,
 restart_interval                    = 60,
 io_form_history                     = 2
 io_form_restart                     = 2
 io_form_input                       = 2
 io_form_boundary                    = 2
 /

 &domains
 time_step                           = 5,
 time_step_fract_num                 = 0,
 time_step_fract_den                 = 1,
 max_dom                             = 3,
 e_we                                = 225,   112,   94,
 e_sn                                = 202,   97,    91,
 e_vert                              = 35,    35,    35,
 p_top_requested                     = 5000,
 num_metgrid_levels                  = 40
 num_metgrid_soil_levels             = 4,
 dx                                  = 1000,
 dy                                  = 1000,
 grid_id                             = 1,     2,     3,
 parent_id                           = 0,     1,     2,
 i_parent_start                      = 1,     31,    30,
 j_parent_start                      = 1,     17,    30,
 parent_grid_ratio                   = 1,     3,     3,
 parent_time_step_ratio              = 1,     3,     3,
 feedback                            = 1,
 smooth_option                       = 0
 /

 &physics
 mp_physics                          = 8,     8,     8,
 ra_lw_physics                       = 4,     4,     4,
 ra_sw_physics                       = 4,     4,     4,
 radt                                = 1,     1,     1,
 sf_sfclay_physics                   = 1,     1,     1,
 sf_surface_physics                  = 2,     2,     2,
 bl_pbl_physics                      = 1,     0,     0,
 bldt                                = 0,     0,     0,
 ysu_topdown_pblmix                  = 1,
 cu_physics                          = 0,     0,     0,
 cudt                                = 0,     0,     0,
 icloud                              = 1,
 num_land_cat                        = 21,
 sf_urban_physics                    = 0,     0,     0,
 /

 &dynamics
 hybrid_opt                          = 2, 
 w_damping                           = 0,
 diff_opt                            = 2,      2,      2,
 km_opt                              = 4,      3,      3,
 diff_6th_opt                        = 0,      0,      0,
 diff_6th_factor                     = 0.12,   0.12,   0.12,
 base_temp                           = 290.
 damp_opt                            = 3,
 zdamp                               = 5000.,  5000.,  5000.,
 dampcoef                            = 0.2,    0.2,    0.2
 khdif                               = 0,      0,      0,
 kvdif                               = 0,      0,      0,
 non_hydrostatic                     = .true., .true., .true.,
 moist_adv_opt                       = 2,      2,      2,
 scalar_adv_opt                      = 2,      2,      2,
 /

 &bdy_control
 spec_bdy_width                      = 5,
 specified                           = .true.,
 /

 &namelist_quilt
 nio_tasks_per_group = 0,
 nio_groups = 1,
 /
```
