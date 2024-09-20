---
layout: post
title: Predicting lightning activity using WRF-ELEC model  
categories: [WRF]
tags: [WRF-ELEC, Lightning]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
---

# WRF-ELEC model

## MicroTed/wrf4-elec

<https://github.com/MicroTed/wrf4-elec>

The impact of initial conditions in predicting lightning activity using the Weather Research and Forecasting (WRF) with the electrification (ELEC) extra package, known as WRF-ELEC model, has been investigated.

### An example of namelist.input

<https://github.com/MicroTed/wrf4-elec/blob/7c90e71bdc4db527d0ec034804e4fd200cdacf28/test/em_quarter_ss/electest/namelist.input#L55>

```
 &physics
 mp_physics                          = 18,     1,     1,
 elec_physics                        = 1,
 nssl_ipelec                         = 2,
 nssl_cccn                           = 0.8e9,
 nssl_isaund                         = 11,
 nssl_iscreen                        = 1,
 nssl_idischarge                     = 1,
 nssl_ibrkd                          = 4,
 ra_lw_physics                       = 0,     0,     0,
 ra_sw_physics                       = 0,     0,     0,
 radt                                = 30,    30,    30,
 sf_sfclay_physics                   = 0,     0,     0,
 sf_surface_physics                  = 0,     0,     0,
 bl_pbl_physics                      = 0,     0,     0,
 bldt                                = 0,     0,     0,
 cu_physics                          = 0,     0,     0,
 cudt                                = 5,     5,     5,
 num_soil_layers                     = 5,
 /
```

## wrf-model's registry.elec

<https://github.com/wrf-model/WRF/blob/master/Registry/registry.elec>

{% note primary %}
Look at namelist !!
{% endnote %}

```
# SPACE CHARGE EXPLICIT LIGHTNING
state   real    scr            ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCR"       "Rain space charge mixing ratio" "# C kg(-1)"
state   real    scw            ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCW"       "cloud water space charge mixing ratio" "# C kg(-1)"
state   real    sci            ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCI"       "cloud ice space charge mixing ratio" "# C kg(-1)"
state   real    scs            ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCS"       "snow space charge mixing ratio" "# C kg(-1)"
state   real    sch            ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCH"       "graupel water space charge mixing ratio" "# C kg(-1)"
state   real    schl           ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCHL"       "hail water space charge mixing ratio" "# C kg(-1)"
state   real    sciona          ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCIONA"       "Passive net ion space charge" "# C kg(-1)"
state   real    clnox          ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "CLNOX"      "Lightning NOx concentration" "# moles kg(-1)"
# END SPACE CHARGE EXPLICIT LIGHTNING

# EXPLICIT LIGHTNING
#
state    real    rscghis_2d      ij   misc    1         -     irh         "rscghis_2d"             "MAX NONINDUCTIVE CHARGING 2D"     "C m-2"
state    real    induc           ikj  misc    1         -     irh         "induc"                  "TOTAL INDUCTIVE CHARGING "     "C m-3"
state    real    noninduc        ikj  misc    1         -     irh         "noninduc"               "TOTAL NONINDUCTIVE CHARGING"     "C m-3"
state    real    sctot           ikj  misc    1         -     irh         "sctot"                  "Total Space Charge Density"     "C m-3"
state    real    elecmag         ikj  misc    1         -     irh         "elecmag"                "EFIELD MAGNITUDE"     "V m-1"
state    real    elecx           ikj  misc    1         -     irh         "elecx"                  "EFIELD X-Component"     "V m-1"
state    real    elecy           ikj  misc    1         -     irh         "elecy"                  "EFIELD Y-Component"     "V m-1"
state    real    elecz           ikj  misc    1         -     irh         "elecz"                  "EFIELD Z-Component"     "V m-1"
state    real    pot             ikj  misc    1         -     irh         "pot"                    "POTENTIAL"     "V"
state    real    light            ij  misc    1         -     irh         "light"                  "lightning flash"         "flash origin density"
state    real    lightdens        ij  misc    1         -     irh         "lightdens"              "lightning flash density"         "flash column-1"
state    integer lightdis         ij  misc    1         -     irh         "lightdis"               "lightning source density"     "Source column-1"
state    real    flshi           ikj  misc    1         -     irh         "flshi"                  "Lightning init points"     "count"
state    real    flshn           ikj  misc    1         -     irh         "flshn"                  "Negative channels"     "count"
state    real    flshp           ikj  misc    1         -     irh         "flshp"                  "Positive channels"     "count"
# END EXPLICIT LIGHTNING


# Explicit lightning
rconfig   integer  nssl_ipelec            namelist,physics      max_domains   0       rh       "Electrification selection"  ""      ""
rconfig   integer  nssl_isaund            namelist,physics      1             12      rh       "Charge separation selection"  ""      ""
rconfig   integer  nssl_iscreen           namelist,physics      1             0       rh       "Screening layer parameterization flag"  ""      ""
rconfig   real     nssl_lightrad          namelist,physics      1             12000   rh       "discharge cylinder radius (m)"  ""      ""
rconfig   integer  nssl_idischarge        namelist,physics      1             1       rh       "lightning discharge flag"  ""      ""
rconfig   integer  nssl_ibrkd             namelist,physics      1             4       rh       "Critical Breakeven Efield profile selection"  ""      ""
rconfig   real     nssl_ecrit             namelist,physics      1             120000  rh       "Critical Breakeven Efield magnitude for discharge (V/m) assuming height-constant Ecrit profile"  ""      ""
rconfig   real     nssl_disfrac           namelist,physics      1             0.3     rh       "percentile of charge removed upon discharge (BLM)"  ""      ""
# end Explicit lightning

rconfig   integer  elec_physics           namelist,physics      1            0       irh       "elec_physics"            ""      ""

# external WRF-ELEC package
package   noelec             elec_physics==0             -             -
package   eleclgt1d          elec_physics==1             -             scalar:scr,scw,sci,scs,sch,schl,sciona;state:rscghis_2d,sctot,noninduc,induc,pot,elecmag,elecx,elecy,elecz,light,lightdens,lightdis
package   eleclgtmsz         elec_physics==2             -             scalar:scr,scw,sci,scs,sch,schl,sciona;state:rscghis_2d,sctot,noninduc,induc,pot,elecmag,elecx,elecy,elecz,light,lightdens,lightdis,flshi,flshn,flshp
package   eleclgtmsznox      elec_physics==3             -             scalar:scr,scw,sci,scs,sch,schl,sciona,clnox;state:rscghis_2d,sctot,noninduc,induc,pot,elecmag,elecx,elecy,elecz,light,lightdens,lightdis,flshi,flshn,flshp
```

