---
layout: post
title: MPAS | Smagorinsky turbulence model | Eddy viscosity
categories: [MPAS]
tags: [MPAS, HPC, WRF, NWP]
author: wpsze
date: 2024-12-10 09:45:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/pxRygfS.png
banner_img: https://i.imgur.com/pxRygfS.png
---

# Large eddy simulation (LES)

Large eddy simulation (LES) is a mathematical model for turbulence used in computational fluid dynamics. It was initially proposed in **1963 by Joseph Smagorinsky to simulate atmospheric air currents**, and **first explored by Deardorff (1970)**. LES is currently applied in a wide variety of engineering applications, including combustion,acoustics, and simulations of the atmospheric boundary layer.

The principal idea behind LES is to reduce the computational cost by ignoring the smallest length scales, which are the most computationally expensive to resolve, via low-pass filtering of the Navier–Stokes equations. Such a low-pass filtering, which can be viewed as a time- and spatial-averaging, effectively removes small-scale information from the numerical solution. This information is not irrelevant, however, and its effect on the flow field must be modelled, a task which is an active area of research for problems in which small-scales can play an important role.

# Smagorinsky–Lilly model 

The first sub-grid scales (SGS) model developed was the **Smagorinsky–Lilly SGS model**, which was developed by Joseph Smagorinsky and used in the first LES simulation by Deardorff. It models the eddy viscosity as:

$$
\nu_\mathrm{t} = C \Delta^2\sqrt{2\bar{S}_{ij}\bar{S}_{ij}} = C \Delta^2 \left| \bar{S} \right|
$$

where $\Delta$ is the grid size and $C$ is a constant.

This method assumes that the energy production and dissipation of the small scales are in equilibrium - that is, $\epsilon$, the dissipation of kinetic energy.

# MPAS-A

1. Only horizontal mixing.

- `MPAS-Model/src/core_atmosphere/Registry.xml`,

```xml
<nml_option name="config_horiz_mixing" type="character" default_value="2d_smagorinsky"
        units="-"
        description="Formulation of horizontal mixing"
        possible_values="`2d_fixed' or `2d_smagorinsky'"/>

<nml_option name="config_len_disp" type="real" default_value="0.0" in_defaults="false"
        units="m"
        description="Horizontal length scale, used by the Smagorinsky formulation of horizontal diffusion and by 3-d divergence damping"
        possible_values="Positive real values. A zero value implies that the length scale is prescribed by the nominalMinDc value in the input file."/>


<nml_option name="config_smagorinsky_coef" type="real" default_value="0.125" in_defaults="false"
        units="-"
        description="Dimensionless empirical parameter relating the strain tensor to the eddy viscosity in the Smagorinsky turbulence model"
        possible_values="Real values typically in the range 0.1 to 0.4"/>

<var name="kdiff" type="real" dimensions="nVertLevels nCells Time" units="m^2 s^{-1}"
        description="Smagorinsky horizontal eddy viscosity"/>
```

- `MPAS-Model/src/core_atmosphere/dynamics/mpas_atm_time_integration.F`,

```Fortran
   ! Output: tend - tendencies: tend_u, tend_w, tend_theta and tend_rho
   !                these are all coupled-variable tendencies.
   !         various other quantities in diag: Smagorinsky eddy viscosity
......
   call mpas_pool_get_config(configs, 'config_smagorinsky_coef', c_s)
......

         ! Smagorinsky eddy viscosity, based on horizontal deformation (in this case on model coordinate surfaces).
         ! The integration coefficients were precomputed and stored in defc_a and defc_b

         if(config_horiz_mixing == "2d_smagorinsky") then
            do iCell = cellStart,cellEnd
               d_diag(1:nVertLevels) = 0.0
               d_off_diag(1:nVertLevels) = 0.0
               do iEdge=1,nEdgesOnCell(iCell)
                  do k=1,nVertLevels
                     d_diag(k)     = d_diag(k)     + defc_a(iEdge,iCell)*u(k,EdgesOnCell(iEdge,iCell))  &
                                                   - defc_b(iEdge,iCell)*v(k,EdgesOnCell(iEdge,iCell))
                     d_off_diag(k) = d_off_diag(k) + defc_b(iEdge,iCell)*u(k,EdgesOnCell(iEdge,iCell))  &
                                                   + defc_a(iEdge,iCell)*v(k,EdgesOnCell(iEdge,iCell))
                  end do
               end do
!DIR$ IVDEP
               do k=1, nVertLevels
                  ! here is the Smagorinsky formulation, 
                  ! followed by imposition of an upper bound on the eddy viscosity
                  kdiff(k,iCell) = min((c_s * config_len_disp)**2 * sqrt(d_diag(k)**2 + d_off_diag(k)**2),(0.01*config_len_disp**2) * invDt)
               end do
            end do

            h_mom_eddy_visc4   = config_visc4_2dsmag * config_len_disp**3
            h_theta_eddy_visc4 = h_mom_eddy_visc4

         else if(config_horiz_mixing == "2d_fixed") then

            kdiff(1:nVertLevels,cellStart:cellEnd) = config_h_theta_eddy_visc2
            h_mom_eddy_visc4 = config_h_mom_eddy_visc4
            h_theta_eddy_visc4 = config_h_theta_eddy_visc4

         end if
......
```

# WRF

1. sub-grid turbulence (constant K diffusion/ 2-D Smagorinsky/ predicted TKE /2-D, 6th order diffusion / Nonlinear Backscatter Anisotropic (NBA) sub-grid turbulence stress for LES )

- `run/README.namelist`,
```namelist
km_opt(max_dom) = 1,	! eddy coefficient option
                1 = constant (use khdif kvdif)
                2 = 1.5 order TKE closure (3D)
                3 = Smagorinsky first order closure (3D)
                    Note: option 2 and 3 are not recommended for DX > 2 km
                4 = horizontal Smagorinsky first order closure
                    (recommended for real-data cases)
c_s (max_dom) = 0.25     ! Smagorinsky coeff
```

## WRF-LES

WRF provides a capability for ideal LES simulation. Please take a look at the WRF/test/em_les/README.les to get some idea how it works.
Note that real-data LES simulation could be more complicated.

- `WRF/test/em_real/namelist.input.pbl-les`,
- `WRF/test/em_les/README.les` --> The ideal em_les test case


1. [(**Important**) | Overview of WRF Physics Boundary Layer and Turbulence | 2021Jimy Dudhia NCAR/MMM](https://www2.mmm.ucar.edu/wrf/users/tutorial/presentation_pdfs/202101/dudhia_physics_pbl_turbulence.pdf)
   1. P.26-
   2. For grid sizes of up to about 100 m, LES is preferable
   3. LES treats turbulence three-dimensionally instead of separate vertical (PBL) and horizontal diffusion schemes

# References

1. Skamarock, W. C., Klemp, J. B., Duda, M. G., Fowler, L. D., Park, S., & Ringler, T. D. (2012). A Multiscale Nonhydrostatic Atmospheric Model Using Centroidal Voronoi Tesselations and C-Grid Staggering. Monthly Weather Review, 140(9), 3090-3105. <https://doi.org/10.1175/MWR-D-11-00215.1>
   1. The horizontal filtering formulation of Smagorinsky (1963) uses the second-order Laplacians along with an eddy viscosity