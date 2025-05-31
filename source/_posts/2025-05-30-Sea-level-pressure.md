---
layout: post
title: Mean sea level pressure | 平均海平面氣壓
categories: [Meteorology]
tags: [NWP, MPAS, WRF]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-05-30 11:19:00
index_img: https://i.imgur.com/qJAq2VG.png
banner_img: https://i.imgur.com/qJAq2VG.png
---

# Detailed Mathematical Formulations of Mean Sea Level Pressure (MSLP)  

---

## 1. Fundamental Physical Basis 

### Hydrostatic Equation / 流體靜力平衡方程  
The vertical variation of atmospheric pressure is governed by the hydrostatic balance:  
大氣壓力隨高度變化遵循流體靜力平衡方程：  

$$
\frac{dP}{dz} = -\rho g
$$  

where:  

- $P$ = atmospheric pressure (Pa) 
- $z$ = geometric height (m) 
- $\rho$ = air density (kg/m³) 
- $g$ = gravitational acceleration (9.80665 $m/s^2$) 

---

### Ideal Gas Law 
Air density relates to pressure and temperature as:  
  
$$
P = \rho R_d T_v
$$  

where:  

- $R_d$ = specific gas constant for dry air (~287.05 J/(kg·K)) / 乾空氣氣體常數  
- $T_v$ = virtual temperature (K), accounts for moisture / 虛溫，考慮濕度影響  

The **specific gas constant of a gas** (dry gas) or a mixture of gases ($R_d$) is given by the molar gas constant divided by **the molar mass ($M$)** of the gas or mixture:

$$
R_d = \dfrac{R}{M}
$$

where

- molar mass of air (dry) $M$ = $28.964917$ g/mol
- $R$ = $8.314$ J/(K mol).

---

## 2. Derivation of Mean Sea Level Pressure Equation 

Substituting $\rho$ into the hydrostatic equation:  

$$
\frac{dP}{dz} = -\frac{P}{R_d T_v} g
$$

Rearranged differential form:   

$$
\frac{dP}{P} = -\frac{g}{R_d T_v} dz
$$

---

### Assuming a linear temperature lapse rate 

If virtual temperature decreases linearly with height:  
 

$$
T_v(z) = T_0 - \gamma z
$$  

where:  

- $T_0$ = virtual temperature at surface (K) / 表面虛溫  
- $\gamma$ = lapse rate (K/m), typically 0.0065 K/m / 溫度遞減率（約 $0.0065$ K/m）  

---

### Integrating from station height $z$ to sea level$0$/ 從測站高度積分至海平面 

Integrate pressure from station pressure $P$ at height$z$down to sea level pressure $P_0$:  