## sourceforege

<https://sourceforge.net/projects/wrfelec/files/>

This is a first release in WRF of a **basic electrification for the NSSL microphysics scheme**. The electrification parameterizations are based out of past work in modeling. The basics of the charge separation schemes comes from Mansell et al. (2005). Their use in the NSSL 2-moment microphysicsis shown in Mansell et al. 2010 and Mansell and Ziegler (2013). **First implementation into WRF is described by Fierro et al. (2013)**, which includes details of the basic discharge scheme (cylindrical discharge regions centered on lightning initiation points).

- Mansell et al. 2005: Charge structure and lightning sensitivity in a simulated multicell thunderstorm. J. Geophys. Res., 110, D12101, doi:10.1029/2004JD005287
- Mansell, E. R., C. L. Ziegler, and E. C. Bruning, 2010: Simulated electrification of a small thunderstorm with two-moment bulk microphysics. J. Atmos. Sci., 67, 171-194, doi:10. 1175/2009JAS2965.1.
- **Fierro, A. O., E.R. Mansell, C. Ziegler and D. R. MacGorman 2013: The implementation of an explicit charging and discharge lightning scheme within the WRF-ARW model: Benchmark simulations of a continental squall line, a tropical cyclone and a winter storm. Monthly Weather Review, Volume 141, 2390-2415** (see below)

If you have trouble with this option please contact the developers directly (i.e., not wrf_help):

> Ted Mansell (Ted.Mansell _at_ noaa.gov)

> Alexandre Fierro (Alex.Fierro _at_ noaa.gov) 

Specific namelist.input options are given below, along with a list of output fields.

