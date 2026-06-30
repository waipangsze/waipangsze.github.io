---
layout: post
title: "CFD | Urban CFD | Atmospheric Boundary Layer (ABL) simulation"
categories: [CFD]
tags: [OpenFOAM, Urban, CFD, ABL, HPC]
author: wpsze
date: 2026-06-24 23:06:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/Z2lpCCR.png
banner_img: https://i.imgur.com/Z2lpCCR.png
---

# Wall function

**Wall functions act as a boundary condition on this first cell**. Instead of resolving the complicated flow within the viscous sublayer directly, the software bridges the gap by using semi-empirical formulas to calculate the wall shear stress directly at the first cell center.

When using the standard k-ε turbulence model, you do not manually assign separate wall functions to velocity (u, v, w), pressure (p), or density (ρ). Instead, the wall function acts automatically as a unified boundary condition applied to the Wall zone, which internally modifies how the fluid solver calculates values at the first cell layer.
Here is how each variable is handled at the wall boundary when wall functions are enabled:

## Boundary Condition Setup Checklist

* **Velocity (u, v, w)**: Set the wall boundary condition to **No-Slip**. The solver automatically applies the logarithmic law of the wall to compute the wall shear stress, rather than forcing the velocity to zero throughout the cell.
* **Pressure (p)**: Set to Zero Gradient (or Neumann boundary condition). The pressure at the wall is extrapolated directly from the first cell center. 
* **Density (ρ)**: Set to Zero Gradient for compressible flows. For incompressible flows, density remains constant across the domain. 
* **Turbulent Kinetic Energy (k)**: Set to Zero Gradient or specify a wall function. The solver computes k at the first cell based on local equilibrium (production equals dissipation).  
* **Turbulence Dissipation Rate (ε)**: Automatically computed by the solver at the first cell layer using an algebraic equation ($$\epsilon = \frac{C_{\mu}^{3/4} k^{3/2}}{\kappa y}$$ ), where κ is the von Kármán constant and y is the distance to the cell center. 

### Critical Meshing Requirement

Because you are using the standard k-ε model, it is incapable of resolving the low-Reynolds number flow near the wall. You must ensure your first grid cell center sits inside the log-law region.
Target a y⁺ value between 30 and 300 for your first layer. If your y⁺ drops below 30, the standard wall functions will output highly inaccurate skin friction and heat transfer results.

The velocity values at the exact wall boundary are always zero (0, 0, 0) due to the no-slip condition. However, the velocity values computed at the center of the first layer cell are non-zero, calculated algebraically by the software using the Logarithmic Law of the Wall. 
Instead of tracking absolute values for $u$, $v$, and $w$, the software dynamically rotates the local velocity vector relative to the wall orientation. 

## Local Velocity Components at the First Cell Center

* **Wall-Parallel Velocity** ($U_{\parallel}$): This is the component of velocity moving along the surface of the wall. It is calculated based on your target grid layout ($y^+$) using the law of the wall:
$$u^+ = \frac{U_{\parallel}}{u_{\tau}} = \frac{1}{\kappa} \ln(y^+) + B$$ 
* Where $u_{\tau}$ is the friction velocity, $\kappa \approx 0.41$ is the von Kármán constant, and $B \approx 5.2$.
   * The absolute magnitude of this velocity component depends directly on the core flow speed of your simulation. 
* **Wall-Normal Velocity** ($U_{\perp}$): This is the velocity moving directly into or out of the wall. It is set strictly to 0 m/s because fluid cannot penetrate a solid boundary ($u \cdot n = 0$).

# Friction velocity

**Friction velocity** (usually written as $u_{\tau}$) is a theoretical velocity scale that measures the intensity of shear stress and turbulent friction near a solid wall. It does not represent a physical fluid speed you can measure with a probe, but it dictates how fast momentum changes as you move away from the surface.

## 1. The Core Formula

