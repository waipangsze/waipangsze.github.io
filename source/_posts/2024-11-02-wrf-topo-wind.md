---
layout: post
title: WRF | topo-wind
categories: [WRF]
tags: [WRF,MPAS,NWP]
author: wpsze
date: 2024-11-02 13:34:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/cOWLDX0.png
banner_img: https://i.imgur.com/cOWLDX0.png
---

# Introduction 

- The Developmental Testbed Center WRFv3.4.1+ Surface Drag Parameterization Sensitivity Test Plan Points of Contact: Hongli Jiang, Michelle Harrold and Jamie Wolff November 28, 2012 

It is widely acknowledged that the Weather Research and Forecasting (WRF) model has a high surface wind speed bias, especially over plains and valleys. In recent versions of WRF, two new surface drag parameterization schemes, **both associated with the Yonsei University (YSU) planetary boundary layer (PBL) scheme**, have been developed. The Developmental Testbed Center (DTC) will be performing testing and evaluation of three WRF model configurations with the Advanced Research WRF (ARW) core (Skamarock et al. 2008). The baseline configuration will be run with the physics suite being used in the ARW High-Resolution Window (HIRESW) forecast system being run operationally at the National Centers for Environmental Prediction (NCEP). The two comparative configurations will be testing the effects of the surface drag parameterization scheme namelist option, topo_wind, which aims to correct the high wind bias seen in WRF. One configuration will be run with **topo_wind=1 (TWIND1; Jimenez and Dudhia 2011)**, which is based on the concept of a momentum sink term and makes use of the standard deviation of the subgrid-scale orography as well as the Laplacian of the topographic field. The second configuration will be run with **topo_wind=2 (TWIND2; Mass and Ovens 2012)**, which determines the subgrid terrain variance and makes the surface drag or roughness used in the model dependent on it; it also includes additional consideration for stability and wind speed. The baseline configuration will have **topo_wind=0 (turned off)**. These runs will be cold start cases initialized every 36 hours and run out to 48 hours for one full year. 

The Weather Research and Forecasting (WRF)model (Skamarock et al. 2008) has presented a **high surface wind speed bias over land** since the early versions of the model (Cheng and Steenburgh 2005). The bias still persists in more recent versions (e.g., Bernardet et al. 2005; Roux et al. 2009; Mass and Ovens 2010, 2011) and represents a limitation for the high demand of accurate surface wind estimations by different sectors such as wind-energy applications or air-quality studies.

- The wind speed is higher in plains and valleys,
- The wind speed is lower in mountains and hills.

WRF模式對風速變化趨勢模擬較好，但是風速值偏差較大，主要是因為 WRF 沒有對次網格地形阻力進行參數化，致使

{% note primary %}
- 平原和山谷風速偏大，
- 高山和丘陵風速偏小。
{% endnote %}

從WRFv3.4開始，WRF提供了與YSU PBL方案相關聯的TopoWind模式，以改善地形對近地面風的影響。 YSU PBL方案共有三種方案：

- topo_wind = 0，在近地面風計算中不包括TopoWind模式的附加地形效應；
- topo_wind = 1，將亞柵格尺度地形的標準差納入近地面風計算；
- topo_wind = 2，採用與topowind = 1 相同的方法計算近地面風，但同時透過加入亞格點地形變化加強了摩擦速度的計算。

# WRF 

- topo-wind=1 → varsso (Jimenez 2011)
- topo-wind=2 → var/var2d (Mass 2012)
  
and geog data,

- varsso=0.924km (0.5m)
- varsso_2m=3.7km
- varsso_5m=9.25km
- varsso_10m=18.5km


# Jiménez 2011

- Jiménez, P. A., & Dudhia, J. (2012). Improving the representation of resolved and unresolved topographic effects on surface wind in the WRF model. Journal of Applied Meteorology and Climatology, 51(2), 300-316.

- u = stands for the zonal wind component **at the first model level**

