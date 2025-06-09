---
layout: post
title: MPAS | WRF | MYNN
categories: [MPAS]
tags: [NWP, MPAS, WRF, PBL, MYNN, TKE]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-05-22 11:04:00
index_img: https://i.imgur.com/0uL1eqw.png
banner_img: https://i.imgur.com/0uL1eqw.png
---

# MYNN PBL scheme

The **Turbulent Kinetic Energy (TKE) budget equation** in the **Mellor-Yamada-Nakanishi-Niino (MYNN) PBL** scheme of the WRF model and how they are outputted. The MYNN scheme is an improved version of the classic Mellor-Yamada scheme (Mellor and Yamada 1982), and it is able to output the TKE budget equation terms on the default WRF history file. These outputs were unbalanced with the equivalent terms used in MYNN to integrate the TKE budget equation. **It was fixed on WRF 4.2.2 version code for tests released on WRF 4.5 (CCPP version).**

The **MYNN-EDMF scheme** represents the local mixing using an eddy-diffusivity approach tied to turbulent kinetic energy (TKE). The nonlocal mixing, important for convective boundary layers, is represented using a mass-flux approach. The scheme can be run with either a **2.5 or 3.0 closure and includes a partial-condensation scheme**, commonly referred to as a cloud PDF or statistical-cloud scheme, to represent the effects of subgrid-scale (SGS) clouds on buoyancy. **This module was originally translated from Nakanishi and Niino (2009) and put into the WRF model by Mariusz Pagowski NOAA/GSD and CIRA/CSU in 2008**. It was extensively modified by Joseph Olson and Jaymes Kenyon of NOAA/GSD and CU/CIRES.

---

# Literature Review