```text
Electrification only works with the NSSL 2-moment options (with hail):

 mp_physics(max_dom)
                                     = 17, NSSL 2-moment 4-ice scheme (steady background CCN)
                                     = 18, NSSL 2-moment 4-ice scheme with predicted CCN (better for idealized than real cases)
                                       ; to set a global CCN value, use
                                       
Other variables in the &physics namelist:

 elec_physics                        = 0, electrification (and charge arrays) turned off (DEFAULT)
                                     = 1, electrification turned on with "1D" lightning scheme based on Ziegler and MacGorman (1994, JAS) (only works with mp_physics = 17 or 18, i.e., 2-moment NSSL schemes)

 nssl_ipelec (max_dom)     ! NOTE: only set this to a nonzero value on the innermost domain
                                     = 0, charging turned off (DEFAULT)
                                     = 2, non-inductive charging only
                                     = 3, non-inductive + inductive charging
                                     = 1, not used (reserved for future use)

 nssl_idischarge                     = 0, no discharge
                                     = 1, discharge turned on (DEFAULT)

 nssl_iscreen                        = 0, no screening layer (DEFAULT)
                                     = 1, screening layer scheme of Ziegler et al. (1991, JGR)
                                     = 2, screening layer only applied at cloud top (seems to be better for storm complexes and large storms)

 nssl_ibrkd                          = breakdown electric field profile to initiate lightning
                                     = 1, constant profile with height with value set by nssl_ecrit
                                     = 2 to 5, variants of vertical Ecrit profile of Dwyer (2003, GRL) - '4' is recommended (DEFAULT is 4).

 nssl_isaund                         = Calls the appropriate version of the UMIST (e.g., Saunders et al. 1991 scheme)
                                     = -5 : Saunders etal 1991 (following Helsdon et al. 2001, but use normal charging instead of 'anomalous' zones)
                                     = 0 : Saunders 1991 (modified as in Wojcik 1994)
                                     = 2 : RR scheme ( no extra factor ) (Mansell et al. 2005, JGR)
                                     = 4 : Saunders and Peck Scheme ( no extra factor ) (Mansell et al. 2005, JGR)
                                     = 9 : Saunders and Peck Scheme ( no extra factor, cutoff at -32.47 as orig eq. from sp98 ) (Mansell et al. 2010, JAS)
                                     = 10 : Brooks et al. RARcrit for T > -15 using saund2 (otherwise same as isaund=2) (set rarfac to negative in saund2)
                                     = 11 : Brooks et al. RARcrit for T > -15 using saund6 (otherwise same as isaund=4)
                                     = 12 : Brooks et al. RARcrit for T > -15 using saund6 (otherwise same as isaund=9) (DEFAULT)

 nssl_lightrad                       = 12000, radius (m) of discharge cylinder for 1-D discharge scheme

 nssl_disfrac                        = 0.3, nominal fraction of charge removed per discharge

 nssl_ecrit                          = 120000, breakdown electric field (Volts/m) to initiate lightning (i.e., constant value with height). (for nssl_ibrkd=1)
```

and, OUTPUT FIELDS are

```text
2D, time-dependent:

  LIGHT      : Sum of lightning initations in the column
  LIGHTDENS  : Flash extent density (sum of the number of flashes that extend into the grid column)
  LIGHTDIS   : A kind of "source point" density that adds up the number of points in a column that have lightning
               charge magnitude greater than 0.1*chgthr (effectively 0.01 nC/m^3 or more)

3D, time-dependent:

  POT         : Electric potential (Volts)
  ELECMAG     : Electric field magnitude
  ELECX       : x-component of E
  ELECY       : y-component of E
  ELECZ       : z-component of E
  INDUC       : Inductive charge separation rate
  NONINDUC    : Net noninductive charge separation rate
  SCW         : Charge density carried by cloud droplets
  SCR         : Charge density carried by rain
  SCI         : Charge density carried by cloud ice
  SCS         : Charge density carried by snow
  SCH         : Charge density carried by graupel
  SCHL        : Charge density carried by hail
  SCTOT       : Net charge density
  SCIONA      : Residual charge from lightning and evaporating hydrometeors that has not been reattached to another species
```
Source: README.wrfelec, updated 2015-05-04

# Compile Errors

1. Error NoahMP submodule files not populating WRF directories. 
   1. <https://waipangsze.github.io/2024/06/04/WRF_install_Error_NoahMP_submodule_files/>
2. time No such file or directory.
   1. <https://waipangsze.github.io/2024/05/29/WRF_install_time_error/>
   
# Done

```sh
--->                  Executables successfully built                  <---
 
-rwxrwxr-x 1 wpsze wpsze 43939288 Sep 20 13:08 main/ndown.exe
-rwxrwxr-x 1 wpsze wpsze 43824624 Sep 20 13:08 main/real.exe
-rwxrwxr-x 1 wpsze wpsze 43238968 Sep 20 13:08 main/tc.exe
-rwxrwxr-x 1 wpsze wpsze 48241712 Sep 20 13:07 main/wrf.exe
```

and there is an extra folder called "elec",

```sh
 $ ls wrf4-elec/elec/
binwrf.pl                           module_discharge.F
depend.elec                         module_discharge_msz.F
include_microphysics_driver_elec.F  module_mp_nssl_2mom_elec.F
include_solve_lightningnudge.F      module_nudge_light.F
Makefile                            module_screen.F
module_boxmgsetup.F                 takahashi.txt
module_commasmpi.F
```

