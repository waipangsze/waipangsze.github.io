---
layout: post
title: NWP | MPAS | WRF| Land Use/Land Cover and Soil Type
categories: [NWP]
tags: [NWP, MPAS, WRF, Meteorology, LANDUSE, SOILPARM]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-05-28 10:51:00
index_img: https://i.imgur.com/0btS3Co.png
banner_img: https://i.imgur.com/0btS3Co.png
---

# The Interplay Between Land Use/Land Cover and Soil Type in the WRF Model: Implications for Surface Variable Prognostication  

The Weather Research and Forecasting (WRF) model is a widely used numerical weather prediction system that integrates atmospheric dynamics with land-surface interactions. A critical aspect of its performance hinges on accurately representing land-surface processes, which depend on two primary input datasets: **land use/land cover (LULC)** and **soil type**. This report examines the necessity of synchronizing LULC and soil type modifications in WRF, elucidates their interdependencies, and delineates their combined role in calculating prognostic variables such as 2-meter temperature (t2m).  

---

## Land Use/Land Cover and Soil Type: Distinct but Interconnected Inputs  

### Role of LULC in Surface Parameterization  
LULC data define the physical and biological characteristics of the land surface, including vegetation type, albedo, roughness length, and emissivity. These properties directly influence energy partitioning between **sensible heat, latent heat, and ground heat fluxes**[^6][^7]. For example, forests exhibit higher **roughness lengths and evapotranspiration rates** compared to urban areas, altering boundary layer dynamics[^7]. The **Noah Land Surface Model (LSM)**, a common component of WRF, uses LULC data to parameterize vegetation-dependent processes such as **stomatal resistance and leaf area index (LAI)**[^6].  

