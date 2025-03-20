---
layout: post
title: Precipitation minus Evaporation (P-E)
categories: [Meteorology]
tags: [NWP, MPAS, WRF, Precipitation, Evaporation, Hydrology]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-03-20 10:40:00
index_img: https://i.imgur.com/yprVCEl.png
banner_img: https://i.imgur.com/yprVCEl.png
---

# Precipitation minus Evaporation (P-E)

To calculate **Precipitation minus Evaporation (P-E)** in numerical weather simulations using mixing ratio, the process involves analyzing atmospheric moisture fluxes and thermodynamic relationships. 

**地表可用水量 (降水減蒸發，P-E)** 是反映氣候乾濕程度的關鍵指標，對區域水資源、農業生產和生態系統穩定性有重要影響。熱力學理論指出，隨著全球變暖，大氣中水汽含量上升，水汽輸送增強，這會促使乾燥的地區/季節變得更乾，而濕的地區/季節變得更濕。地表可用水量季節變率增強將使得水資源在年內分配更不均衡，導致濕季更容易出現洪澇災害，而乾季更容易發生乾旱事件，給區域水資源管理和調度帶來嚴峻挑戰和壓力

- **Definition**: P-E is the difference between the amount of precipitation (P) and evaporation (E) over a given area and time period. It represents the net supply of freshwater available for runoff, infiltration, and other hydrological processes.
- **Role in Hydrology**: P-E is crucial for understanding water availability, as it influences surface water and groundwater recharge. A positive P-E indicates a surplus of water, potentially leading to increased runoff and flooding, while a negative P-E suggests a deficit, often associated with drought conditions.

To calculate P-E, you need data on both precipitation and evaporation. Here's a general approach:

1. **Precipitation Data**: Obtain precipitation data from sources like rain gauges, satellite imagery, or reanalysis datasets (e.g., ERA5).

2. **Evaporation Data**: Evaporation can be estimated using models that incorporate factors like temperature, humidity, wind speed, and solar radiation. Common methods include the Penman-Monteith equation or using reanalysis datasets.

3. **Compute P-E**: Subtract the evaporation from the precipitation over the desired time period (e.g., monthly or annually).
   $$
   P - E = \text{Precipitation} - \text{Evaporation}
   $$

## Another Calculation Methods

### 1. **Moisture Budget Equation**

The fundamental relationship in atmospheric water balance is:
$$
P - E = -\left(\frac{\partial q}{\partial t} + \nabla \cdot (\mathbf{v}q)\right)
$$

Where:

- $q$ = mixing ratio (kg water vapor/kg dry air)
- $\mathbf{v}$ = horizontal wind vector
- $\frac{\partial q}{\partial t}$ = local moisture tendency
- $\nabla \cdot (\mathbf{v}q)$ = horizontal moisture advection

This residual method calculates **P-E** as the **negative sum** of:

- Local moisture changes over time
- Net horizontal moisture flux divergence