# Literature review

Lightning activity is related to two important factors: dynamic–thermodynamic and microphysical characteristics (e.g., Williams et al., 2005; Rosenfeld et al., 2008; Guo et al., 2016; Wang et al., 2018; Zhao et al., 2020). Since the dynamic–thermodynamic processes affect the development of thunderstorm significantly, lightning activity is influenced by various dynamic–thermodynamic variables: temperature (Price, 1993), relative humidity in the lower and middle troposphere (Xiong et al., 2006; Fan et al., 2007), convective available potential energy (Qie et al., 2004; Stolz et al., 2015), and many others.

## Fierro et al. (2013)

The Implementation of an Explicit Charging and Discharge Lightning Scheme within the WRF-ARW Model: Benchmark Simulations of a Continental Squall Line, a Tropical Cyclone, and a Winter Storm

This work describes the recent implementation of explicit lightning physics within the Weather Research and Forecasting (WRF) Model. 

- Charging of hydrometeors consists of five distinct noninductive parameterizations, polarization of cloud water, and the exchange of charge during collisional mass transfer. 
- The three components of the ambient electric field are explicitly solved for via the computationally efficient multigrid elliptic solver. 
- The discharge process employs concepts adapted from two well-documented bulk lightning models, whereby charge reduction is imposed within a prescribed volume centered at grid points characterized by electric field magnitudes exceeding a given breakdown threshold. 

This lightning model was evaluated through benchmark 

- convection-allowing (3 km) model simulations of three contrasting convective systems: a continental squall line, 
- a major hurricane (Rita 2005), and 
- a winter storm. 
  
The areal coverage and magnitude of the simulated hourly flash origin density (FOD) for the continental squall line are qualitatively comparable to that of the total lightning data observations from Earth Networks Total Lightning Network (ENTLN). In agreement with the ENTLN observations, no FOD are simulated for the winter storm case. The simulated spatial FOD pattern of the hurricane and the eyewall gross charge structure were both in reasonable agreement with observations. The simulated FOD for all three cases were also evaluated against those obtained with the recently developed McCaul diagnostic lightning prediction schemes and exhibited overall good qualitative agreement with each other for Rita and the continental squall line.

## Thunderstorm Indices (LPI, Layrh, SCP)

**Thunderstorm Indices (LPI, Layrh, SCP)** obtained from IMD-WRF (3km) Hourly outputs.

- Model GEFS - WRF Description
- <https://srf.tropmet.res.in/srf/ts_prediction_system/lpi_model.php>
- **Lightning Potential Index (LPI)**
  - Here, LPI has been calculated using IMD-WRF (3km) hourly model output. 24 Hour accumulated LPI has been generated by adding hourly LPI values. The threat of LPI has been categorized in Low, Moderate and High. LOW: LPI < 0.001 and >0.0005; Moderate: LPI :< 0.01 and > 0.001; Threat Level: High: LPI > 0.01.
- **SuperCell Composite Parameter (SCP)**
  - SCP > 1 indicated that the environment is conducive for thunderstorm formation. The Threat Level of SCP has been categorized in Low, Moderate and High. LOW: 3 < SCP < 5; Moderate: 5 < SCP < 7; High: SCP > 7.
- **Layer Relative Humidity (LAYRH)**
  - The threat level of Layrh has been categorized in Low, Moderate and High. LOW: 20 < LAYRH < 40; Moderate: 40 < LAYRH < 60; High : LAYRH > 60

## Prediction of lightning activity using WRF-ELEC model

> **Gharaylou, M., Farahani, M. M., Mahmoudian, A., & Hosseini, M. (2020)**. Prediction of lightning activity using WRF-ELEC model: Impact of initial and boundary conditions. Journal of Atmospheric and Solar-Terrestrial Physics, 210, 105438.

> - <https://sci-hub.se/https://doi.org/10.1016/j.jastp.2020.105438>