![The differentiation has been accomplished by applying the following operator to the topographic field $h$](https://i.imgur.com/JrR5M9h.png){width=600}
![](https://i.imgur.com/3r4P6Uh.png){width=600}
![](https://i.imgur.com/1lSVGvw.png){width=600}
![](https://i.imgur.com/4F3m0BK.png){width=600}

# Mass and Ovens 2012

topo_wind=2 (TWIND2; Mass and Ovens 2012)

- Fixing WRF's High Speed Wind Bias: A New Subgrid Scale Drag Parameterization and the Role of Detailed Verification (91st American Meteorological Society Annual Meeting)
  - <https://ams.confex.com/ams/91Annual/webprogram/Paper180011.html>
- <https://www2.mmm.ucar.edu/wrf/users/workshops/WS2010/presentations/session%204/4-1_WRFworkshop2010Final.pdf>


# WRF code

- WRF/dyn_em/start_em.F

```fortran
!
! paj: computes ct and ct2 for topo_wind
!
       grid%ctopo=1.
       grid%ctopo2=1.
!
       if (config_flags%topo_wind.eq.1) then
         DO j = jts,min(jte,jde-1)
         DO i = its, min(ite,ide-1)
            if(grid%xland(i,j).lt.1.5)grid%ctopo(i,j)=sqrt(grid%var_sso(i,j))
            if (grid%ctopo(i,j).le.2.718) then
            grid%ctopo(i,j)=1.
            else
            grid%ctopo(i,j)=alog(grid%ctopo(i,j))
            endif
!
            if (grid%lap_hgt(i,j).gt.-10.) then
              grid%ctopo(i,j)=grid%ctopo(i,j)
            else
               if (grid%lap_hgt(i,j).ge.-20) then
                 alpha=(grid%lap_hgt(i,j)+20.)/10.
                 grid%ctopo(i,j)=alpha*grid%ctopo(i,j)+(1-alpha)
               else
                  if (grid%lap_hgt(i,j).ge.-30.) then
                  grid%ctopo(i,j)=(grid%lap_hgt(i,j)+30.)/10.
                  grid%ctopo2(i,j)=(grid%lap_hgt(i,j)+30.)/10.
                  else
                  grid%ctopo(i,j)=0.
                  grid%ctopo2(i,j)=0.
                  endif
               endif
            endif
         ENDDO
         ENDDO
!       if topo_wind==2 (D.Ovens, C.Mass)
       else if (config_flags%topo_wind.eq.2) then
         DO j = jts,min(jte,jde-1)
         DO i = its, min(ite,ide-1)
           if (grid%xland(i,j).lt.1.5) then
              vfac = amin1(1.575,(grid%var2d(i,j)*0.4/200.+1.175))
              vfac = vfac * vfac
           else                      ! over water, leave it alone
              vfac = 1.
           endif
!          print *, j,i,grid%ctopo(i,j),vfac
           grid%ctopo(i,j)=grid%ctopo(i,j)*vfac
         ENDDO
         ENDDO
       endif
```
- Versions of WPS prior to V3.4 did not include VAR_SSO static data. This is necessary for running the topo_wind option in WRF (also introduced in version 3.4). [link](https://github.com/wrf-model/WRF/pull/10)
- Remark, **ctopo2 = 1** in topo_wind2.

{% note primary %}
u10, v10 (no modify in topo_wind=2)\
Modify u10, v10 ⇒ Effective in topo_wind=1 only\
In topo_wind=2, No Modify u10, v10
{% endnote %}

## convective velocity and PBL height

- <https://github.com/wrf-model/WRF/pull/116>
- WRFv3.8.1 and before, don't consider convective velocity and PBL height
- It is found that this scheme can improve the simulated surface winds, especially at night, **but it can underestimate the winds during daytime.** 
  - Lorente-Plazas, R., Jiménez, P. A., Dudhia, J., & Montávez, J. P. (2016). Evaluating and Improving the Impact of the Atmospheric Stability and Orography on Surface Winds in the WRF Model. Monthly Weather Review, 144(7), 2685-2693. <https://doi.org/10.1175/MWR-D-15-0449.1>
  - <https://journals.ametsoc.org/view/journals/mwre/144/7/mwr-d-15-0449.1.xml>
- In the daytime,
  - if higher PBLH, then consider less ctopo effect 
  - new_topo ~ off_ctopo in daytime
- Uses convective velocity and PBL height to determine instability
- Extra code is added to compute tke, hybrid PBL height and convective velocity within the YSU PBL

```text
TYPE: enhancement

KEYWORDS: topo_wind=1, diurnal cycle improvement, YSU PBL only

SOURCE: internal + Raquel Lorente (formerly RAL) and Pedro Jimenez (RAL)

PURPOSE: To improve diurnal behavior of surface wind when topo_wind=1 is
activated. This replaces the topo_wind=1 and is not a new option.

DESCRIPTION OF CHANGES:

Follows paper Lorente-Plazas et al. (2016, MWR) by decreasing effects of
topo_wind=1 option when unstable boundary-layer conditions prevail. Uses
convective velocity and PBL height to determine instability. PBL height uses a
hybrid tke-theta method copied from the MYNN PBL, but for YSU tke is computed
diagnostically.
Extra code is added to compute tke, hybrid PBL height and convective velocity
within the YSU PBL, so this is the only module affected.

LIST OF MODIFIED FILES :

M phys/module_bl_ysu.F
```

## module_bl_ysu.F

- <https://github.com/wrf-model/WRF/blob/v4.5/phys/module_bl_ysu.F>

- ! paj: ctopo=1 if topo_wind=0 (default)

```fortran
   real(kind=kind_phys),     dimension( its: )                               , &
             optional                                                        , &
             intent(in   )   ::                                         ctopo, &
                                                                       ctopo2
```

```fortran
!--- end of paj tke
! compute vconv
!      Use Beljaars over land
        if (xland(i).lt.1.5) then
        fluxc = max(sflux(i),0.0)
        vconvc=1.
        VCONV = vconvc*(g/thvx(i,1)*pblh_ysu(i)*fluxc)**.33
        else
! for water there is no topo effect so vconv not needed
        VCONV = 0.
        endif
        vconvfx(i) = vconv
!raquel
!ctopo stability correction
      fric(i,1)=ust(i)**2/wspd1(i)*rhox(i)*g/del(i,1)*dt2         &
        *(wspd1(i)/wspd(i))**2
      if(present(ctopo)) then
        vconvnew=0.9*vconvfx(i)+1.5*(max((pblh_ysu(i)-500)/1000.0,0.0))
        vconvlim = min(vconvnew,1.0)
        ad(i,1) = 1.+fric(i,1)*vconvlim+ctopo(i)*fric(i,1)*(1-vconvlim)
        ad(i,1) = ad(i,1) - bepswitch*frc_urb1d(i)* &
                     (fric(i,1)*vconvlim+ctopo(i)*fric(i,1)*(1-vconvlim))
!       ad(i,1) = 1.+(1.-bepswitch*frc_urb1d(i))* &
!                    (fric(i,1)*vconvlim+ctopo(i)*fric(i,1)*(1-vconvlim))
      else
        ad(i,1) = 1.+fric(i,1)
      endif
     f1(i,1) = ux(i,1)+uoxl(i)*ust(i)**2*rhox(i)*g/del(i,1)*dt2/wspd1(i)*(wspd1(i)/wspd(i))**2
     f2(i,1) = vx(i,1)+voxl(i)*ust(i)**2*rhox(i)*g/del(i,1)*dt2/wspd1(i)*(wspd1(i)/wspd(i))**2
   enddo
```

- ux=u3d(ims,kms,j),vx=v3d(ims,kms,j)

```fortran
!
! paj: ctopo2=1 if topo_wind=0 (default)
!
   do i = its,ite
     if(present(ctopo).and.present(ctopo2)) then ! mchen for NMM
       u10(i) = ctopo2(i)*u10(i)+(1-ctopo2(i))*ux(i,1)
       v10(i) = ctopo2(i)*v10(i)+(1-ctopo2(i))*vx(i,1)
     endif !mchen
   enddo
```

# References

1. Topo Wind Intercomparison
   1. <https://dtcenter.ucar.edu/eval/meso_mod/topo_wind/>
   2. Topo Wind Intercomparsion Test
   3. Statistically/Practically Significant Table Reports
   4. Publications
      1. Investigating the impact of surface parameterization schemes available in WRF on surface winds
      2. Investigating seasonal and regional differences if the WRF-ARW HIRES Window physics suite
2. Lorente-Plazas, R., Jiménez, P. A., Dudhia, J., & Montávez, J. P. (2016). Evaluating and Improving the Impact of the Atmospheric Stability and Orography on Surface Winds in the WRF Model. Monthly Weather Review, 144(7), 2685-2693. <https://doi.org/10.1175/MWR-D-15-0449.1>