The exact analytical definition of friction velocity is based on the wall shear stress ($\tau_w$) and the fluid density ($\rho$): 
$$u_{\tau} = \sqrt{\frac{\tau_w}{\rho}}$$ 

* $u_{\tau}$ = Friction velocity ($\text{m/s}$)
* $\tau_w$ = Wall shear stress ($\text{Pa}$ or $\text{N/m}^2$)
* $\rho$ = Fluid density ($\text{kg/m}^3$) 

### wall shear stress

For simple laminar flows, wall shear stress (represented by the symbol $\tau _{w}$ is mathematically defined as: $$\tau_w = \mu \left( \frac{\partial u}{\partial y} \right)_{y=0}$$

- $\tau _{w}$ = Wall shear stress (typically expressed in Pascals or N/m²)
- μ = Dynamic viscosity of the fluid 
- $\frac{\partial u}{\partial y}$ = The velocity gradient, representing how fast the fluid speed (u) increases as you move away from the wall (y)
- $y=0$ = Evaluated exactly at the surface of the wall

## 2. How to Calculate It (Step-by-Step)

In a real engineering or CFD application, you rarely know $\tau_w$ upfront. Instead, you calculate $u_{\tau}$ using a dynamic estimation process based on your flow properties.

## Step 1: Calculate the Skin Friction Coefficient ($C_f$) 

For internal pipe flow or flow over a flat plate, estimate $C_f$ using empirical formulas based on the Reynolds number ($Re$): 

* For flat plates: $C_f = 0.0592 \cdot Re^{-1/5}$
* For smooth pipes: $C_f = 0.079 \cdot Re^{-1/4}$

## Step 2: Compute Wall Shear Stress ($\tau_w$)

Use the skin friction coefficient and your core freestream velocity ($U_{\infty}$) to find the wall stress:
$$\tau_w = \frac{1}{2} C_f \rho U_{\infty}^2$$ 

## Step 3: Solve for Friction Velocity ($u_{\tau}$)

Substitute $\tau_w$ back into the core formula. By combining the two equations, you get a direct shortcut:
$$u_{\tau} = U_{\infty} \sqrt{\frac{C_f}{2}}$$ 

## 3. Example Calculation

Suppose you have air flowing at $U_{\infty} = 20\text{ m/s}$ over a flat plate.

* Density ($\rho$): $1.2\text{ kg/m}^3$
* Calculated $C_f$: $0.003$ (based on the local Reynolds number)

## Step A: Find Wall Shear Stress

$$\tau_w = 0.5 \times 0.003 \times 1.2 \times (20)^2 = 0.72\text{ Pa}$$ 

## Step B: Find Friction Velocity

$$u_{\tau} = \sqrt{\frac{0.72}{1.2}} = \sqrt{0.6} \approx \mathbf{0.775\text{ m/s}}$$ 

## 4. How the CFD Solver Uses It

Inside your $k-\epsilon$ simulation, the solver actually works backward. It knows the velocity at the first cell center ($U_{\parallel}$) and its distance from the wall ($y$).

   1. It assumes the cell is in the log-law region where $y^+ = \frac{u_{\tau} y}{\nu}$ and $\frac{U_{\parallel}}{u_{\tau}} = \frac{1}{\kappa} \ln(y^+) + B$.
   2. It uses an iterative root-finding algorithm to solve for the only unknown variable: $u_{\tau}$.
   3. Once the solver extracts $u_{\tau}$, it multiplies it out to compute the local wall shear stress ($\tau_w = \rho u_{\tau}^2$) and updates the cell's momentum equation.

# Roughness of a surface

**Yes, friction velocity and wall shear stress are directly and heavily influenced by the roughness of a surface.** 
When a surface is rough, it disrupts the fluid flow near the wall, increases turbulence generation, and drastically alters both values.
Here is exactly how roughness impacts these variables and how it changes the mathematical calculations.

## 1. Physical Impact on Friction and Stress

* **Increased Wall Shear Stress** ($\tau_w$): Microscopic peaks and valleys on a rough surface act as tiny physical obstructions. The fluid collides with these bumps, generating form drag (pressure resistance) on a micro-scale. This causes a massive increase in the total force resisting the flow, driving $\tau_w$ upward.
* **Increased Friction Velocity** ($u_{\tau}$): Because friction velocity is mathematically defined by shear stress ($u_{\tau} = \sqrt{\tau_w / \rho}$), any increase in wall shear stress automatically forces the friction velocity to increase.

## 2. How Roughness Modifies the Law of the Wall

**For a smooth wall**, the dimensionless velocity ($u^+$) in the log-law region is calculated as:
$$u^+ = \frac{1}{\kappa} \ln(y^+) + B$$ 
**When a wall is rough**, it physically shifts the velocity profile downward. To account for this, the CFD solver subtracts a roughness shift function ($\Delta B$) from the equation:
$$u^+ = \frac{1}{\kappa} \ln(y^+) + B - \Delta B$$ 
The value of $\Delta B$ depends entirely on your **surface's equivalent sand-grain roughness height** ($k_s$), which is converted into a dimensionless roughness Reynolds number ($k_s^+$):
$$k_s^+ = \frac{u_{\tau} k_s}{\nu}$$ 

## 3. The Three Roughness Regimes

Depending on the size of the roughness elements ($k_s$) relative to the thickness of the viscous sublayer, the flow falls into one of three categories: 

* Hydraulically Smooth ($k_s^+ < 5$): The roughness elements are entirely submerged inside the thin, stagnant viscous sublayer. The fluid flows right over them smoothly. $\Delta B = 0$, meaning roughness has no effect on wall shear stress or friction velocity.
* Transitionally Rough ($5 \le k_s^+ \le 70$): The roughness peaks begin to poke through the viscous sublayer into the turbulent zone. Wall shear stress and friction velocity begin to steadily rise. 
* Fully Rough ($k_s^+ > 70$): The roughness elements completely destroy the viscous sublayer. The drag is now entirely dominated by the physical shape of the rough bumps. In this regime, wall shear stress becomes independent of fluid viscosity ($\nu$) and depends solely on the physical height ($k_s$) of the roughness. 

## 4. How to Set This Up in a $k-\epsilon$ Model

If you are simulating a rough pipe, a cast iron plate, or a corroded surface, you must explicitly pass this information to your CFD software. In your wall boundary condition menu:

   1. Change the wall roughness option from Smooth to Rough.
   2. Input the Roughness Height ($k_s$) in meters or millimeters (this is the average peak-to-valley height of the surface texture).
   3. Input the Roughness Constant ($C_s$) (default is usually 0.5 for uniform sand-grain roughness, but can be raised up to 1.0 if the roughness elements are jagged or non-uniform).

# Any differences of handing wall function on buildings surface and on the ground surface?

**Yes, there are major differences.** When simulating a large urban domain (several kilometers) with explicit 3D building geometries, treating the building walls and the ground exactly the same is one of the most common mistakes in wind engineering. [1, 2] 
In an Atmospheric Boundary Layer (ABL) simulation, the building surfaces and the ground surface play entirely different hydrodynamic roles, requiring a Double Wall Function strategy. [2] 

## 1. Ground Surface: Handled as an "Atmospheric" Wall

The ground represents kilometers of upstream landscape (trees, roads, small structures) that you did not explicitly model in CAD. [3, 4] 

{% note primary %}
**The Goal**: **Maintain a stable, realistic wind velocity gradient (the ABL profile) across the entire domain without letting the wind "decay" or flatten out before it hits the buildings.**
{% endnote %}

* **The Goal**: **Maintain a stable, realistic wind velocity gradient (the ABL profile) across the entire domain without letting the wind "decay" or flatten out before it hits the buildings.** [1, 5] 
* **Roughness Selection**: You must use an implicit aerodynamic roughness length ($z_0$) based on meteorological parameters (e.g., $z_0 = 0.03\text{ m}$ for open grassland, or $z_0 = 0.1\text{ m}$ for parkland). [6] 
* **CFD Input** ($K_s$): Convert your terrain $z_0$ to equivalent sand-grain roughness using the standard formula $K_s \approx 20 \times z_0$. If your terrain is grassland ($z_0=0.03\text{ m}$), set the ground wall to $K_s = 0.6\text{ m}$ (600 mm). [6, 7] 
* **Mesh Requirement**: Because $K_s$ is huge (600 mm), your first cell layer center at the ground must be physically higher than 0.6 meters. [7, 8] 

## 2. Building Surfaces: Handled as "Industrial" Walls

The buildings are explicit 3D solid obstacles interacting directly with the wind. They cause flow separation, vortex shedding, and downwashes. [6, 9] 

* The Goal: Accurately resolve how wind splits around a solid block of concrete or glass.
* Roughness Selection: Treat these as standard, physically smooth materials.
* CFD Input ($K_s$): Set the building walls to $K_s = 0\text{ m}$ (Smooth) or a negligible material roughness like $K_s = 0.001\text{ m}$ (1 mm) for rough concrete.
* Mesh Requirement: To catch the tight wind vortices on the edges of roofs and building corners, the mesh needs to be highly refined. The first cell layer on the building walls will be very thin, typically a few centimeters wide. [6] 

## Summary Comparison Table

| Feature [2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12] | Ground Surface | Building Surfaces |
|---|---|---|
| Physical Context | Broad open terrain (landscape scale) | Localized engineering structures |
| Wall Function Mode | Rough (Atmospheric) | Smooth or minimally rough (Standard) |
| Typical Target $K_s$ | $0.5\text{ m}$ to $2.0\text{ m}$ (600 to 2000 mm) | $0.0\text{ m}$ to $0.002\text{ m}$ (0 to 2 mm) |
| First Layer Cell Height | Coarse ($> K_s$, usually 1 to 2 meters high) | Very fine (a few centimeters thin) |
| Flow Phenomenon | Friction drag driving the ABL velocity profile | Flow separation, stagnation, and wakes |

## Software Implementation Tip

In your software (such as ANSYS Fluent or OpenFOAM), do not lump all surfaces under a single "wall" boundary type. Create two distinct boundary zones: [2, 13] 

   1. Name one Ground: Select a rough wall function and assign the high macro-roughness ($K_s$).
   2. Name the other Buildings: Select a standard wall function and leave it smooth or at default factory values. [2, 6, 7] 


[1] [https://research.tue.nl](https://research.tue.nl/en/publications/cfd-simulation-of-the-atmospheric-boundary-layer-wall-function-pr/)
[2] [https://www.researchgate.net](https://www.researchgate.net/publication/233823549_CFD_simulation_of_wind-induced_pressure_coefficients_on_buildings_with_and_without_balconies_Validation_and_sensitivity_analysis)
[3] [https://www.urbanphysics.net](https://www.urbanphysics.net/2007_AE_CFD_ABL_simulation_Preprint.pdf)
[4] [https://www.urbanphysics.net](https://www.urbanphysics.net/2007_AE_CFD_ABL_simulation_Preprint.pdf)
[5] [https://www.researchgate.net](https://www.researchgate.net/publication/228680223_On_the_use_of_the_k-_model_in_commercial_CFD_software_to_model_the_neutral_atmospheric_boundary_layer)
[6] [https://docs.nablaflow.io](https://docs.nablaflow.io/archiwind/guide/methodology/inlet-boundaries-ref-height/)
[7] [https://www.reddit.com](https://www.reddit.com/r/CFD/comments/1nie7km/abl_ansys_problem/)
[8] [https://www.cfd-online.com](https://www.cfd-online.com/Forums/openfoam-solving/117137-yplus-values-atmospheric-boundary-layer-over-complex-terrain-rough-surface.html)
[9] [https://www.sciencedirect.com](https://www.sciencedirect.com/science/article/pii/S0360132316300415)
[10] [https://help.sim-flow.com](https://help.sim-flow.com/boundary-conditions/atmospheric-wall-function)
[11] [https://www.sciencedirect.com](https://www.sciencedirect.com/science/article/abs/pii/S135223100600834X)
[12] [https://besjournals.onlinelibrary.wiley.com](https://besjournals.onlinelibrary.wiley.com/doi/10.1111/2041-210X.70146)
[13] [https://repository.tudelft.nl](https://repository.tudelft.nl/file/File_d69526a2-1b41-4163-b18d-398250ec72b5)

# The Goal: Maintain a stable, realistic wind velocity gradient (the ABL profile) across the entire domain without letting the wind "decay" or flatten out before it hits the buildings.

- The reason we need this goal—technically termed Horizontal Homogeneity—is that if the wind profile decays as it travels from your inlet boundary to your buildings, the flow that actually hits your buildings will not be the wind you specified at the boundary. [1] 
- Because your domain spans several kilometers, the wind has a long distance to travel before it reaches the structures. If the inlet profile is mathematically incompatible with the ground’s wall functions, the wind velocity and turbulence energy will rapidly "flatten out," dissipate, or deform. [1, 2, 3, 4] 
- Failing to maintain a stable profile compromises the engineering validity of your results for four critical reasons:

## 1. Severe Underestimation of Wind Loads on Tall Buildings [5] 

The physical Atmospheric Boundary Layer (ABL) means wind speed is low near the ground but increases exponentially or logarithmically with height. [6] 

* If the profile decays: The high-velocity wind at the top of your domain will bleed momentum downward or flatten out.
* The Consequence: By the time the wind hits a tall skyscraper, the actual velocity striking the upper floors will be significantly lower than real-world conditions. Your calculation of drag forces, base moments, and structural wind loads will be unsafely low, risking structural under-design. [1, 6, 7, 8] 

## 2. Flawed Pedestrian Wind Comfort (PWC) Assessments

Urban planners use CFD to ensure that downwashes (wind hitting a skyscraper facade and shooting straight down to the street) do not create dangerous gale-force zones for pedestrians. [9, 10, 11] 

* If the profile decays: The structural turbulence kinetic energy ($k$) typically drops off drastically over long distances if the ground roughness doesn't actively sustain it. [2, 12] 
* The Consequence: The wind reaching the ground level around your buildings will artificially lack energy. Your simulation will falsely report that a street corner is calm and safe, when in reality, the real-world wind would create highly uncomfortable or hazardous wind tunnels. [13] 

## 3. Inaccurate HVAC Exhaust and Pollutant Dispersion [14] 

Simulations often check if generator exhaust or cooling tower plumes will pull back into building fresh air intakes. [1, 9] 

* If the profile decays: Dispersion depends entirely on the wind's vertical mixing capability, which is driven by the turbulence profile ($\epsilon$ and $k$). [15] 
* The Consequence: If turbulence decays, the exhaust plumes will not mix or dilute properly in the air. They will either hover like a dense blanket or drift inaccurately, giving completely false readings of gas and heat concentrations around the building envelopes. [7] 

## 4. Flawed Validation and "Virtual Wind Tunnel" Ethics

In computational wind engineering (CWE), your simulation must mimic a real wind tunnel or real-world meteorological data. [12, 16, 17] 

* **If you tell a client or regulatory board, "I tested this building at 30 m/s wind speeds," but the profile decayed to 22 m/s by the time it reached the building site**, `your test is invalid`. You are essentially testing the building under the wrong weather conditions.

## 🛠️ The Solution: How to Stop the Decay

To prevent this decay over a multi-kilometer domain, you cannot just input a standard velocity profile. You must use mathematically matched equations at the inlet that act as a perfectly balanced equilibrium solution for your turbulence solver. [1, 18] 
**The industry standard approach is the Richards and Hoxey (1993) boundary conditions**. They derived a linked set of equations where the Inlet Velocity, Turbulent Kinetic Energy ($k$), and Dissipation Rate ($\epsilon$) perfectly balance out the friction generated by your rough ground wall function: [1, 18, 19] 

* Velocity Profile: $u(y) = \frac{u_{\tau}}{\kappa} \ln\left(\frac{y + z_0}{z_0}\right)$
* Turbulent Kinetic Energy: $k = \frac{u_{\tau}^2}{\sqrt{C_{\mu}}}$
* Dissipation Rate: $\epsilon(y) = \frac{u_{\tau}^3}{\kappa (y + z_0)}$ [7, 15, 20] 

When these three inlet profiles are used alongside a ground wall function set to $K_s \approx 20 \times z_0$, the forces perfectly balance. The wind will travel across 5 kilometers of empty terrain and arrive at your building group perfectly intact—exactly as you designed it. [1, 3, 18] 

[1] [https://www.linkedin.com](https://www.linkedin.com/posts/ryansayko_cfd-datacenters-windengineering-activity-7468658829501734912-sl6B)
[2] [https://www.researchgate.net](https://www.researchgate.net/publication/264177865_Numerical_calculation_of_the_wind_action_on_buildings_using_Eurocode_1_atmospheric_boundary_layer_velocity_profiles)
[3] [https://iawe.org](https://iawe.org/wp-content/uploads/2025/07/WA2-04.pdf)
[4] [https://pmc.ncbi.nlm.nih.gov](https://pmc.ncbi.nlm.nih.gov/articles/PMC10990122/)
[5] [https://www.sciencedirect.com](https://www.sciencedirect.com/science/article/abs/pii/S0167610521000441)
[6] [https://cadeengineering.com](https://cadeengineering.com/atmospheric-boundary-layer-assessing-wind-loads-on-urban-structures/)
[7] [https://www.jafmonline.net](https://www.jafmonline.net/article_2240_64ed19038730cc7a1c45cd60782bea88.pdf)
[8] [https://cadeengineering.com](https://cadeengineering.com/atmospheric-boundary-layer-assessing-wind-loads-on-urban-structures/)
[9] [https://www.simscale.com](https://www.simscale.com/docs/analysis-types/pedestrian-wind-comfort-analysis/wind-conditions/atmospheric-boundary-layer/)
[10] [https://www.researchgate.net](https://www.researchgate.net/publication/330535567_Review_for_practical_application_of_CFD_for_the_determination_of_wind_load_on_high-rise_buildings)
[11] [https://www.simscale.com](https://www.simscale.com/blog/building-downwash-mitigation-strategies/)
[12] [https://wes.copernicus.org](https://wes.copernicus.org/preprints/wes-2025-2/wes-2025-2.pdf)
[13] [https://www.easternengineeringgroup.com](https://www.easternengineeringgroup.com/aerodynamic-design-in-skyscrapers-balancing-wind-comfort-and-structural-performance/)
[14] [https://www.urbanphysics.net](https://www.urbanphysics.net/2007_AE_CFD_ABL_simulation_Preprint.pdf)
[15] [https://web.aimgroupinternational.com](https://web.aimgroupinternational.com/2023/icwe/papers/ICWE2023_AbstractSubmission-677_2023-01-31%2009_59_50.pdf)
[16] [https://scholars.cityu.edu.hk](https://scholars.cityu.edu.hk/en/studentTheses/cfd-modelling-of-atmospheric-boundary-layer-flow-and-wind-effects/)
[17] [https://airshaper.com](https://airshaper.com/blog/wind-engineering)
[18] [https://www.sciencedirect.com](https://www.sciencedirect.com/science/article/pii/S016761051930131X)
[19] [https://www.sciencedirect.com](https://www.sciencedirect.com/science/article/pii/S1875963723000150)
[20] [https://www.sciencedirect.com](https://www.sciencedirect.com/science/article/pii/S2950601824000058)