- The WRF model belongs to the first group in which lightning is predicted based on the **Bulk Lightning Model (BLM)**. Ziegler and MacGorman (1994) have studied lightning flashes in a twoand three-dimensional cloud models using the WRF model.
- In the early 90s, Price and Rind (1992) developed a lightning parameterization scheme based on **cloud-top height**. 
- Furthermore, another lightning flash parameterization scheme has been introduced by Allen and Pickering (2002) and Allan et al. (2010) based on the **square of deep convective mass flux**. 
- Choi et al. (2005) and Zhao et al. (2009) incorporated the **convective available potential energy (CAPE)** in their comparative parameterizations.
- Yair et al. (2010) introduced **Lightning Potential Index (LPI)**, an advanced index for evaluating the probability of lightning occurrence, incorporated the dynamic and microphysic concepts.
- Following, Lynn et al. (2012) introduced the **potential electrical energy (Ep) parameter** into the WRF model, which was used to predict both **Cloud-to-Ground (CG)** and **Intra-Cloud (IC)** *lightning events*. 
- Afterwards, Wong et al. (2013) developed the lightning parameterization based on cloud-top height (Price and Rind, 1992).
- Gharaylou et al. (2019) examined 2 indices which are used in predicting the lightning activities both in time and location. They compared the **electric potential difference (POT)** and the **LPI** performance in predicting the lightning associated with four events over a domain located in the north of Iran.
- **The LPI is computationally less expensive and is directly calculated by the WRF model with less computational cost in comparison with the POT which requires an extra package (ELEC model).**

### Effect of Initial and boundary conditions (IBCs)

- The mesoscale models ordinarily obtain their initial and boundary conditions (IBCs) from large scale global models. **Applying different initial conditions highly influences the accuracy of the model prediction in local areas** (Zhang et al., 2002, 2006).
- A few works have been done to investigate the effect of initial conditions on lightning activity (Lynn et al., 2012). 
- Zepka et al. (2014) studied the impact of the resolution of the NCEP operational Global Forecast System.
- As mentioned, ***there are very few works*** related to the assessment of IBC effects in the WRF-ELEC model for lightning prediction.
  - WRF-ELEC model initialized with different datasets needs to be verified.


# References
1. 徐良韬, 张义军, 王飞, 郑栋. 2012: 雷暴起电和放电物理过程在WRF模式中的耦合及初步检验. 大气科学, 36(5): 1041-1052. DOI: 10.3878/j.issn.1006-9895.2012.11235
   1. WRFv3.2.1
   2. 本文将雷暴云的起电、放电物理过程引入中尺度的WRF (Weather Research and Forecasting) 模式,并对超级单体和飑线过程进行了模拟研究.起电过程在Milbrandt双参数微物理方案中写入,包含霰、雹与冰晶、雪之间的非感应起电机制,以及霰、雹与云滴之间的感应起电机制.
2. 徐良韬, 陈双, 姚雯, 等. 利用起放电模式开展闪电活动的直接预报试验. 应用气象学报, 2018, 29(5): 534-545. DOI: 10.11898/1001-7313.20180503.
   1. WRFv3.4.1
   2. 利用耦合有起电和放电物理过程的中尺度起电放电模式WRF-Electric
3. Wasson, G., & Panda, S. K. (2024). Sensitivity of PBL parameterization schemes in simulating lightning and thunderstorm using WRF-ELEC model. Climate Dynamics, 1-23.
   1. The WRF-ELEC model, an auxiliary package integrated into  WRF, is specifically designed to compute electric potential and overall lightning flash rate (source: <https://sourceforge.net/projects/wrfelec>).
   2. It incorporates explicit cloudresolving scale physics for lightning prediction ([Fierro et al.2013](https://scholar.google.com/scholar_url?url=https://journals.ametsoc.org/view/journals/mwre/141/7/mwr-d-12-00278.1.xml%3Ftab_body%3Dpdf&hl=en&sa=T&oi=gsr-r-ggp&ct=res&cd=0&d=6841908713848986342&ei=DTHtZqfdAfSC6rQP2qO70AU&scisig=AFWwaeaoJDdXALkuhsn44g0xEyU5)). Detailed formulations can be found in [Mansell et al.(2005)](https://scholar.google.com/scholar_url?url=https://agupubs.onlinelibrary.wiley.com/doi/abs/10.1029/2004jd005287&hl=en&sa=T&oi=gsr-r&ct=res&cd=0&d=13100036974408530565&ei=JTHtZs_QIeqs6rQPw4y2sAM&scisig=AFWwaeZ27Z0oFR35ZZvLDk-yrgo_) and [Fierro et al. (2013)](https://scholar.google.com/scholar_url?url=https://journals.ametsoc.org/view/journals/mwre/141/7/mwr-d-12-00278.1.xml%3Ftab_body%3Dpdf&hl=en&sa=T&oi=gsr-r-ggp&ct=res&cd=0&d=6841908713848986342&ei=OTHtZtytGPay6rQPxrKeiAM&scisig=AFWwaeaoJDdXALkuhsn44g0xEyU5).
   3. skill scores were calculated for each experiment.
      1. standard statistics and skill score analysis of CAPE (J/kg) and accumulated total precipitation (mm),
