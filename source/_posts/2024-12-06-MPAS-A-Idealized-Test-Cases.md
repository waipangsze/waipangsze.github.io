---
layout: post
title: MPAS | MPAS-A Idealized Test Cases
categories: [MPAS]
tags: [MPAS, HPC, WRF, NWP]
author: wpsze
date: 2024-12-06 09:19:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/pxRygfS.png
banner_img: https://i.imgur.com/pxRygfS.png
---

# MPAS-Atmosphere idealized test cases

{% note danger %}
- **<https://mpas-dev.github.io/atmosphere/test_cases.html>**
{% endnote %}

The downloads below for MPAS-Atmosphere idealized test cases include the following:

- an MPAS mesh file to be used with the test case;
- for 3-d test cases, mesh decomposition files for several MPI task counts;
- a **namelist file for creating initial conditions** for the test case;
- a **namelist file for running the model**; and
- **NCL scripts** for making plots of the output.

The process of generating initial conditions and running each test case is described in further detail in the MPAS-Atmosphere Users' Guide.

## Test cases on the sphere

### The Jablonowski and Williamson baroclinic wave test case.

{% note danger %}
**Jablonowski, C., & Williamson, D. L. (2006). A baroclinic instability test case for atmospheric model dynamical cores. Quarterly Journal of the Royal Meteorological Society, 132(621C), 2943–2975. <https://doi.org/10.1256/qj.06.12>**
{% endnote %}

- The dynamical core is initialized with steady-state, balanced initial conditions that are an analytic solution to the hydrostatic primitive equations. **This model set-up reveals how well a dynamical core maintains this initial state** before numerical round-off and truncation errors as well as gravity waves degrade the steady state.

