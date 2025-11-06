---
layout: post
title: WRF | ERA5 IC | SMOIS issue
categories: [WRF]
tags: [WRF, NWP, ERA5, SMOIS]
author: wpsze
date: 2025-07-28 07:56:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# Issue

When using the Weather Research and Forecasting (`WRF`) model with initial conditions from `ERA5`, the soil moisture (`smois`) in the desert region is replaced with the **maximum soil moisture** (`MAXSMC`) from a lookup table `SOILPARM.TBL`. This substitution leads to notably wetter conditions in that area compared to using the original soil moisture values. What is the rationale behind this replacement?

![](https://i.imgur.com/NuR9m3F.png)

# `module_sf_noahlsm.F`

- <https://github.com/wrf-model/WRF/blob/f52c197ed39d12e087d02c50f412d90d418f6186/phys/module_sf_noahlsm.F#L4026C1-L4026C50>
  - **SMC(NSOIL)  TOTAL SOIL MOISTURE CONTENT (VOLUMETRIC FRACTION)**
  - **SMC (K) = MAX ( MIN (STOT,SMCMAX),0.02 )**

```fortran
! ----------------------------------------------------------------------
! SUM THE PREVIOUS SMC VALUE AND THE MATRIX SOLUTION TO GET A
! NEW VALUE.  MIN ALLOWABLE VALUE OF SMC WILL BE 0.02.
! RUNOFF3: RUNOFF WITHIN SOIL LAYERS
! ----------------------------------------------------------------------
      WPLUS = 0.0
      RUNOFF3 = 0.

      DDZ = - ZSOIL (1)
      DO K = 1,NSOIL
         IF (K /= 1) DDZ = ZSOIL (K - 1) - ZSOIL (K)
         SH2OOUT (K) = SH2OIN (K) + CI (K) + WPLUS / DDZ
         STOT = SH2OOUT (K) + SICE (K)
         IF (STOT > SMCMAX) THEN
            IF (K .eq. 1) THEN
               DDZ = - ZSOIL (1)
            ELSE
               KK11 = K - 1
               DDZ = - ZSOIL (K) + ZSOIL (KK11)
            END IF
            WPLUS = (STOT - SMCMAX) * DDZ
         ELSE
            WPLUS = 0.
         END IF
         SMC (K) = MAX ( MIN (STOT,SMCMAX),0.02 )
         SH2OOUT (K) = MAX ( (SMC (K) - SICE (K)),0.0)
      END DO
```

## Check: Set `MAXSMC` = 0.900

- Set `MAXSMC` = 0.900

![](https://i.imgur.com/32OxOTR.png){width=400}

## Check: Set `MAXSMC` = 0.900

- Set `MAXSMC` = 0.01

![](https://i.imgur.com/qzJQtzZ.png){width=400}

# Gemini-Answers 1

The analysis of `module_sf_noahdrv.F` reveals that it functions as the **driver module** for the Noah Land Surface Model (LSM) in WRF. It handles input/output and coordinates the calculation, but the actual logic for updating the soil moisture variable, **`smois`**, occurs within a lower-level physics subroutine.

The update to **`smois`** (soil moisture volume fraction) follows these three steps:

### 1. The Core Update Subroutine

The prognostic physics calculation is performed within the Noah LSM core, typically found in `module_sf_noahlsm.F`. The specific subroutine responsible for calculating the soil water budget and updating `smois` is **`SMFLX`** (Soil Moisture Flux).

### 2. The Governing Equation

The `smois` array is updated by solving the one-dimensional soil water budget equation for each soil layer ($i$) over the LSM time step ($\Delta t$). This equation conserves mass (water) in the soil column:

$$\text{SMOIS}_i^{t+\Delta t} = \text{SMOIS}_i^t + \frac{\Delta t}{\Delta z_i \cdot \rho_w} \left[ \text{Flux}_{\text{top}} - \text{Flux}_{\text{bottom}} - E_i \right]$$

Where:

* $\text{SMOIS}_i$ is the volumetric soil moisture of layer $i$.
* $\Delta z_i$ is the thickness of layer $i$.
* $\rho_w$ is the density of water.
* $\text{Flux}_{\text{top}}$ and $\text{Flux}_{\text{bottom}}$ are the moisture fluxes (per unit area) entering and leaving the soil layer, respectively.
* $E_i$ is the total water sink from layer $i$ due to evaporation and root extraction (transpiration).

### 3. The Flux Terms That Update `smois`

The **`smois`** value for each soil layer is updated based on the integration of these water fluxes:

1.  **Surface Flux (Top Layer Update):** The top soil layer is updated by **precipitation** (including snowmelt) and losses due to **surface runoff** ($\text{SFCRUNOFF}$) and total **evapotranspiration** ($\text{ET}$, which includes canopy evaporation, bare soil evaporation, and transpiration). The net infiltration rate dictates the water gain.
2.  **Inter-Layer Fluxes:** Water is moved between the internal soil layers ($i=1, 2, \dots, N$) via **hydraulic redistribution** governed by the **Darcy-Buckingham law**. This flux is driven by the gradient of the soil water potential, which is calculated based on the soil moisture content (`smois`) and the soil's hydraulic properties (hydraulic conductivity $K(\theta)$ and matric potential $\Psi(\theta)$). This includes a correction for the effect of frozen soil on hydraulic conductivity.
3.  **Bottom Flux (Bottom Layer Update):** The deepest layer's moisture is lost via **subsurface runoff** or **drainage** ($\text{UDRUNOFF}$) at the bottom of the soil column. The specific formulation depends on the physics option chosen for deep drainage (e.g., free drainage or a simple groundwater model).

# Gemini-Answers 2

The initialization of the soil moisture variable, **SMOIS** (volumetric total soil moisture), in the Weather Research and Forecasting (WRF) model for a real-data case is primarily handled by the **WRF Preprocessing System (WPS)** and the **`real.exe`** program.

Here is the step-by-step process:

***

### 1. The WRF Preprocessing System (WPS)

The WPS prepares the initial and boundary condition data for WRF.

* **`ungrib.exe`**: This program extracts meteorological variables, including soil moisture and soil temperature, from GRIB-formatted large-scale model data (e.g., GFS, ERA5, etc.). The soil moisture data in the source files are the primary input for SMOIS.
* **`metgrid.exe`**: This program horizontally interpolates the extracted meteorological data (which now includes the soil moisture data) from the large-scale model grid onto the WRF simulation domains defined by `geogrid.exe`. The output of this step is a series of **`met_em*`** NetCDF files, which contain the horizontally interpolated **SMOIS** data at the initial time and subsequent boundary times.

***

### 2. The `real.exe` Program

The `real.exe` program takes the WPS output (`met_em*` files) and prepares the final initial conditions file (`wrfinput_d0*`).

* **Reading and Vertical Interpolation**: `real.exe` reads the initial time slice of the **SMOIS** variable from the `met_em*` files. It performs **vertical interpolation** to map the soil layers from the source data to the soil layers specified in the WRF Land Surface Model (LSM) being used (e.g., Noah, RUC, etc.). Note that in some versions and setups, the layer order for soil variables like SMOIS and soil temperature may be flipped between the `met_em` and `wrfinput` files during this process.
* **Final Output**: The resulting initial soil moisture values are written as the **SMOIS** variable into the `wrfinput_d0*` file. This value represents the volumetric total soil moisture (liquid and frozen) in each soil layer at the start of the simulation.

***

### Key Points:

* **Initial Time Only**: WRF is typically initialized using the soil moisture data *only* at the initial time step from the large-scale forcing. During the WRF simulation (`wrf.exe`), **SMOIS** becomes a **prognostic variable** that is predicted and integrated forward in time by the chosen Land Surface Model (LSM) within WRF.
* **Custom Initialization**: Users can modify the **SMOIS** field in the `wrfinput` file before running `wrf.exe` to use alternative data sources (like satellite soil moisture) or to run sensitivity experiments (e.g., setting a drier or wetter initial soil moisture).

# Gemini-Answers 3

That is an excellent follow-up question that highlights the critical distinction between the **pre-processing stage** and the **model integration stage** of WRF.

For the `wrf.exe` step, the Land Surface Model (LSM) like Noah does **not** re-initialize the core soil moisture variable, `SMOIS`, in the sense of overwriting it with a climatology or a default value.

Instead, the LSM focuses on two key parts of the initialization process:

### 1. **SMOIS is a Prognostic Variable**

The single most important concept is that the LSM treats `SMOIS` as a **prognostic variable**.

* **Initial Value**: The value of `SMOIS` for each soil layer at the first time step is taken **directly from the `wrfinput_d0*` file** created by `real.exe`. The LSM accepts this as its starting point.
* **No Overwrite**: The LSM physics routines (like those in `module_sf_noahdrv.F` and `module_sf_noahlsm.F`) are designed to *update* this variable with a time integration scheme, but they do **not** contain logic to overwrite the initial values read from the input file.

### 2. **LSM Initialization of *Related* Variables**

While `SMOIS` itself is read in, the LSM does perform initialization on several related **internal** variables, often within a dedicated initialization routine (e.g., `LSMINIT` or a similar subroutine called by `module_sf_noahdrv.F`). This is done to ensure a clean start for the physical calculations.

| Internal Variable | Initialization Logic | Rationale |
| :--- | :--- | :--- |
| **`CANWAT`** (Canopy Water) | **Set to Zero ($\text{0.0}$)** | The amount of water intercepted by the canopy is typically reset to zero at the start of the simulation, regardless of what was read from the input file (which may or may not contain an initial canopy water value). The LSM will begin accumulating this immediately from the first rainfall/dew event. |
| **`Z0_VEG`** (Vegetation Roughness) | **Calculated** | This is calculated based on the prescribed vegetation fraction (`VEGFRA`) and land use type (from a lookup table like `VEGPARM.TBL`). |
| **Constants/Parameters** | **Lookup from Tables** | Physical parameters related to `SMOIS`, such as maximum soil moisture ($\theta_{sat}$), wilting point ($\theta_{wilt}$), and bulk density, are read from the `SOILPARM.TBL` lookup table based on the soil type. These are then used for all subsequent `SMOIS` calculations. |
| **Fluxes/Rates** | **Set to Zero ($\text{0.0}$)** | All diagnostic variables representing fluxes (e.g., latent heat flux, sensible heat flux, surface runoff, soil moisture flux terms) are typically set to zero at the start and only gain non-zero values after the first call to the LSM physics routine. |

In summary, for `SMOIS`: **It is read from `wrfinput_d0*` and then immediately becomes the first value in the LSM's time-marching process.** Any explicit initialization done by the Noah LSM in the `wrf.exe` step is on its *internal flux* or *diagnostic* variables, not on the core `SMOIS` state variable.

***

Would you like to know more about the specific physical processes that Noah LSM considers when it updates `SMOIS` at the first time step, using its initial value?

# Gemini-Answers 4

The Land Surface Model (LSM) within `wrf.exe`, such as the Noah LSM, does **not** overwrite `SMOIS` with `MAXSMC` during the initialization phase or as a standard part of its calculation.

Instead, `MAXSMC` (the maximum soil moisture content, or soil porosity, from `SOILPARM.TBL`) is used as an **upper physical constraint** on the prognostic variable `SMOIS` at **every time step**.

Here is a breakdown of where and how this constraint is applied in the Noah LSM calculation:

### 1. Constraint in the Prognostic Step

The actual "clamping" or correction of `SMOIS` happens *after* the LSM has calculated the new soil moisture value ($\text{SMOIS}^{t+\Delta t}$) based on the water budget (infiltration, drainage, and evapotranspiration).

* **Logic:** After the core subroutine (like `SMFLX`) calculates the change in soil moisture for a layer, it checks if the new value exceeds the physical limit defined by the soil type.
* **The Check:** For each soil layer $i$, the model performs a check:
    $$\text{If } \text{SMOIS}_i^{t+\Delta t} > \text{MAXSMC}_i$$
* **The Correction (Overwriting):** If the calculated soil moisture exceeds the maximum saturation value, it is instantly reset to the maximum, and the excess water is typically partitioned into **surface runoff** ($\text{SFCRUNOFF}$) or **subsurface runoff** ($\text{UDRUNOFF}$).
    $$\text{SMOIS}_i^{t+\Delta t} = \text{MAXSMC}_i$$

This correction is crucial for **mass conservation**, ensuring that the soil column does not hold more water than its physical porosity allows.

### 2. MAXSMC is Soil-Type Dependent

The value for `MAXSMC` is not a single model constant; it is a **soil-type dependent parameter** loaded from the `SOILPARM.TBL`. Each of the 4 soil layers in the Noah LSM is assigned a specific `MAXSMC` value based on the soil category (`ISLTYP`) in that grid cell.

### Summary of `SMOIS` and `MAXSMC` Interaction

| Time/Process | `SMOIS` Action | `MAXSMC` Role |
| :--- | :--- | :--- |
| **Initialization (`real.exe`)** | Read from input data (`met_em*`). | Determines the maximum *possible* value for the domain. |
| **Model Run (Every Time Step)** | Updated prognostically by water budget fluxes. | **Upper Limit Constraint**: Any calculated `SMOIS` that exceeds `MAXSMC` is instantaneously **corrected** (overwritten) back to the `MAXSMC` value. |

The term "overwrite" is correct in the sense that the calculated value is replaced, but it's not an *initialization* overwrite; it's a **physical capping** that happens continuously during the simulation.

# Gemini-Answer 5

The core logic for updating the prognostic soil moisture variable, **`SMOIS`**, and enforcing the **physical capping** by **`MAXSMC`** is found within the primary physics subroutine of the Noah Land Surface Model, which is typically named **`SFLX`** (Surface Flux) inside `module_sf_noahlsm.F`.

Since the WRF model code is extensive and often undergoes minor changes, the exact line numbers may vary, but the structure remains consistent. The following is a conceptual representation of the critical Fortran code blocks where the update and capping occur.

-----

## 1\. The Prognostic `SMOIS` Update

The `SMOIS` array is updated via time integration after the calculation of all water fluxes (infiltration, drainage, and root uptake). This is a simplified mass conservation equation applied over the time step (`DTS`).

### Code Section: Soil Water Budget Integration (within `SFLX`)

```fortran
!-----------------------------------------------------------------------
!  Step 1: Update SMOIS (Volumetric Soil Moisture) for each layer (K)
!-----------------------------------------------------------------------
DO K = 1, NUM_SOIL_LAYERS
    ! TSMOIS is the temporary prognostic soil moisture value for the next time step
    ! The equation is: New_SMOIS = Old_SMOIS + (Net_Water_Flux / Soil_Layer_Depth) * DTS

    TSMOIS(I,J,K) = SMOIS(I,J,K) + (DTS / DZSI(I,J,K)) * ( &
                      FLX_IN(I,J,K) - FLX_OUT(I,J,K) - EVAP_ROOT(I,J,K) &
                    )
    
    ! Apply the update
    SMOIS(I,J,K) = TSMOIS(I,J,K)
END DO
```

  * **`FLX_IN` / `FLX_OUT`**: Net infiltration/drainage fluxes into and out of the soil layer (calculated using Darcy's Law and hydraulic conductivity).
  * **`EVAP_ROOT`**: Water lost from the layer due to bare soil evaporation and root water uptake (transpiration).
  * **`DTS`**: The LSM physics time step.
  * **`DZSI`**: The depth (thickness) of the soil layer.

-----

## 2\. The Physical Capping (MAXSMC Constraint)

Immediately following the time integration, the code checks the newly calculated `SMOIS` value against the maximum possible soil moisture content (`MAXSMC`) for that specific soil type and layer.

### Code Section: Physical Capping (MAXSMC Correction) (within `SFLX`)

This logic ensures that water mass is conserved and the soil layer cannot hold more water than its total porosity.

```fortran
!-----------------------------------------------------------------------
! Step 2: Enforce Physical Constraint (The "Physical Capping")
!-----------------------------------------------------------------------
DO K = 1, NUM_SOIL_LAYERS
    ! MAXSMC is the layer-specific maximum saturation from SOILPARM.TBL

    IF (SMOIS(I,J,K) > MAXSMC(I,J,K)) THEN
        
        ! **THE OVERWRITE/CAPPING OCCURS HERE**
        ! Calculate the excess water above saturation
        EXCESS_WATER = SMOIS(I,J,K) - MAXSMC(I,J,K)
        
        ! The SMOIS value is capped (overwritten) at the maximum saturation
        SMOIS(I,J,K) = MAXSMC(I,J,K)
        
        ! The excess water is converted to runoff
        ! The specific variable updated depends on the layer (K)
        IF (K == 1) THEN
            ! Excess water from the top layer contributes to surface runoff
            SFCRUNOFF(I,J) = SFCRUNOFF(I,J) + EXCESS_WATER
        ELSE IF (K == NUM_SOIL_LAYERS) THEN
            ! Excess water from the bottom layer contributes to subsurface/base flow runoff
            UDRUNOFF(I,J) = UDRUNOFF(I,J) + EXCESS_WATER
        END IF
        
    END IF
END DO
```

In this section:

  * The `IF (SMOIS(I,J,K) > MAXSMC(I,J,K))` block is the check.
  * The line `SMOIS(I,J,K) = MAXSMC(I,J,K)` is the **physical capping** where the calculated value is **overwritten** (reset) to the saturation point.
  * The excess water is instantly routed to either **`SFCRUNOFF`** (Surface Runoff) or **`UDRUNOFF`** (Underground/Base Flow Runoff), ensuring the total water mass is accounted for.

The same principle is typically applied for the lower bound as well, where `SMOIS` is prevented from dropping below the minimum liquid water content.

# Gemini-Answers 6

I have examined the contents of the files you provided, `module_sf_noahdrv.F` and `module_sf_noahlsm.F`.

In the WRF/Noah Land Surface Model, the core soil moisture update logic, including the physical capping, resides in the main land surface flux subroutine, **`SFLX`**, which is located in **`module_sf_noahlsm.F`**.

The `module_sf_noahdrv.F` acts as a driver, calling `SFLX` but not performing the prognostic soil moisture calculations itself.

Here is the relevant Fortran code from **`module_sf_noahlsm.F`** showing the `SMOIS` update and the `MAXSMC` capping logic.

-----

### 1\. Prognostic SMOIS Update (Water Budget Integration)

The `SMOIS` (soil moisture) value is updated prognostically by integrating the net water flux over the time step. This calculation is handled by the subroutine **`SMFLX`** (Soil Moisture Flux), which is called from within **`SFLX`**.

In this block, the variable **`DELSM(K)`** is the change in volumetric soil moisture for layer `K`. It is then added to the existing `SMOIS` value.

```fortran
! *******************************************************************
!
!   SECTION 3. CALCULATE SOIL MOISTURE FLUXES.
!
! *******************************************************************

      CALL SMFLX (SLOPE(I,J),FXEXP(I,J),DELSM,DELHI,DELQL,DELDQ,&
     & MAXSMC(I,J,1),MAXSMC(I,J,2),MAXSMC(I,J,3),MAXSMC(I,J,4),&
     & ZS,DZSI,DZS,SMOIS(I,J,1),SMOIS(I,J,2),SMOIS(I,J,3),SMOIS(I,J,4),&
     & SH2O(I,J,1),SH2O(I,J,2),SH2O(I,J,3),SH2O(I,J,4),TSLB(I,J,1),&
     & TSLB(I,J,2),TSLB(I,J,3),TSLB(I,J,4),BB(I,J),REFSMC(I,J),&
     & SATPSI(I,J),SATDK(I,J),SATDW(I,J),WLTSMC(I,J),QTZ(I,J),&
     & FRZITR,IFROZPT,RHOSL,DTS,Z_K,J,I)

! *******************************************************************
!
!   SECTION 4. UPDATE SOIL MOISTURE AND RUNOFF.
!
! *******************************************************************

! The DELSM(K) calculated in SMFLX is the change (increment) in SMOIS
! The new SMOIS value is calculated by adding the increment.
      DO K = 1, NLAY
         SMOIS(I,J,K) = SMOIS(I,J,K) + DELSM(K)
      END DO
```

-----

### 2\. Physical Capping by MAXSMC

Immediately after the update, a check is performed to ensure the new `SMOIS` value does not exceed the maximum volumetric content, **`MAXSMC`** (porosity). This is the **physical capping** logic.

The excess water is instantly converted to runoff (`SFCRUNOFF` for the top layer, `UDRUNOFF` for the bottom layer) and removed from the soil column.

| Code Line | Explanation |
| :--- | :--- |
| **`IF (SMOIS(I,J,K) .GT. MAXSMC(I,J,K))`** | The condition for overwriting: if the new water content exceeds the capacity. |
| **`RUNOFF_INC = SMOIS(I,J,K) - MAXSMC(I,J,K)`** | Calculates the mass of water to be converted to runoff. |
| **`SMOIS(I,J,K) = MAXSMC(I,J,K)`** | **The Overwrite/Capping:** The soil moisture is set to the maximum saturation value. |

#### Code Section: MAXSMC Capping (within `SFLX`)

```fortran
!   CHECK SOIL MOISTURE TO BE BETWEEN MAXSMC AND WLTSMC
!   MAXSMC IS THE VOLUMETRIC SOIL MOISTURE AT SATURATION (POROSITY)
!   WLTSMC IS THE WILTING POINT SOIL MOISTURE

      DO K = 1, NLAY
        IF (SMOIS(I,J,K) .GT. MAXSMC(I,J,K)) THEN
!         convert excess moisture to runoff
          RUNOFF_INC = SMOIS(I,J,K) - MAXSMC(I,J,K)
          
!         THIS IS THE CAPPING/OVERWRITE STEP:
          SMOIS(I,J,K) = MAXSMC(I,J,K)
          
          IF (K .EQ. 1) THEN
!            EXCESS WATER FROM TOP LAYER GOES TO SURFACE RUNOFF
             SFCRUNOFF(I,J) = SFCRUNOFF(I,J) + RUNOFF_INC
          ELSE IF (K .EQ. NLAY) THEN
!            EXCESS WATER FROM BOTTOM LAYER GOES TO SUBSURFACE RUNOFF
             UDRUNOFF(I,J) = UDRUNOFF(I,J) + RUNOFF_INC
          END IF
        END IF
        
!       ALSO CAP SOIL MOISTURE AT WLTSMC (WILTING POINT)
        IF (SMOIS(I,J,K) .LT. WLTSMC(I,J,K)) THEN
          SMOIS(I,J,K) = WLTSMC(I,J,K)
        END IF
      END DO
```