- [Mellor, George L. "**Analytic prediction of the properties of stratified planetary surface layers**." J. Atmos. Sci 30.9 (1973): 1061-1069.](https://www.gfdl.noaa.gov/bibliography/related_files/glm7301.pdf)
- [Mellor, George L., and Tetsuji Yamada. "**Development of a turbulence closure model for geophysical fluid problems.**" Reviews of Geophysics 20.4 (1982): 851-875.](https://rcfftp.soest.hawaii.edu/kelvin/OCN665/OCN665_full/papers/MellorYamada1982.pdf)
  - Applications of **second-moment turbulent closure hypotheses** to geophysical fluid problems have developed rapidly since 1973, when genuine predictive skill in coping with the effects of stratification was demonstrated.
  - 2. **The Basic Model**
    - The Closure Assumptions
    - The Level 4 Model (Melior and Yarnada, 1974)
    - The Level 3 Model
    - The Level 2.5 Model
    - The Level 2 Model
    - The Boundary Layer Apporximation: Level 2.5
    - The Boundary Layer Apporximation: Level 2
- [Nakanishi, Mikio, and Hiroshi Niino. "**An improved Mellor–Yamada level-3 model: Its numerical stability and application to a regional prediction of advection fog.**" Boundary-layer meteorology 119 (2006): 397-407.](https://www.researchgate.net/profile/Mikio-Nakanishi-2/publication/226023953_An_Improved_Mellor-Yamada_Level-3_Model_Its_Numerical_Stability_and_Application_to_a_Regional_Prediction_of_Advection_Fog/links/0deec5330d21abae7b000000/An-Improved-Mellor-Yamada-Level-3-Model-Its-Numerical-Stability-and-Application-to-a-Regional-Prediction-of-Advection-Fog.pdf)
- [Nakanishi, Mikio, and Hiroshi Niino. "**Development of an improved turbulence closure model for the atmospheric boundary layer.**" Journal of the Meteorological Society of Japan. Ser. II 87.5 (2009): 895-912.](https://www.jstage.jst.go.jp/article/jmsj/87/5/87_5_895/_pdf)


# A Description of the MYNN-EDMF Scheme and the Coupling to Other Components in WRF–ARW (March 2019)

- <https://www.atmos.albany.edu/facstaff/rfovell/NWP/noaa_19837_DS1.pdf>

{% gi 2 2 %}
![](https://i.imgur.com/mXipGBJ.png)
![](https://i.imgur.com/0uL1eqw.png)
{% endgi %}

# Turbulent Kinetic Energy Budget for MYNN-EDMF PBL Scheme in WRF model (2023)

The aim of this technical note is to describe the Turbulent Kinetic Energy (TKE) budget equation in the Mellor-Yamada-Nakanishi-Niino (MYNN) PBL scheme of the WRF model and how they are outputted. The MYNN scheme is an improved version of the classic Mellor-Yamada scheme (Mellor and Yamada 1982), and it is able to output the TKE budget equation terms on the default WRF history file. These outputs were unbalanced with the equivalent terms used in MYNN to integrate the TKE budget equation. It was fixed on WRF 4.2.2 version code for tests released on WRF 4.5 (CCPP version).

Besides the TKE output fix, a new set of similarity relationship equations was implemented in the MYNN scheme to provide the lower boundary conditions for TKE budget equation terms.

- [**Turbulent Kinetic Energy Budget for MYNN-EDMF PBL Scheme in WRF model** | **Technical note** UFSM/GruMA 001/2023]
  - ![](https://i.imgur.com/piiTKYD.png){width=400}

![](https://i.imgur.com/H8sm0RX.png)

## output all TKE budget equation terms

![](https://i.imgur.com/KepL4Sl.png)

# Second-moment models 

## Turbulence:

- [Heinze, R., Mironov, D., & Raasch, S. (2015). Second‐moment budgets in cloud topped boundary layers: A large‐eddy simulation study. Journal of Advances in Modeling Earth Systems, 7(2), 510-536.](https://agupubs.onlinelibrary.wiley.com/doi/abs/10.1002/2014MS000376)

In NWP, turbulence is often parameterized using second-moment schemes. **These schemes predict the budget equations for the second-order moments of fluctuating velocity and scalar fields**, which helps to better represent the effects of turbulent mixing on the atmosphere. 

# Level 2.5 vs 3 ?

## Key Points
- Research suggests Level 2.5 in the MYNN PBL scheme focuses on TKE, while Level 3 includes more detailed turbulence variables, offering higher accuracy but at a higher computational cost.
- It seems likely that Level 2.5 is simpler and better for general forecasting, while Level 3 suits complex scenarios like cloudy conditions.
- The evidence leans toward distinguishing them in WRF by setting `bl_mynn_closure = 2.5` for Level 2.5 and `bl_mynn_closure = 3.0` for Level 3, with `bl_pbl_physics = 5`.

## What Are Level 2.5 and Level 3 in the MYNN PBL Scheme?

The MYNN PBL scheme, used in models like WRF, helps simulate how the atmosphere mixes near the surface. Level 2.5 and Level 3 are different ways to handle this mixing, based on how detailed the calculations are.

- **Level 2.5**: This level mainly tracks **turbulent kinetic energy (TKE)**, which measures the energy of air turbulence. It **estimates other details**, like temperature changes, using simpler formulas. It’s less demanding on computers and good for everyday weather predictions.
- **Level 3**: This level goes further, **tracking TKE and additional details like temperature and moisture variations directly**. It’s more accurate, especially for complex situations like cloudy skies, but it needs more computing power.

## How to Distinguish Them

In WRF, you can choose between these levels by adjusting the settings:

- For Level 2.5, set `bl_pbl_physics = 5` and `bl_mynn_closure = 2.5`.
- For Level 3, set `bl_pbl_physics = 5` and `bl_mynn_closure = 3.0`.

This tells the model which level of detail to use for mixing calculations.

---

## Survey Note: Detailed Explanation of MYNN PBL Scheme Levels in WRF

The Mellor-Yamada-Nakanishi-Niino (MYNN) Planetary Boundary Layer (PBL) scheme is a critical component of numerical weather prediction models like the Weather Research and Forecasting (WRF) model, designed to parameterize turbulent mixing within the planetary boundary layer. This layer, closest to the Earth’s surface, significantly influences weather patterns, especially near-surface conditions. The MYNN scheme, rooted in the Mellor-Yamada turbulence closure framework, offers different levels of complexity, notably Level 2.5 and Level 3, which cater to varying needs in accuracy and computational efficiency. This section provides a comprehensive analysis based on available documentation and user discussions, ensuring a thorough understanding for researchers and practitioners.

### Background on MYNN PBL Scheme
The MYNN scheme is a second-order turbulence closure model, an advancement over earlier Mellor-Yamada schemes, tuned using large-eddy simulation (LES) data to improve predictions, particularly over oceanic surfaces ([Improvement of the Mellor–Yamada–Nakanishi–Niino Planetary Boundary-Layer Scheme Based on Observational Data in China | Boundary-Layer ...](https://link.springer.com/article/10.1007/s10546-016-0187-0)). It was integrated into WRF-ARW version 3.1 by Mariusz Pagowski of NOAA’s Global Systems Division, aiming to enhance turbulence representation for operational systems like Rapid Refresh (RAP) and High-Resolution Rapid Refresh (HRRR) ([Noaa](https://repository.library.noaa.gov/view/noaa/19837)). The scheme’s flexibility allows it to operate at different closure levels, balancing computational cost with physical realism.

### Detailed Description of Level 2.5
**Level 2.5** is a partially prognostic closure model within the MYNN framework. It focuses on solving a prognostic equation for turbulent kinetic energy (TKE), denoted as \(q^2/2\), where \(q^2\) represents **twice the TKE**. This level simplifies the parameterization by **diagnosing other second-order moments**, *such as temperature variance (\(θ^2\)), momentum fluxes (\(u'w'\), \(v'w'\)), and heat fluxes (\(w'θ'\)), using algebraic relationships rather than prognostic equations*. This approach reduces computational demand, making it suitable for operational forecasting where efficiency is crucial.

- **Key Features**:
  - **Prognostic variable: TKE only.**
  - **Diagnostic variables: Other second-order moments, computed via simplified formulas.**
  - Closure assumptions: Neglects time tendencies and advection of most second-order moments, with pressure correlations highly parameterized.
  - Stability functions: Based on gradient Richardson number, accounting for stratification effects.

- **Applications**: Level 2.5 is often the default choice for general weather forecasting, particularly in clear boundary layers under stable or neutral conditions, due to its robustness and lower computational cost. It is noted for stability in simulations, as seen in user discussions where MYNN 2.5 runs successfully for extended periods compared to Level 3 ([WRF User's Forum • View topic - MYNN level 3 PBL scheme](http://forum.wrfforum.com/viewtopic.php?f=9&t=3938)).

### Detailed Description of Level 3
Level 3 represents a more complex closure, extending beyond TKE to include prognostic equations for additional second-order moments, such as scalar variances (e.g., temperature variance \(θ^2\), moisture variance \(q^2\), and covariances like \(θ'q'\)). This level retains more terms in the turbulence equations, including partial contributions from pressure covariances and third-order transport terms, aligning closer to a full second-order closure model.

- **Key Features**:
  - Prognostic variables: TKE and scalar variances, reducing the reliance on diagnostic computations.
  - Diagnostic variables: Fewer, as more moments are explicitly evolved over time.
  - Closure assumptions: Includes more detailed parameterization of pressure terms and transport, enhancing representation of non-local mixing.
  - Stability functions: May include additional dependencies on prognostic scalar variances, improving accuracy under complex stability conditions.

- **Applications**: Level 3 is preferred for research simulations where scalar variances are critical, such as in foggy conditions, cloud formation, or urban boundary layers. It performs better in scenarios with strong stratification or cloud interactions, as suggested by studies comparing PBL schemes ([Investigation of PBL schemes combining the WRF model simulations with scanning water vapor differential absorption lidar measurements - Milovac ...](https://agupubs.onlinelibrary.wiley.com/doi/full/10.1002/2015JD023927)). However, it is computationally more expensive, and user reports indicate potential instability, with crashes noted in simulations ([WRF User's Forum • View topic - Problem with MYNN2.5 and MYNN3 PBL schemes](http://forum.wrfforum.com/viewtopic.php?f=6&t=4017)).

### Comparative Analysis: Level 2.5 vs. Level 3
The distinction between Level 2.5 and Level 3 lies in their approach to turbulence parameterization, affecting both accuracy and computational demand. The following table summarizes the key differences:

| **Aspect**                | **Level 2.5**                              | **Level 3**                                |
|---------------------------|--------------------------------------------|--------------------------------------------|
| **Prognostic Variables**  | Only TKE (\(q^2/2\))                      | TKE + scalar variances (e.g., \(θ^2\), \(q^2\), \(θ'q'\)) |
| **Diagnostic Variables**  | Most second-order moments (e.g., \(θ^2\), \(u'w'\)) | Fewer, as more moments are prognostic      |
| **Complexity**            | Simpler, fewer equations                  | More complex, additional equations         |
| **Computational Cost**    | Lower                                     | Higher                                     |
| **Accuracy**              | Good for general PBL cases                | Better for complex cases (e.g., clouds, fog) |
| **Pressure Terms**        | Highly parameterized                      | Less parameterized, more explicit terms    |
| **Applications**          | Operational forecasting, general use       | Research, complex PBL (e.g., cloudy BL)    |

This comparison highlights that Level 2.5 is efficient for broad applications, while Level 3 offers enhanced detail for specialized studies, at the cost of increased computational resources.

### Implementation in WRF
In the WRF model, the MYNN PBL scheme is selected via the `bl_pbl_physics` namelist parameter, set to 5 for MYNN. The choice between Level 2.5 and Level 3 is controlled by the `bl_mynn_closure` option, as detailed in user forums and documentation ([WRF namelist setting ---MYNN3 | WRF & MPAS-A Support Forum](https://forum.mmm.ucar.edu/threads/wrf-namelist-setting-mynn3.18733/post-45436)). Specifically:

- **Level 2.5**: Set `bl_mynn_closure = 2.5`.
- **Level 3**: Set `bl_mynn_closure = 3.0`.

Note that `bl_mynn_closure` should be set as a single value (e.g., `bl_mynn_closure = 3.0,`) and not as multiple domain values (e.g., `3.0,3.0,3.0,`) to avoid namelist errors, as observed in user error reports. Additional options, such as `bl_mynn_mixscalars = 1`, can activate prognostic equations for scalar variances, aligning with Level 3 functionality ([CCPP Scientific Documentation: GSD MYNN-EDMF PBL Scheme Module](https://dtcenter.ucar.edu/GMTB/v4.0/sci_doc/group__gsd__mynn__edmf.html)).

### Practical Considerations
The choice between Level 2.5 and Level 3 depends on the simulation’s objectives. Level 2.5 is often sufficient for operational forecasting, given its stability and efficiency, as seen in simulations running for a year on a 5 km grid ([WRF User's Forum • View topic - MYNN level 3 PBL scheme](http://forum.wrfforum.com/viewtopic.php?f=9&t=3938)). Conversely, Level 3 is recommended for research, especially in scenarios requiring detailed scalar variance representation, such as cloudy boundary layers, but users should be aware of potential instability, with reports of crashes after short simulation periods ([WRF User's Forum • View topic - Problem with MYNN2.5 and MYNN3 PBL schemes](http://forum.wrfforum.com/viewtopic.php?f=6&t=4017)).

### Conclusion
In summary, Level 2.5 and Level 3 in the MYNN PBL scheme offer different levels of turbulence closure, with Level 2.5 focusing on TKE and diagnosing other moments for efficiency, and Level 3 prognosing additional moments for higher accuracy. Distinguishing them in WRF involves setting `bl_mynn_closure` to 2.5 or 3.0, respectively, with `bl_pbl_physics = 5`. This understanding is derived from a synthesis of WRF documentation, user forums, and scientific literature, acknowledging the complexity and ongoing research in PBL parameterization.

## Key Citations

- [WRF User's Forum MYNN level 3 PBL scheme](http://forum.wrfforum.com/viewtopic.php?f=9&t=3938)
- [WRF namelist setting MYNN3 WRF & MPAS-A Support Forum](https://forum.mmm.ucar.edu/threads/wrf-namelist-setting-mynn3.18733/post-45436)
- [Improvement of the Mellor–Yamada–Nakanishi–Niino Planetary Boundary-Layer Scheme Based on Observational Data in China](https://link.springer.com/article/10.1007/s10546-016-0187-0)
- [Investigation of PBL schemes combining WRF model simulations with scanning water vapor differential absorption lidar measurements](https://agupubs.onlinelibrary.wiley.com/doi/full/10.1002/2015JD023927)
- [WRF User's Forum Problem with MYNN2.5 and MYNN3 PBL schemes](http://forum.wrfforum.com/viewtopic.php?f=6&t=4017)
- [CCPP Scientific Documentation GSD MYNN-EDMF PBL Scheme Module](https://dtcenter.ucar.edu/GMTB/v4.0/sci_doc/group__gsd__mynn__edmf.html)

# WRF

## namelist

- <https://github.com/wrf-model/WRF/blob/master/run/README.namelist>

```namelist
 &physics
 sf_sfclay_physics (max_dom)         surface-layer option (old bl_sfclay_physics option)
                                     = 0, no surface-layer
                                     = 1, Revised MM5 Monin-Obukhov scheme (Jimenez, renamed in v3.6)
                                     = 2, Monin-Obukhov (Janjic) scheme
                                     = 3, NCEP Global Forecast System scheme 
                                     = 4, QNSE surface layer
                                     = 5, MYNN surface layer
                                     = 7, Pleim-Xiu surface layer
                                     = 10, TEMF surface layer
                                     = 91, Old MM5 scheme (previously option 1)
 bl_pbl_physics (max_dom)            boundary-layer option
                                     = 0, no boundary-layer 
                                     = 1, YSU scheme
                                     = 2, Mellor-Yamada-Janjic TKE scheme
                                     = 3, Hybrid EDMF GFS scheme 
                                     = 4, Eddy-diffusivity Mass Flux, Quasi-Normal Scale Elimination PBL
                                     = 5, MYNN 2.5 level TKE scheme, works with
                                          sf_sfclay_physics=1 or 2 as well as 5
                                          (option 6 removed in 4.5, replaced by bl_mynn_closure options)
 bl_mynn_closure                          = 2.5: level 2.5
                                            2.6: level 2.6
                                            3.0: level 3.0                                         
                                     = 7, ACM2 (Pleim) PBL
                                     = 8, Bougeault and Lacarrere (BouLac) PBL
                                     = 9, UW boundary layer scheme from CAM5 (CESM 1_0_1)
                                     = 10, TEMF (Total Energy Mass Flux) scheme
                                          sf_sfclay_physics=10
                                     = 11, Shin-Hong 'scale-aware' PBL scheme
                                     = 12, Grenier-Bretherton-McCaa scheme
                                     = 16, TKE+TKE dissipation rate (epsilon) scheme
                                           works with surface layer options 1,91,2,5.
                                     = 17, TKE+TKE dissipation rate+TPE (temparature variance) scheme
                                     = 99, MRF scheme

 tke_budget (max_dom)                = 0,       ! default off; = 1 adds MYNN tke budget terms to output (new in 4.5)
                                                  (replacing bl_mynn_tkebudget in prior versions)
 bl_mynn_tkeadvect (max_dom)         = .false., ! default off; = .true. do MYNN tke advection
 icloud_bl                                        option to couple the subgrid-scale clouds from the PBL scheme (MYNN only) 
                                                  to radiation schemes 
                                     0:  no coupling; 1: activate coupling to radiation (default)
 bl_mynn_cloudmix (max_dom)          = 0 ! default off; =1 activates mixing of qc and qi in MYNN
                                     0: no mixing of qc & qi; 1: mixing activated (default). 
                                        Note qnc and qni are mixed when scalar_pblmix =1.
 bl_mynn_mixlength                   option to change mixing length formulation in MYNN
                                     0: original as in Nakanishi and Niino 2009, 
                                     1: RAP/HRRR (including BouLac in free atmosphere), 
                                     2: experimental (default; includes cloud-specific mixing length and a scale-aware mixing 
                                        length, following Ito et al. 2015, BLM). Option 2 has been well tested with 
                                        the edmf options.
 bl_mynn_cloudpdf                    option to switch to different cloud PDFs to represent subgrid clouds
                                     0: original (Sommeria and Deardorf 1977); 
                                     1: Kuwano et al 2010, similar to option 0, but uses resolved scale gradients 
                                        as opposed to higher order moments ; 
                                     2: from Chaboureau and Bechtold (2002, JAS, with mods, default) 
 bl_mynn_edmf (max_dom)              = 1   ! activate mass-flux option in MYNN, 0: mass-flux option off
                                             Related (hidden) options: 
 bl_mynn_edmf_mom (max_dom)          = 1   ! - activates momentum transport in MYNN mass-flux scheme 
                                           (assuming bl_mynn_edmf > 0); 0=off (default)
                                       0    no momentum transport; 1: momentum transport activated (default)
 bl_mynn_edmf_tke (max_dom)          = 0,  ! default; = 1 activates TKE transport in MYNN mass-flux scheme 
                                            (assuming bl_mynn_edmf > 0)
                                       0    no TKE transport (default);1: activate TKE transport
 bl_mynn_output (max_dom)            = 0,  ! do not output extra arrays
                                       1    allocate and output extra 3D arrays from MYNN PBL
 bl_mynn_mixscalars (max_dom)        = 0   ! off, 1: activate mixing of scalars
 bl_mynn_mixqt (max_dom)             = 0   ! mixing moisture species separately, 1: mixing total water

  shcu_physics (max_dom)              independent shallow cumulus option (not tied to deep convection)
                                     = 0, no independent shallow cumulus
                                     = 1, Grell 3D ensemble scheme (use with cu_physics=93 or 5) (PLACEHOLDER: SWITCH NOT YET IMPLEMENTED--use ishallow)
                                     = 2, Park and Bretherton shallow cumulus from CAM5 (CESM 1_0_1)
                                     = 3, GRIMS shallow cumulus from YSU group
                                     = 4, NSAS shallow cululus scheme. Must be used with KIAPS SAS cumulus scheme (cu_physics = 14)
                                     = 5, Deng cumulus scheme (including both shallow and deep convections) from PSU and WRF-Solar.
                                          However the deep part isn't very active. Not recommended to use alone for deep convection 
                                          case. Could work well for grid sizes 3-9 km.
                                          This scheme only works with MYJ PBL scheme, and should not be combined with icloud=3
                                          This scheme can also work with MYNN PBL scheme, but one should turn off EDMF (bl_mynn_edmf=0)

 iz0tlnd                             = 0        ! thermal roughness length for sfclay (0 = old, 1 = veg dependent Chen-Zhang Czil,
                                                      2 = Zilitinkevitch (czil=0.1))
                                                      for mynn sfc (0=Zilitinkevitch (def),1=Chen-Zhang,2=mod Yang,3=const zt)

 spp_pbl (max_dom)                   = 0        ! Perturb parameters of MYNN PBL scheme only

 pert_mynn             = .false.  ! Activates perturbations to the MYNN PBL parameterization
 pert_mynn_qv          = 0.0
 pert_mynn_qc          = 0.0
 pert_mynn_t           = 0.0
 pert_mynn_qke         = 0.0
```

# MPAS

- [MPAS-Atmosphere Model User’s Guide Version 8.2.0](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_8.2.0.pdf)

![](https://i.imgur.com/cpVkHp4.png)
![](https://i.imgur.com/Zz6asM2.png)

- Registry.xml

```xml
<nml_option name="config_mynn_tkeadvect" type="logical" default_value="false" in_defaults="false"
        description="= true:do MYNN TKE advection"

<nml_option name="config_mynn_tkebudget" type="integer" default_value="0" in_defaults="false"
        description="=1:option to add the MYNN TKE budget terms to output"

<nml_option name="config_mynn_closure" type="real" default_value="2.5" in_defaults="false"
        description="2.5 level or 3.0 level for the MYNN TKE turbulence closure"

<nml_option name="config_mynn_cloudpdf" type="integer" default_value="2" in_defaults="false"
        description="switch to different cloud PDFs to represent subgrid clouds in the MYNN PBL scheme"

<nml_option name="config_mynn_mixlength" type="integer" default_value="2" in_defaults="false"
        description="mixing length in the MYNN PBL scheme:=0(Nakanishi and Niino 2009);=1(RAP/HRRR);=2(Ito et al. 2015)"

<nml_option name="config_mynn_stfunc" type="integer" default_value="1" in_defaults="false"
<nml_option name="config_mynn_topdown" type="integer" default_value="0" in_defaults="false"
<nml_option name="config_mynn_scaleaware" type="integer" default_value="1" in_defaults="false"
<nml_option name="config_mynn_dheat_opt" type="integer" default_value="1" in_defaults="false"

<nml_option name="config_mynn_edmf" type="integer" default_value="1" in_defaults="false"
        description="=1:activate the EDMF option in the MYNN PBL scheme (=0 otherwise)"

<nml_option name="config_mynn_edmf_dd" type="integer" default_value="0" in_defaults="false"
        description="=1:activate the EDMF downdraft option in the MYNN PBL scheme (=0 otherwise)"
<nml_option name="config_mynn_edmf_mom" type="integer" default_value="0" in_defaults="false"
        description="=1:activate momentum transport with the EDMF option of the MYNN PBL scheme(=0 otherwise)"
<nml_option name="config_mynn_edmf_tke" type="integer" default_value="0" in_defaults="false"
        description="=1:activate TKE transport with the EDMF option of the MYNN PBL scheme (=0 otherwise)"
<nml_option name="config_mynn_edmf_output" type="integer" default_value="0" in_defaults="false"
        description="=0:suppress the allocation of variables needed in the EDMF option of the MYNN PBL scheme"
<nml_option name="config_mynn_mixscalars" type="integer" default_value="1" in_defaults="false"
        description="=1:activate mixing of scalars in the MYNN PBL scheme (=0 otherwise)"
<nml_option name="config_mynn_mixclouds" type="integer" default_value="1" in_defaults="false"
        description="1:activate mixing of cloud condensates in the MYNN PBL scheme"
<nml_option name="config_mynn_mixqt" type="integer" default_value="0" in_defaults="false"
        description="=0:activatevmixing of moisture species separately;= 1:activate mixing of total water in the mynn PBL scheme"

<var name="edmf_a" type="real" dimensions="nVertLevels nCells Time" units="unitless"
        description="EDMF relative updraft area of moist updrafts in the MYNN PBL scheme"
<var name="edmf_w" type="real" dimensions="nVertLevels nCells Time" units="m s^{-1}"
        description="EDMF vertical velocity of  moist updrafts in the MYNN PBL scheme"
<var name="edmf_qt" type="real" dimensions="nVertLevels nCells Time" units="kg kg^{-1}"
        description="EDMF total water mixing ratio in moist updrafts in the MYNN PBL scheme"
<var name="edmf_qc" type="real" dimensions="nVertLevels nCells Time" units="kg kg^{-1}"
        description="EDMF liquid water mixing ratio in moist updrafts in the MYNN PBL scheme"
<var name="edmf_thl" type="real" dimensions="nVertLevels nCells Time" units="K"
        description="EDMF liquid-ice water potential temperature in moist updrafts in the MYNN PBL scheme"
<var name="edmf_ent" type="real" dimensions="nVertLevels nCells Time" units="m^{-1}"
```

# MPAS/WRF 2025 workshop

## MYNN-EDMF PBL Scheme

---

- [**Updates to the MYNN-EDMF PBL Scheme to Improve MPAS- and WRF- based Forecasting Systems **| NOAA Global Systems Laboratory](https://www2.mmm.ucar.edu/wrf/users/workshops/WS2025/presentations/33_Olson.pdf)

{% gi 6 2-2-2 %}
![](https://i.imgur.com/58i18B3.png)
![](https://i.imgur.com/2SbZnKF.png)
![](https://i.imgur.com/wn1GyMD.png)
![](https://i.imgur.com/yMkC6U8.png)
![](https://i.imgur.com/xfkoCMw.png)
![](https://i.imgur.com/P7P6IKZ.png)
{% endgi %}

---