![](https://i.imgur.com/Cu4feVO.png){width=400}
![](https://i.imgur.com/luYcZPl.png){width=400}

Steps to run this test case:

1. **Link** the `init_atmosphere_model` and `atmosphere_model` executables from the top-level MPAS directory.
2. Run `init_atmosphere_model` to create initial conditions.
   1. The log file may show WARNING messages; these can generally be ignored.
3. Run `atmosphere_model`
4. Run the **bwave_surface_p.ncl** script to produce **plots of surface pressure** each simulated day.

```console
bwave_surface_p.ncl            x1.40962.graph.info.part.16
namelist.atmosphere            x1.40962.graph.info.part.2
namelist.init_atmosphere       x1.40962.graph.info.part.24
README                         x1.40962.graph.info.part.4
stream_list.atmosphere.output  x1.40962.graph.info.part.6
streams.atmosphere             x1.40962.graph.info.part.8
streams.init_atmosphere        x1.40962.grid.nc
x1.40962.graph.info.part.12
```

and,

``` netcdf
netcdf x1.40962.init {
dimensions:
	nVertLevels = 26 ;
	nCells = 40962 ;
	Time = UNLIMITED ; // (1 currently)
	StrLen = 64 ;
	nEdges = 122880 ;
	nVertices = 81920 ;
	TWO = 2 ;
	maxEdges = 10 ;
	maxEdges2 = 20 ;
	vertexDegree = 3 ;
	R3 = 3 ;
	nMonths = 12 ;
	nSoilComps = 8 ;
	FIFTEEN = 15 ;
	nVertLevelsP1 = 27 ;
	nSoilLevels = 4 ;
```

Create a run.sh,

```sh
#!/bin/bash
export CODE_DIR=/home/wpsze/MPAS-A/mpasv822/MPASv822/
export MPAS_ENV=/home/wpsze/MPAS-A/mpas_env.sh
source ${MPAS_ENV}
source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate mpas_env

ln -s ${CODE_DIR}/init_atmosphere_model
ln -s ${CODE_DIR}/atmosphere_model

./init_atmosphere_model
./atmosphere_model
```

the run is done and shows,

```log
    timer_name                                            total       calls        min            max            avg      pct_tot   pct_par     par_eff
  1 total time                                        6369.85205         1     6369.85205     6369.85205     6369.85205   100.00       0.00       1.00
  2  initialize                                         16.04797         1       16.04797       16.04797       16.04797     0.25       0.25       1.00
  3   read_ICs                                          12.92155         1       12.92155       12.92155       12.92155     0.20      80.52       1.00
  2  diagnostic_fields                                  39.15386      6146        0.00027        0.55109        0.00637     0.61       0.61       1.00
  2  stream_output                                     135.25525      3073        0.00000       25.31770        0.04401     2.12       2.12       1.00
  2  time integration                                 6178.20117      3072        1.83821        6.71001        2.01113    96.99      96.99       1.00
  3   atm_rk_integration_setup                          72.62624      3072        0.02114        0.06312        0.02364     1.14       1.18       1.00
  3   atm_compute_moist_coefficients                    37.50331      3072        0.01115        0.03584        0.01221     0.59       0.61       1.00
  3   physics_get_tend                                  22.05075      3072        0.00633        0.03133        0.00718     0.35       0.36       1.00
  3   atm_compute_vert_imp_coefs                        45.81344      3072        0.01347        0.04496        0.01491     0.72       0.74       1.00
  3   atm_compute_dyn_tend                            2480.10986      9216        0.20928        0.82551        0.26911    38.94      40.14       1.00
  3   small_step_prep                                  133.52666      9216        0.01284        0.03533        0.01449     2.10       2.16       1.00
  3   atm_advance_acoustic_step                       2095.08105     36864        0.04626        0.15461        0.05683    32.89      33.91       1.00
  3   atm_divergence_damping_3d                        457.87030     36864        0.01101        0.03708        0.01242     7.19       7.41       1.00
  3   atm_recover_large_step_variables                 268.87030      9216        0.02323        0.08294        0.02917     4.22       4.35       1.00
  3   atm_compute_solve_diagnostics                    445.47675      9216        0.03804        0.16322        0.04834     6.99       7.21       1.00
  3   atm_rk_dynamics_substep_finish                    43.84446      3072        0.01267        0.03757        0.01427     0.69       0.71       1.00
 
 -----------------------------------------
 Total log messages printed:
    Output messages =                25140
    Warning messages =                   0
    Error messages =                     0
    Critical error messages =            0
 -----------------------------------------
```

- Plot: `ncl bwave_surface_p.ncl`, **for surface pressure**,

```ncl
  rdzw = f->rdzw(:)
  p = f->pressure(iTime,:,0)
  rho=f->rho(iTime,:,:)
  qv = f->qv(iTime,:,:) 
  h = (p + 0.5/rdzw(0)*9.80616*(1.25*rho(:,0) - .25*rho(:,1)))/100. ;for dry!!!
  ;h = (p + 0.5/rdzw(0)*9.80616*(1.25*rho(:,0)*(1.+qv(:,0)) - .25*rho(:,1)*(1.+qv(:,1))))/100. ;for moist!!!
```

![](https://i.imgur.com/IY1Zckj.gif){width=600}

## Test cases on the Cartesian plane

### Supercell

The supercell thunderstorm test case.

- [Idealized Nonhydrostatic Supercell Simulations in the Global MPAS | Klemp | 2014 ](https://www2.cgd.ucar.edu/events/20140407/Presentations-Posters/Klemp.pdf)
  - Supercell Thunderstorms
    - Strong, long-lived convective cells
    - Deep, persistent rotating updrafts
    - May propagate tranverse to the mean winds
    - May split into two counter-rotating storms
    - Produce most of the world’s intense tornadoes
- Based on historical supercell simulations (Weisman and Klemp, 1982, 1984)
- [Zarzycki, C. M., Jablonowski, C., Kent, J., Lauritzen, P. H., Nair, R., Reed, K. A., ... & Skamarock, W. C. (2019). DCMIP2016: the splitting supercell test case. Geoscientific Model Development, 12(3), 879-892.](http://admg.engin.umich.edu/wp-content/uploads/sites/525/2021/03/DCMIP-2016_TestCaseDocument_10June2016.pdf)

Steps to run this test case:

1. Link the `init_atmosphere_model` and `atmosphere_model` executables from the top-level MPAS directory.
2. Run `init_atmosphere_model` to create initial conditions.
3. Run `atmosphere_model`.
4. Run the `supercell.ncl` script to produce **plots theta, vertical velocity**, etc.

```netcdf
netcdf supercell_init {
dimensions:
	nVertLevels = 40 ;
	nCells = 28080 ;
	Time = UNLIMITED ; // (1 currently)
	StrLen = 64 ;
	nEdges = 84240 ;
	nVertices = 56160 ;
	TWO = 2 ;
	maxEdges = 6 ;
	maxEdges2 = 12 ;
	vertexDegree = 3 ;
	R3 = 3 ;
	nMonths = 12 ;
	nSoilComps = 8 ;
	FIFTEEN = 15 ;
	nVertLevelsP1 = 41 ;
	nSoilLevels = 4 ;
```

the run is done and shows,

```log
    timer_name                                            total       calls        min            max            avg      pct_tot   pct_par     par_eff
  1 total time                                        6989.56445         1     6989.56445     6989.56445     6989.56445   100.00       0.00       1.00
  2  initialize                                         13.22789         1       13.22789       13.22789       13.22789     0.19       0.19       1.00
  3   read_ICs                                          11.03032         1       11.03032       11.03032       11.03032     0.16      83.39       1.00
  2  diagnostic_fields                                  30.90804      4802        0.00030        0.53316        0.00644     0.44       0.44       1.00
  2  stream_output                                      52.69895      2401        0.00000       24.40299        0.02195     0.75       0.75       1.00
  2  time integration                                 6891.87109      2400        2.28900        4.57639        2.87161    98.60      98.60       1.00
  3   physics driver                                     0.01804      2400        0.00000        0.00014        0.00001     0.00       0.00       1.00
  3   atm_rk_integration_setup                          81.38436      2400        0.02777        0.08220        0.03391     1.16       1.18       1.00
  3   atm_compute_moist_coefficients                    61.76581      2400        0.01953        0.08913        0.02574     0.88       0.90       1.00
  3   physics_get_tend                                  32.39993      2400        0.00967        0.02704        0.01350     0.46       0.47       1.00
  3   atm_compute_vert_imp_coefs                        43.12508      2400        0.01534        0.04965        0.01797     0.62       0.63       1.00
  3   atm_compute_dyn_tend                            1610.07593      7200        0.17735        0.70892        0.22362    23.04      23.36       1.00
  3   small_step_prep                                   97.92229      7200        0.01138        0.03734        0.01360     1.40       1.42       1.00
  3   atm_advance_acoustic_step                       1270.13843     28800        0.03023        0.10505        0.04410    18.17      18.43       1.00
  3   atm_divergence_damping_3d                        310.51599     28800        0.00890        0.05312        0.01078     4.44       4.51       1.00
  3   atm_recover_large_step_variables                 237.22810      7200        0.02374        0.08794        0.03295     3.39       3.44       1.00
  3   atm_advance_scalars                              986.22546      4800        0.14607        0.64590        0.20546    14.11      14.31       1.00
  3   atm_compute_solve_diagnostics                    371.22009      7200        0.03595        0.17258        0.05156     5.31       5.39       1.00
  3   atm_advance_scalars_mono                         644.47906      2400        0.18351        0.88479        0.26853     9.22       9.35       1.00
  3   atm_rk_dynamics_substep_finish                    43.15440      2400        0.01410        0.04277        0.01798     0.62       0.63       1.00
  3   microphysics                                    1031.64172      2400        0.30451        0.79860        0.42985    14.76      14.97       1.00
  4    mp_kessler                                      247.80276      2400        0.08487        0.28454        0.10325     3.55      24.02       1.00
 
 -----------------------------------------
 Total log messages printed:
    Output messages =                26971
    Warning messages =                   0
    Error messages =                     0
    Critical error messages =            0
 -----------------------------------------
```

Plot,

{% gi 2 2 %}
![1](https://i.imgur.com/EoUDfnf.png)
![2](https://i.imgur.com/71tbDoJ.png)
{% endgi %}

### Mountain-wave

The Schaer mountain-wave test case.

{% note danger %}
**[Schär, C., Leuenberger, D., Fuhrer, O., Lüthi, D., & Girard, C. (2002). A new terrain-following vertical coordinate formulation for atmospheric prediction models. Monthly Weather Review, 130(10), 2459-2480.](https://iacweb.ethz.ch/doc/publications/2002_ScharEtAl_VertCoord.pdf)**
{% endnote %}

- **Analytical solution** (Eqn. 46, Figure 13g)
- [Standard Tests of NH dynamical kernels | Jozef Vivoda | 2005](https://www.umr-cnrm.fr/gmapdoc/IMG/pdf/TestingNHMOdels.pdf)
  - P.8 Schaer test case

![](https://i.imgur.com/aGauoyFl.png){width=600}

Steps to run this test case:

1. Link the `init_atmosphere_model` and `atmosphere_model` executables from the top-level MPAS directory
2. Run `init_atmosphere_model` to create initial conditions
3. Run `atmosphere_model`
4. Run the `mtn_wave_w.ncl` script to **plot vertical cross-sections of vertical velocity**

```netcdf
netcdf mtn_wave_init {
dimensions:
	nVertLevels = 70 ;
	nCells = 1600 ;
	Time = UNLIMITED ; // (1 currently)
	StrLen = 64 ;
	nEdges = 4800 ;
	nVertices = 3200 ;
	TWO = 2 ;
	maxEdges = 6 ;
	maxEdges2 = 12 ;
	vertexDegree = 3 ;
	R3 = 3 ;
	nMonths = 12 ;
	nSoilComps = 8 ;
	FIFTEEN = 15 ;
	nVertLevelsP1 = 71 ;
	nSoilLevels = 4 ;
```

the run is done and shows,

```log
    timer_name                                            total       calls        min            max            avg      pct_tot   pct_par     par_eff
  1 total time                                         479.17487         1      479.17487      479.17487      479.17487   100.00       0.00       1.00
  2  initialize                                          1.40130         1        1.40130        1.40130        1.40130     0.29       0.29       1.00
  3   read_ICs                                           1.04874         1        1.04874        1.04874        1.04874     0.22      74.84       1.00
  2  diagnostic_fields                                  38.32835      6002        0.00030        0.26142        0.00639     8.00       8.00       1.00
  2  stream_output                                       8.16368      3001        0.00000        2.18816        0.00272     1.70       1.70       1.00
  2  time integration                                  430.28293      3000        0.12909        0.37118        0.14343    89.80      89.80       1.00
  3   atm_rk_integration_setup                           7.68167      3000        0.00226        0.00653        0.00256     1.60       1.79       1.00
  3   atm_compute_moist_coefficients                     3.88766      3000        0.00115        0.00331        0.00130     0.81       0.90       1.00
  3   physics_get_tend                                   1.73502      3000        0.00049        0.00179        0.00058     0.36       0.40       1.00
  3   atm_compute_vert_imp_coefs                         4.89605      3000        0.00145        0.00413        0.00163     1.02       1.14       1.00
  3   atm_compute_dyn_tend                             181.87900      9000        0.01650        0.06131        0.02021    37.96      42.27       1.00
  3   small_step_prep                                    9.66831      9000        0.00094        0.00315        0.00107     2.02       2.25       1.00
  3   atm_advance_acoustic_step                        113.06064     36000        0.00273        0.01248        0.00314    23.59      26.28       1.00
  3   atm_divergence_damping_3d                         37.56601     36000        0.00091        0.00282        0.00104     7.84       8.73       1.00
  3   atm_recover_large_step_variables                  21.39947      9000        0.00175        0.00898        0.00238     4.47       4.97       1.00
  3   atm_compute_solve_diagnostics                     37.34831      9000        0.00319        0.01347        0.00415     7.79       8.68       1.00
  3   atm_rk_dynamics_substep_finish                     4.19751      3000        0.00123        0.00363        0.00140     0.88       0.98       1.00
 
 -----------------------------------------
 Total log messages printed:
    Output messages =                24552
    Warning messages =                   0
    Error messages =                     0
    Critical error messages =            0
 -----------------------------------------
 ```

 Plot,

 ![](https://i.imgur.com/ZZHlHFq.gif){width=600}

# References

1. Skamarock, W. C., Klemp, J. B., Duda, M. G., Fowler, L. D., Park, S., & Ringler, T. D. (2012). A Multiscale Nonhydrostatic Atmospheric Model Using Centroidal Voronoi Tesselations and C-Grid Staggering. Monthly Weather Review, 140(9), 3090-3105. <https://doi.org/10.1175/MWR-D-11-00215.1>
   1. Test cases: Nonhydrostatic flows on Cartesian planes
      1. 2D Scha¨r test case
      2. 2D density current
      3. Supercell simulation
   2. Test cases: Large-scale flow on the sphere
      1.  Dry baroclinic wave simulations
      2.  Unfiltered baroclinic wave simulations