$$
\int_{P}^{P_0} \frac{dP'}{P'} = -\int_{z}^{0} \frac{g}{R_d (T_0 - \gamma z')} dz'
$$

Solving the integral:  
$$
\ln \left(\frac{P_0}{P}\right) = \frac{g}{R_d \gamma} \ln \left( \frac{T_0}{T_0 - \gamma z} \right)
$$

Exponentiating both sides:  

$$
P_0 = P \left( \frac{T_0}{T_0 - \gamma z} \right)^{\frac{g}{R_d \gamma}}
$$

This is the **standard formula** to reduce station pressure to mean sea level pressure.  (此為標準海平面氣壓換算公式。)

---

## 3. Virtual Temperature Calculation / 虛溫計算

Virtual temperature accounts for moisture:  
虛溫考慮濕度影響：  

$$
T_v = T (1 + 0.608 q)
$$  
where:  

- $T$ = actual air temperature (K) / 實際氣溫  
- $q$ = specific humidity (kg/kg) / 比濕  

---

## 4. Simplified Approximate Form / 簡化近似形式

For small heights (less than ~1000 m), a linear approximation is often used:  
對低海拔（<1000米），可用線性近似：  

$$
P_0 \approx P + \rho g z
$$

Using ideal gas law:  

$$
P_0 \approx P + \frac{P}{R_d T_v} g z
$$

---

## 5. Other Important Equations / 其他重要方程

### Pressure Height Equation / 氣壓高度方程  
Given pressure difference, height difference can be calculated as:  

$$
h = \frac{R_d \overline{T_v}}{g} \ln \left(\frac{P_1}{P_2}\right)
$$  

where $\overline{T_v}$ is the mean virtual temperature between two pressure levels.

---

### QNH (Aviation Mean Sea Level Pressure) / 航空用QNH計算  
QNH is the local mean sea level pressure used to calibrate altimeters:  

$$
\text{QNH} = P \times \left(1 - \frac{L \cdot z}{T_0}\right)^{\frac{g}{R_d L}}
$$  

where:  

- $L$= lapse rate (0.0065 K/m)  
- $z$= station elevation (m)  
- $T_0$= standard temperature at sea level (288.15 K)  

---

## 6. Summary Table of Key Variables / 主要變數總結

| Symbol | Meaning                    | Typical Value / Range       |
|--------|----------------------------|----------------------------|
| $P$  | Pressure at station (Pa)   | Variable                   |
| $P_0$ | Mean sea level pressure (Pa) | ~101325 Pa (standard)      |
| $z$  | Station elevation (m)      | 0 - several thousand meters|
| $T$  | Air temperature (K)        | ~250 - 320 K               |
| $T_v$ | Virtual temperature (K)    | Slightly higher than $T$ |
| $\gamma$ | Lapse rate (K/m)          | ~0.0065 K/m                |
| $g$  | Gravity acceleration (m/s²) | 9.80665                    |
| $R_d$ | Gas constant for dry air (J/kg·K) | 287.05                    |
| $q$  | Specific humidity (kg/kg)  | 0 - 0.03                   |

---

# Correcting the Sea Level Pressure Equation

- [【科学漫谈】关于气压订正技术的尘封往事](https://www.cma.gov.cn/2011xwzx/2011xqxxw/2011xqxyw/201410/t20141026_264967.html)
  - 那麼，韋中達是如何修訂氣壓訂正公式的呢？
  - 其實，他提供的方法簡單又合理：**氣壓訂正時要用到當時的測站溫度，他提出，改用最近24小時的平均溫度（這在無形中消除了溫度的日變化）代替觀測時的溫度，再做訂正計算就可以了**。西北地區溫度的日變化很大。
  - **世界氣象組織建議的計算方法，對於海拔不高、溫度日變化不大的情況是適用的**。但是遇到氣象站海拔高而溫度日變化又大的情況時，即便本站氣壓變化不大，但是溫度的日變化就可以導致訂正過的海平面氣壓有很大的虛假的日變化。而韋中達的辦法巧妙地消除了這個不合理因素。

# WRF

- phys/module_diag_trad_fields.F

```fortran
! Mean sea level pressure
sealevelp(i,j) =   MSLP ( ht(i,j), pressure(i,kms,j), geoheight(i,kms,j) , &
                            qv(i,kms,j), temperature(i,kms,j) )
```

- phys/module_diag_functions.F

{% fold info @MSLP %}
```fortran
  FUNCTION MSLP ( zsfc, psfc, zlev1, qlev1, tlev1 )

      implicit none
     
     
!     DECLARE VARIABLES

      REAL,    INTENT ( IN )  :: zsfc         !~ Surface height ( m )
      REAL,    INTENT ( IN )  :: psfc         !~ Surface height ( m )
      REAL,    INTENT ( IN )  :: zlev1        !~ Level 1 height ( m )
      REAL,    INTENT ( IN )  :: qlev1        !~ Level 1 mixing ratio ( kg/kg )
      REAL,    INTENT ( IN )  :: tlev1        !~ Level 1 temperature ( K )
      real,PARAMETER :: G=9.81
      real,PARAMETER :: GI=1./G
      real,PARAMETER :: RD=287.0
      real,PARAMETER :: ZSL=0.0
      real,PARAMETER :: TAUCR=RD*GI*290.66,CONST=0.005*G/RD
      real,PARAMETER :: GORD=G/RD,DP=60.E2
      real,PARAMETER :: GAMMA=6.5E-3

      real MSLP,TVRT,TVRSFC,TAUSFC,TVRSL,TAUSL,TAUAVG
!    
!**********************************************************************
!     START NGMSLP HERE.
!
         MSLP = PSFC
!
!        COMPUTE LAYER TAU (VIRTUAL TEMP*RD/G).
         TVRT = TLEV1*(1.0+0.608*QLEV1)
         !TAU  = TVRT*RD*GI
!    
!        COMPUTE TAU AT THE GROUND (Z=ZSFC) AND SEA LEVEL (Z=0)
!        ASSUMING A CONSTANT LAPSE RATE OF GAMMA=6.5DEG/KM.
         TVRSFC = TVRT + (ZLEV1 - ZSFC)*GAMMA
         TAUSFC = TVRSFC*RD*GI
         TVRSL  = TVRT + (ZLEV1 - ZSL)*GAMMA
         TAUSL  = TVRSL*RD*GI
!    
!        IF NEED BE APPLY SHEULL CORRECTION.
         IF ((TAUSL.GT.TAUCR).AND.(TAUSFC.LE.TAUCR)) THEN
            TAUSL=TAUCR
         ELSEIF ((TAUSL.GT.TAUCR).AND.(TAUSFC.GT.TAUCR)) THEN
            TAUSL = TAUCR-CONST*(TAUSFC-TAUCR)**2
         ENDIF
!    
!        COMPUTE MEAN TAU.
         TAUAVG = 0.5*(TAUSL+TAUSFC)
!    
!        COMPUTE SEA LEVEL PRESSURE.
         MSLP = PSFC*EXP(ZSFC/TAUAVG)

  END FUNCTION MSLP
```
{% endfold %}

# MPAS-A

- src/core_atmosphere/diagnostics/mpas_isobaric_diagnostics.F

{% fold info @mpas_isobaric_diagnostics.F %}
```fortran
logical :: need_mslp, &

    need_mslp = MPAS_field_will_be_written('mslp')
    need_any_diags = need_any_diags .or. need_mslp
    real (kind=RKIND), dimension(:), pointer :: mslp
    call mpas_pool_get_array(diag, 'mslp', mslp)
    if (NEED_TEMP .or. NEED_RELHUM .or. NEED_DEWPOINT .or. need_mslp) then
    
    if (need_mslp) then
        !... compute SLP (requires temp, height, pressure, qvapor)
            call compute_slp(nCells, nVertLevels, num_scalars, temperature, height, pressure, index_qv, scalars, mslp)
            mslp(:) = mslp(:) * 100.0   ! Convert from hPa to Pa
        !   mslp(iCell) = diag % surface_pressure % array(iCell) + 11.38*height(1,iCell)
        !   mslp(iCell) = mslp(iCell)/100.
            call mpas_log_write('*** MSLP field will not be computed')
            call mpas_log_write('*** MSLP field will not be computed')
```
{% endfold %}

{% fold info @compute_slp %}
```fortran

    subroutine compute_slp(ncol,nlev_in,nscalars,t,height,p,index_qv,scalars,slp)
   
       implicit none
   
      !input arguments:
       integer, intent(in) :: ncol, nlev_in, nscalars
      
      !p: in mb
      !t: in K
      !scalars: in kg/kg
      !height: in m
       real(kind=RKIND), intent(in), dimension(nlev_in,ncol) :: p,t
       real(kind=RKIND), intent(in), dimension(nlev_in+1,ncol) :: height
       integer, intent(in) :: index_qv
       real(kind=RKIND), intent(in), dimension(nscalars,nlev_in,ncol) :: scalars
      
      !output arguments:
       real(kind=RKIND), intent(out), dimension(ncol) :: slp
      
      !local variables:
       integer :: icol, k, kcount
       integer :: klo, khi
      
       real(kind=RKIND) :: gamma, rr, grav
       parameter (rr=287.0, grav=9.80616, gamma=0.0065)
      
       real(kind=RKIND) :: tc, pconst
       parameter (tc=273.16+17.5, pconst=100.)
      
       logical mm5_test
       parameter (mm5_test=.true.)
      
       integer, dimension(:), allocatable :: level
       real(kind=RKIND), dimension(:), allocatable :: t_surf, t_msl
       real(kind=RKIND) :: plo , phi , tlo, thi , zlo , zhi
       real(kind=RKIND) :: p_at_pconst , t_at_pconst , z_at_pconst, z_half_lowest
      
       logical :: l1, l2, l3, found
      
      ! Find least zeta level that is PCONST Pa above the surface.  We later use this
      ! level to extrapolate a surface pressure and temperature, which is supposed
      ! to reduce the effect of the diurnal heating cycle in the pressure field.
      
       if (.not.allocated(level))  allocate(level(ncol))
       if (.not.allocated(t_surf)) allocate(t_surf(ncol))
       if (.not.allocated(t_msl))  allocate(t_msl(ncol))
      
       do icol = 1 , ncol
          level(icol) = -1
      
          k = 1
          found = .false.
          do while ( (.not. found) .and. (k.le.nlev_in))
                if ( p(k,icol) .lt. p(1,icol)-pconst ) then
                   level(icol) = k
                   found = .true.
                end if
                k = k+1
          end do
      
          if ( level(icol) .eq. -1 ) then
             call mpas_log_write('Troubles finding level $r above ground.', realArgs=(/pconst/))
             call mpas_log_write('Problems first occur at ($i)', intArgs=(/icol/))
             call mpas_log_write('Surface pressure = $r hPa.', realArgs=(/p(1,icol)/))
             call mpas_log_write('*** MSLP field will not be computed')
             slp(:) = 0.0
             return
          end if
      
       end do
      
      ! Get temperature PCONST hPa above surface.  Use this to extrapolate
      ! the temperature at the surface and down to sea level.
      
       do icol = 1 , ncol
      
          klo = max ( level(icol) - 1 , 1      )
          khi = min ( klo + 1        , nlev_in - 1 )
      
          if ( klo .eq. khi ) then
             call mpas_log_write('Trapping levels are weird.')
             call mpas_log_write('icol = $i', intArgs=(/icol/))
             call mpas_log_write('klo = $i, khi = $i: and they should not be equal.', intArgs=(/klo,khi/))
             call mpas_log_write('*** MSLP field will not be computed')
             slp(:) = 0.0
             return
          end if
      
          plo = p(klo,icol)
          phi = p(khi,icol)
          tlo = t(klo,icol) * (1. + 0.608 * scalars(index_qv,klo,icol))
          thi = t(khi,icol) * (1. + 0.608 * scalars(index_qv,khi,icol))
          zlo = 0.5*(height(klo,icol)+height(klo+1,icol))
          zhi = 0.5*(height(khi,icol)+height(khi+1,icol))
      
          p_at_pconst = p(1,icol) - pconst
          t_at_pconst = thi-(thi-tlo)*log(p_at_pconst/phi)*log(plo/phi)
          z_at_pconst = zhi-(zhi-zlo)*log(p_at_pconst/phi)*log(plo/phi)
      
          t_surf(icol) = t_at_pconst*(p(1,icol)/p_at_pconst)**(gamma*rr/grav)
          t_msl(icol) = t_at_pconst+gamma*z_at_pconst
      
       end do
      
      ! If we follow a traditional computation, there is a correction to the sea level
      ! temperature if both the surface and sea level temnperatures are *too* hot.
      
       if ( mm5_test ) then
          kcount = 0
          do icol = 1 , ncol
                l1 = t_msl(icol) .lt. tc
                l2 = t_surf(icol) .le. tc
                l3 = .not. l1
                if ( l2 .and. l3 ) then
                   t_msl(icol) = tc
                else
                   t_msl(icol) = tc - 0.005*(t_surf(icol)-tc)**2
                   kcount = kcount+1
                end if
          end do
      !   call mpas_log_write('These number of points had t_msl adjusted $i', intArgs=(/kcount/))
       end if
      
       do icol = 1 , ncol
          z_half_lowest=0.5*(height(1,icol)+height(2,icol))
          slp(icol) = p(1,icol) * exp((2.*grav*z_half_lowest)/ &
                                    (rr*(t_msl(icol)+t_surf(icol))))
       end do
      
       if (allocated(level))  deallocate(level)
       if (allocated(t_surf)) deallocate(t_surf)
       if (allocated(t_msl))  deallocate(t_msl)
   
    end subroutine compute_slp
```
{% endfold %}


# 全球多年平均海平面氣壓圖

- <https://psl.noaa.gov/mddb2/makePlot.html?variableID=158395&fileID=1449936>
  - 從NOAA官網上直接用了再分析數據在線上匯出了一月與七月的全球多年平均海平面氣壓圖

{% fold info @全球多年平均海平面氣壓圖 %}
```sh
#
# for mslp, long term monthly mean
#

if [[ 1 ]]; then
    for MM in $(seq 1 1 12); do
        tmp_MM=$(printf "%02d" ${MM})
        wget "https://psl.noaa.gov/cgi-bin/mddb2/plot.pl?doplot=1&varID=158395&fileID=1449936&itype=2&variable=slp&levelType=Sea%20Level&level_units=&level=Sea%20Level&timetype=mon&fileTimetype=mon&year1=3810&month1=${MM}&day1=2&hr1=00%20Z&year2=3810&month2=1&day2=2&hr2=00%20Z&vectorPlot=0&contourLevel=auto&cint=4&lowr=0&highr=200&colormap=default&reverseColormap=no&contourlines=1&colorlines=1&contourfill=1&contourlabels=1&removezonal=0&boundary=Geophysical&projection=CylindricalEquidistant&region=All&area_north=90&area_west=0&area_east=360&area_south=-90&centerLat=0.0&centerLon=270.0&mapfill=0" -O mslp_MM${tmp_MM}.png
    done
fi
```
{% endfold %}

![](https://i.imgur.com/kFvyZQr.png)
![](https://i.imgur.com/qJAq2VG.png)

- 可以看到，南極地區並不是低壓而是一直處於**相對高壓**之中，這跟高中課本上講的極地高壓也是吻合的！
- 不過從這兩張圖裡面，我們也可以看到南北半球的一些區別，比如說南半球靠近極地的地區氣壓梯度非常大，這是由於南半球靠近極地的地方海陸的緯向分佈非常均一。
- **簡單的三圈環流模型是建立在地球下墊面均一的基礎上的**，在這種情況下我們可以得到與維圈平行的各個風帶、氣壓帶。但是在現實中，**海陸分佈的不均一性進而引起下墊面熱力性質的差異**，導致在理想的氣壓帶分佈的基礎上，夏季時陸地氣壓更低、海洋氣壓更高；冬季時陸地氣壓更高、海洋氣壓更低。
- 海陸的緯向分佈如果不均勻到一定程度，甚至會導致原來的平行氣壓帶根本就不太明顯（如北半球）。但是在南半球靠近極地的地方，基本上同一個緯度基本上呢不是海洋（南太平洋、南印度洋、南大西洋）就是大塊的陸地（南極大陸），這種分佈有利於不同緯度形成穩定的平均氣壓差，所以我們就可以看到60°S那一塊基本上就是副極地低壓，而南極基本上就是高壓。而在七月（南半球冬季），60°S的海洋加劇了副極地低壓、南極大陸加劇了極地高壓，**氣壓梯度就更加明顯，甚至形成了非常強的風帶－（南極）極渦**。

# References

1. [为什么南极的海平面气压值低于其他地方？ - mosekyo的回答 - 知乎](https://www.zhihu.com/question/285571457/answer/963558274)