![CERES land classification from IGBPa_1198.map.nc. Surface types 1 - 17 correspond to those defined by IGBP (International Geosphere Biosphere Programme). The last 3 surface types were defined for CERES. Surface type 18, Tundra; Fresh snow, number 19, and sea ice, number 20 are not permanent surface types. They are obtained daily from the National Snow and Ice Data Center. The IGBP surface type for snow and ice number 15, is for permanent snow and ice. (Climate Data Guide; D. Shea)](https://i.imgur.com/vRt3sIP.png)

- [MCD12Q1.061 MODIS Land Cover Type Yearly Global 500m](https://developers.google.com/earth-engine/datasets/catalog/MODIS_061_MCD12Q1?hl=zh-tw)
  - Terra 和 Aqua 結合的 Moderate Resolution Imaging Spectroradiometer (MODIS) 土地覆蓋類型 (MCD12Q1) 版本 6.1 資料產品，提供全球土地覆蓋類型，間隔為一年。MCD12Q1 6.1 版資料產品是使用 MODIS Terra 和 Aqua 反射率資料的監督分類產生。土地覆蓋類型是根據國際地球-生物圈計畫 (IGBP)、馬里蘭大學 (UMD)、葉面積指數 (LAI)、生物群系-生物地球化學循環 (BGC) 和植物功能類型 (PFT) 分類方案而來。之後，受控分類作業會進行額外的後續處理，整合先前知識和輔助資訊，進一步精進特定類別。糧農組織 (FAO) 土地覆蓋類別分類系統 (LCCS) 提供其他土地覆蓋物屬性評估圖層，用於評估土地覆蓋物、土地使用情形和地表水文。也提供地貌類型 1-5、地貌屬性 1-3、地貌屬性評估 1-3、地貌品質管控 (QC) 和陸地水域遮罩的圖層。

### Role of Soil Type in Hydrological and Thermal Processes
**Soil type determines hydraulic and thermal properties, including saturation moisture content, hydraulic conductivity, and heat capacity**. The WRF model categorizes soils into types such as sand, loam, and clay, each associated with specific parameters in the `SOILPARM.TBL` file[^3]. For instance, sandy soils have higher hydraulic conductivity but lower water-holding capacity compared to clayey soils, affecting soil moisture evolution and root-zone water availability[^3][^6]. These properties are critical for modeling infiltration, runoff, and soil temperature profiles.  

![USDA soil texture triangle adopted in the SoilGrids250m data set (Hengl et al., 2017) and the reduced set of hydrologic soil groups used in this study (Sa = sand; Lo = loam; Cl = clay; Si = silt).](https://i.imgur.com/AGCB3H4.png)

### Relationship Between LULC and Soil Type
LULC determines surface characteristics like vegetation and albedo, while soil type affects subsurface properties like water retention. **Both influence the surface energy balance, which governs heat, moisture, and momentum exchange between land and atmosphere, impacting weather predictions**.

The relationship between LULC and soil type in WRF is functional rather than direct, as **both contribute to the surface energy balance but operate through different mechanisms**. 

**LULC** influences surface properties such as:

- Albedo (reflectivity, affecting solar radiation absorption),
- Roughness length (impacting momentum exchange),
- Vegetation fraction and type (affecting evapotranspiration).

**Soil type**, on the other hand, determines subsurface properties such as:

- Soil texture (e.g., sand, clay, loam, influencing water retention),
- Hydraulic conductivity (affecting moisture transfer),
- Thermal conductivity (affecting heat storage and release).

These properties collectively shape the surface energy balance, which includes **sensible heat flux (heat transfer to the air), latent heat flux (evaporation or transpiration), and ground heat flux (heat storage in the soil)**.

### Q1. Necessity of Coherent LULC and Soil Type Assignments?
**While LULC and soil type are independent inputs** (specified via `IVGTYP` and `ISLTYP` variables, respectively), their coherence is physiographically essential. For example, converting a grassland (LULC class) to an urban area should ideally be accompanied by a soil type change from natural loam to impervious surfaces. Discrepancies between LULC and soil type can lead to unphysical land-atmosphere interactions, such as excessive evaporation from paved surfaces misclassified as permeable soils[^5]. A case study highlighted in WRF support forums demonstrated that inconsistent `ISLTYP` (soil type) and `XLAND` (land/water mask) values caused numerical instabilities, including division-by-zero errors in soil thermal calculations[^5].  

### Q2. Do You Need to Change Soil Type When Changing LULC?
**It seems likely that you do not need to change the soil type when altering LULC in WRF, as they are treated as separate inputs**. For example, changing a forest to an urban area might not require a soil type update, but for realistic simulations, significant LULC changes (like deforestation) could imply soil property changes. This depends on the context, and the evidence leans toward flexibility in model setup. 

### Q3. Do you need to change soil type if you change LULC?

- **Not necessarily**, but it's often realistic to consider. **LULC and soil type are distinct inputs in WRF**. You can change one without changing the other. However, in reality, there's often a correlation. For example, certain vegetation types (LULC) are more likely to be found with specific soil types due to ecological factors.
- **Changing LULC doesn't automatically require changing soil type in WRF from a technical standpoint, as they are separate inputs**. However, for realistic simulations, it's often good practice to ensure consistency or consider how a LULC change might realistically be associated with soil characteristics in the region you're modeling.


### Q4. Do Changes in LULC Require Updates to Soil Type?
Research suggests that changing LULC in WRF does not inherently require changing the soil type, **as they are treated as independent inputs during model initialization**. For instance, the geogrid program in WRF interpolates LULC categories (e.g., from **USGS or MODIS datasets**) and soil categories (e.g., from **soiltype_top_30s**) separately to the model grid. This separation allows users to update LULC data, such as using high-resolution datasets without necessarily updating soil type data from sources.

- [GEOGRID.TBL.ARW](https://github.com/wrf-model/WPS/blob/master/geogrid/GEOGRID.TBL.ARW)

{% fold info @GEOGRID.TBL.ARW %}
```text
===============================
name=LANDUSEF
        priority=1
        dest_type=categorical
        z_dim_name=land_cat
        dominant = LU_INDEX
        landmask_water =    nlcd2006_9s:17         # Calculate a landmask from this field
        landmask_water =   nlcd2006_30s:17         # Calculate a landmask from this field
        landmask_water =    nlcd2011_9s:17         # Calculate a landmask from this field
        landmask_water =       nlcd2006:17         # Calculate a landmask from this field
        landmask_water =       ssib_10m:16         # Calculate a landmask from this field
        landmask_water =        ssib_5m:16         # Calculate a landmask from this field
        landmask_water =      modis_15s:17         # Calculate a landmask from this field
        landmask_water = modis_15s_lake:17,21      # Calculate a landmask from this field
        landmask_water =      modis_30s:17         # Calculate a landmask from this field
        landmask_water = modis_30s_lake:17,21      # Calculate a landmask from this field
        landmask_water =       usgs_30s:16         # Calculate a landmask from this field
        landmask_water =     usgs_lakes:16,28      # Calculate a landmask from this field
        landmask_water =    modis_lakes:17,21      # Calculate a landmask from this field
        landmask_water =     modis_2010:17         # Calculate a landmask from this field
        landmask_water = modis_2010_lake:17,21     # Calculate a landmask from this field
        landmask_water =     modis_2050:17         # Calculate a landmask from this field
        landmask_water = modis_2050_lake:17,21     # Calculate a landmask from this field
        landmask_water =        usgs_2m:16         # Calculate a landmask from this field
        landmask_water =        usgs_5m:16         # Calculate a landmask from this field
        landmask_water =       usgs_10m:16         # Calculate a landmask from this field
        landmask_water =         lowres:17,21      # Calculate a landmask from this field
        landmask_water =        default:17,21      # Calculate a landmask from this field
        interp_option =    nlcd2006_9s:average_gcell(0.0)
        interp_option =   nlcd2006_30s:average_gcell(0.0)
        interp_option =    nlcd2011_9s:average_gcell(0.0)  
        interp_option =       nlcd2006:nearest_neighbor
        interp_option =       ssib_10m:four_pt
        interp_option =        ssib_5m:four_pt
        interp_option =      modis_15s:nearest_neighbor
        interp_option = modis_15s_lake:nearest_neighbor
        interp_option =      modis_30s:nearest_neighbor
        interp_option = modis_30s_lake:nearest_neighbor
        interp_option =       usgs_30s:nearest_neighbor
        interp_option =     usgs_lakes:nearest_neighbor
        interp_option =    modis_lakes:nearest_neighbor
        interp_option =     modis_2010:nearest_neighbor
        interp_option = modis_2010_lake:nearest_neighbor
        interp_option =     modis_2050:nearest_neighbor
        interp_option = modis_2050_lake:nearest_neighbor
        interp_option =        usgs_2m:four_pt
        interp_option =        usgs_5m:four_pt
        interp_option =       usgs_10m:four_pt
        interp_option =         lowres:average_gcell(4.0)+four_pt
        interp_option =        default:nearest_neighbor
        rel_path =    nlcd2006_9s:nlcd2006_ll_9s/
        rel_path =   nlcd2006_30s:nlcd2006_ll_30s/
        rel_path =    nlcd2011_9s:nlcd2011_ll_9s/
        rel_path =       nlcd2006:nlcd2006_ll_30s/
        rel_path =       ssib_10m:ssib_landuse_10m/
        rel_path =        ssib_5m:ssib_landuse_5m/
        rel_path =      modis_15s:modis_landuse_20class_15s/
        rel_path = modis_15s_lake:modis_landuse_20class_15s_with_lakes/
        rel_path =      modis_30s:modis_landuse_20class_30s/
        rel_path = modis_30s_lake:modis_landuse_20class_30s_with_lakes/
        rel_path =       usgs_30s:landuse_30s/
        rel_path =     usgs_lakes:landuse_30s_with_lakes/
        rel_path =    modis_lakes:modis_landuse_21class_30s/
        rel_path =     modis_2010:slucm/modis_landuse_20class_15s_2010s/
        rel_path = modis_2010_lake:slucm/modis_landuse_20class_15s_2010s_with_lakes/
        rel_path =     modis_2050:slucm/modis_landuse_20class_15s_2050s/
        rel_path = modis_2050_lake:slucm/modis_landuse_20class_15s_2050s_with_lakes/
        rel_path =        usgs_2m:landuse_2m/
        rel_path =        usgs_5m:landuse_5m/
        rel_path =       usgs_10m:landuse_10m/
        rel_path =         lowres:modis_landuse_20class_5m_with_lakes/
        rel_path =        default:modis_landuse_20class_30s_with_lakes/
===============================
name=SOILTEMP
        priority=1
        dest_type=continuous
        interp_option= lowres:sixteen_pt+four_pt+wt_average_4pt+wt_average_16pt+search
        interp_option=default:sixteen_pt+four_pt+average_4pt+average_16pt+search
        masked=water
        fill_missing=0.
        rel_path= lowres:soiltemp_1deg/
        rel_path=default:soiltemp_1deg/
===============================
name=SOILCTOP
        priority=1
        dest_type=categorical
        z_dim_name=soil_cat
        dominant=SCT_DOM
        interp_option = bnu_soil_30s:nearest_neighbor
        interp_option =     30s:nearest_neighbor
        interp_option =      2m:four_pt
        interp_option =      5m:four_pt
        interp_option =     10m:four_pt
        interp_option =  lowres:four_pt
        interp_option = default:nearest_neighbor
        rel_path= bnu_soil_30s:bnu_soiltype_top/
        rel_path=     30s:soiltype_top_30s/
        rel_path=      2m:soiltype_top_2m/
        rel_path=      5m:soiltype_top_5m/
        rel_path=     10m:soiltype_top_10m/
        rel_path=  lowres:soiltype_top_5m/
        rel_path= default:soiltype_top_30s/
===============================
name=SOILCBOT
        priority=1
        dest_type=categorical
        z_dim_name=soil_cat
        dominant=SCB_DOM
        interp_option = bnu_soil_30s:nearest_neighbor
        interp_option =     30s:nearest_neighbor
        interp_option =      2m:four_pt
        interp_option =      5m:four_pt
        interp_option =     10m:four_pt
        interp_option =  lowres:four_pt
        interp_option = default:nearest_neighbor
        rel_path= bnu_soil_30s:bnu_soiltype_bot/
        rel_path=     30s:soiltype_bot_30s/
        rel_path=      2m:soiltype_bot_2m/
        rel_path=      5m:soiltype_bot_5m/
        rel_path=     10m:soiltype_bot_10m/
        rel_path=  lowres:soiltype_bot_5m/
        rel_path= default:soiltype_bot_30s/
===============================
```
{% endfold %}

---

## Mechanistic Linkages Between Surface Properties and Prognostic Variables  

### How WRF Calculates t2m Based on LULC and Soil Type
The 2-meter temperature (t2m) is a diagnostic variable in WRF, **computed based on the surface layer module rather than directly simulated**. **WRF calculates t2m using the surface layer module, adjusting the lowest model level temperature to 2 meters using similarity theory**. LULC and soil type affect this by influencing surface properties (e.g., roughness, albedo) and subsurface properties (e.g., heat storage), respectively, which shape the surface energy balance and thus t2m.

This adjustment relies on surface fluxes (sensible and latent heat fluxes) and stability conditions, which are determined by the land surface model (LSM). The LSM, such as Noah, Noah-MP, or CLM, integrates LULC and soil type to compute the surface energy balance. Specifically:

- **LULC** affects parameters like albedo, roughness length, and vegetation fraction, influencing how much solar radiation is absorbed and how heat is transferred to the atmosphere.
- **Soil type** affects soil hydraulic and thermal properties, determining how heat and moisture are stored and transferred within the soil, which impacts surface temperature and fluxes.

| **Aspect**                     | **LULC Influence**                                                                 | **Soil Type Influence**                                                                 |
|--------------------------------|-----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| **Surface Properties**         | Determines albedo, roughness length, vegetation fraction, affecting energy absorption and transfer. | Affects hydraulic (water retention) and thermal (heat conductivity) properties of soil.   |
| **Energy Balance Components**  | Impacts sensible and latent heat fluxes through vegetation and surface cover.      | Influences ground heat flux and moisture availability for evaporation.                  |
| **Impact on t2m**              | Indirectly affects t2m via surface temperature and heat fluxes, influenced by albedo and roughness. | Indirectly affects t2m via soil heat storage and moisture, impacting surface temperature.|
| **Typical Data Sources**       | USGS, MODIS, MapBiomas (e.g., 30m resolution).  | STATSGO, FAO-UNESCO, SoilGrids (e.g., global coverage, high resolution).                |

### Surface Energy Balance and t2m Computation  
The 2-meter temperature (t2m) is derived from the surface energy balance, which integrates **net radiation**, **sensible heat flux** ($H$), **latent heat flux** ($LE$), and **ground heat flux** ($G$):

$$
R_{\text{net}} = H + LE + G = (1-\alpha)S_{\downarrow} + L_{\downarrow} - L_{\uparrow} - G
$$  
where $R_{\text{net}}$ is net radiation[^7], $S_{\downarrow}$ and $L_{\downarrow}$ are incoming shortwave and longwave radiation, and $\alpha$ is albedo.

1. **LULC Influence**:  
   - Vegetation type affects albedo (e.g., forests: ~0.15; deserts: ~0.35), altering $R_{\text{net}}$[^6].  
   - Roughness length modulates turbulent fluxes ($H$ and $LE$) via **Monin-Obukhov similarity theory**[^7].  
   - **Urban areas exhibit higher heat storage**, elevating $H$ and t2m[^6].  

2. **Soil Type Influence**:  
   - **Soil moisture** availability governs $LE$ through **evaporation**. Clay soils retain moisture longer, sustaining $LE$ during dry periods[^3][^8].  
   - Thermal conductivity impacts $G$; sandy soils transfer heat more efficiently than organic-rich soils[^3].  

### Joint Impact on Boundary Layer Dynamics  
**Misalignment between LULC and soil type can skew surface flux partitioning**. For instance, assigning a forest LULC with sandy soil (low water retention) may underestimate latent heat flux, leading to overestimated t2m. Conversely, urban LULC with clay soil (high water retention) could artificially enhance $LE$, cooling t2m unrealistically[^5][^8]. The Noah-LSM resolves these interactions by coupling vegetation parameters (e.g., LAI) with soil hydraulic properties to compute root-zone moisture and transpiration[^6].  

**Mismatched landcover-soil pairs disrupt these relationships**. For example, cropland (IVGTYP=2) paired with sandy soil (ISLTYP=1) may underestimate irrigation demands due to exaggerated drainage, while urban (IVGTYP=1) with clay (ISLTYP=3) artificially sustains $LE$ through retained moisture. 

---

## Best Practices for WRF Configuration  

1. **Harmonize LULC and Soil Type Classifications**:  
   - Use physiographic datasets (e.g., USGS, MODIS) ensuring LULC-soil consistency.  
   - Validate `IVGTYP` and `ISLTYP` coherence using tools like `ncks` to prevent runtime errors[^5].  

2. **Sensitivity Testing**:  
   - Conduct ensemble runs with perturbed LULC/soil combinations to quantify t2m variability.  
   - Prioritize soil type updates when LULC changes alter surface permeability (e.g., urbanization).  

3. **Leverage High-Resolution Data**:  
   - Incorporate 30m LULC and soil maps to resolve subgrid heterogeneity, critical for mesoscale applications[^8].  

---

## Case Evidence of Update Necessity  

- [in-house 1s 33 cat land use/30 s 19 cat soil type working in WPS 4.2 but not 4.5 or 4.6](https://forum.mmm.ucar.edu/threads/in-house-1s-33-cat-land-use-30-s-19-cat-soil-type-working-in-wps-4-2-but-not-4-5-or-4-6.18246/)
  - A 2024 WRF forum post documented catastrophic errors when using 1-arcsecond landcover data with 30-arcsecond soil types in WPS versions 4.5–4.6. The geogrid module assigned unrealistic soil moisture values (1e20) to water grids due to landmask inconsistencies between high-resolution landcover and legacy soil datasets. Resolution mismatches induced numerical instabilities in the `process_tile_module.F` code, necessitating harmonized 1-arcsecond soil data to maintain XLAND (land/water mask) coherence.  


## Conclusion  

In the WRF model, land use/land cover and soil type are mutually reinforcing inputs that jointly govern surface flux calculations and prognostic variables like t2m. While technically independent, their physiographic consistency is paramount to avoid numerical artifacts and ensure physically meaningful outputs. Future work should focus on dynamic coupling frameworks that automate LULC-soil harmonization, particularly for applications in climate change and land management.

## Citations

[^1]: <https://pmc.ncbi.nlm.nih.gov/articles/PMC5572266/>
[^2]: <https://pmc.ncbi.nlm.nih.gov/articles/PMC9661943/>
[^3]: <https://github.com/wrf-model/WRF/blob/master/run/SOILPARM.TBL>
[^4]: <http://rccdp.unl.edu/portal/WRF_Variable_Table.pdf>
[^5]: <https://forum.mmm.ucar.edu/threads/inconsistent-xland-and-isltyp.646/>
[^6]: <https://vlab.noaa.gov/web/emc/noah-lsm>
[^7]: <https://journals.ametsoc.org/view/journals/mwre/140/3/mwr-d-11-00056.1.xml>
[^8]: <https://pmc.ncbi.nlm.nih.gov/articles/PMC11191672/>
[^9]: <https://pmc.ncbi.nlm.nih.gov/articles/PMC6360509/>