![Trenberth, Kevin E., and Christian J. Guillemot. "**Evaluation of the global atmospheric moisture budget as** seen from analyses." Journal of Climate 8.9 (1995): 2255-2272.](https://i.imgur.com/6YfNHLN.png){width=500}
![Seager, Richard, and Naomi Henderson. "Diagnostic computation of moisture budgets in the ERA-Interim reanalysis with reference to analysis of CMIP-archived atmospheric model data." Journal of Climate 26.20 (2013): 7876-7901.](https://i.imgur.com/zlMe2hX.png){width=500}

### 2. **Direct Surface Flux Approach**
For evaporation calculation using mixing ratio:
$$
E = \rho_{air} \cdot C_E \cdot |\mathbf{v}| \cdot (q_{sat} - q_{air})
$$
Where:

- $\rho_{air}$ = air density
- $C_E$ = bulk transfer coefficient (~1.5×10⁻³ over water)
- $q_{sat}$ = saturation mixing ratio at surface temperature
- $q_{air}$ = actual near-surface mixing ratio

**Precipitation (P)** is typically output from **microphysics parameterizations** in the model.

## Key Implementation Steps

**Step 1: Compute Saturation Mixing Ratio**  
Use Tetens formula for saturation vapor pressure:
$$
e_s = 6.112 \exp\left(\frac{17.67T}{T + 243.5}\right) \quad [\text{hPa}]
$$

Then calculate:

$$
q_{sat} = \frac{\varepsilon e_s}{P - e_s} \quad (\varepsilon = 0.622)
$$

**Step 2: Vertical Integration**  
For column-integrated P-E:
$$
P - E = -\frac{1}{g} \int_{sfc}^{top} \left(\frac{\partial q}{\partial t} + \nabla \cdot (\mathbf{v} q)\right) dp
$$

**Step 3: Numerical Implementation**  
In finite difference models:

- Calculate moisture tendency between time steps
- Use centered differencing for advection terms
- Apply mass-corrected transport schemes to prevent numerical dispersion

## Practical Considerations

**Data Requirements**  

- 3D fields of mixing ratio and winds at model levels
- Surface pressure and temperature
- Precipitation outputs from convective/resolved schemes

**Validation**  
Compare results against:

1. Direct surface flux measurements
2. Reanalysis-derived P-E products
3. River basin water balance estimates

The moisture budget method generally shows <10% error for monthly means in midlatitudes but requires careful handling of tropical convection systems[^3][^5]. Recent NWP systems like ECMWF's IFS and NASA's GEOS use hybrid approaches combining both methods[^1][^7].

# MPAS: Registry.xml

About mixing ratio,

```xml
<var_array name="scalars" type="real" dimensions="nVertLevels nCells Time" packages="met_stage_out">
        <var name="qv" array_group="moist" units="kg kg^{-1}"
                description="Water vapor mixing ratio"/>

        <var name="qc" array_group="moist" units="kg kg^{-1}"
                description="Cloud water mixing ratio"/>

        <var name="qr" array_group="moist" units="kg kg^{-1}"
                description="Rain water mixing ratio"/>
</var_array>
```

# NWP-AI

## NeuralGCM

- [**NWP | AI | NeuralGCM**](https://waipangsze.github.io/2025/03/18/NWP-AI-NeuralGCM/)

---

- **To diagnose precipitation minus evaporation rate (P − E)**
- In this work, **we only diagnosed precipitation minus evaporation from NeuralGCM**. However, in future work, **we plan to develop a scheme to reformulate NeuralGCM to predict precipitation and evaporation separately**. This could be achieved, for example, by using conventional parameterization to estimate evaporation (and then calculating precipitation by adding it to P-E). Another approach could involve training a neural network (NN) specifically to predict evaporation, potentially optimized to predict the same fluxes as those in ERA5 data.

# References

1. [GLOBAL CHANGES IN **PRECIPITATION MINUS EVAPORATION** | 2023](https://www.met.reading.ac.uk/~sgs02rpa/TALKS/Allan_Seminar23.pdf)
2. [Scott, Robert W., and James R. Scoggins. The **moisture budget** in relation to convection. No. M-216. 1977.](https://ntrs.nasa.gov/api/citations/19770012769/downloads/19770012769.pdf)
3. [Trenberth, Kevin E., and Christian J. Guillemot. "**Evaluation of the global atmospheric moisture budget as** seen from analyses." Journal of Climate 8.9 (1995): 2255-2272. (**Recommand**)](https://journals.ametsoc.org/view/journals/clim/8/9/1520-0442_1995_008_2255_eotgam_2_0_co_2.pdf)
4. [Seager, Richard, and Naomi Henderson. "**Diagnostic computation of moisture budget**s in the ERA-Interim reanalysis with reference to analysis of CMIP-archived atmospheric model data." Journal of Climate 26.20 (2013): 7876-7901.](https://ocp.ldeo.columbia.edu/res/div/ocp/pub/seager/Seager_Henderson_mb.pdf)
5. [**Moisture budget equation** and application to Sahel Drought](https://www.ictp.it/sites/default/files/%5Bteaching-materials%5D/lecture_notes_AD_section12.pdf)


[^1]: <https://www.osti.gov/servlets/purl/6807588>
[^2]: <https://www.eoas.ubc.ca/books/Practical_Meteorology/prmet/Ch04-Moist.pdf>
[^3]: <https://journals.ametsoc.org/downloadpdf/journals/clim/28/20/jcli-d-15-0369.1.pdf>
[^4]: <https://weather.cod.edu/notes/materials/1110/Unit2_1110.pdf>
[^5]: <https://agupubs.onlinelibrary.wiley.com/doi/full/10.1029/2022GL097725>
[^6]: <https://www.weather.gov/epz/wxcalc_mixingratio>
[^7]: <https://atmos.uw.edu/~dennis/321/321_Lecture_15.pdf>
[^8]: <https://www.inscc.utah.edu/~krueger/5130/precip_rate.pdf>
