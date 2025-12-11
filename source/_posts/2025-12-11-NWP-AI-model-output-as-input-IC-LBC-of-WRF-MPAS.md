---
layout: post
title: NWP | AI model output as input (IC/LBC) of WRF/MPAS
categories: NWP
tags: [NWP, ML, AI, WRF, MPAS, PanguWeather, ECMWF]
author: wpsze
date: 2025-12-11 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

- [**NWP | AI for Numerical Weather Prediction (NWP)**](https://waipangsze.github.io/2024/10/09/NWP-AI-for-Numerical-Weather-Prediction-NWP/)
- [NWP | ECMWF | IFS | Open data](https://waipangsze.github.io/2024/11/12/ECMWF-IFS/)

---

# Introduction

As the field of Numerical Weather Prediction (NWP) rapidly evolves, the integration of AI-driven forecasting models like ECMWF's Pangu-Weather offers incredible speed and efficiency. This post documents a technical workflow where I used the Pangu-Weather model to generate a 10-day forecast and encountered a critical issue when attempting to initialize the Weather Research and Forecasting (WRF) model: **the absence of crucial soil temperature and soil moisture variables** in the Pangu-Weather GRIB output.

This article outlines the steps taken, the problem encountered, and the solution involving the use of auxiliary data from the ECMWF's operational Integrated Forecasting System (IFS).

# Literature review

## AI-Driven WRF (2025)

- [Xu, H., Zhao, Y., Dajun, Z., Duan, Y., & Xu, X. (2025). Exploring the typhoon intensity forecasting through integrating AI weather forecasting with regional numerical weather model. npj Climate and Atmospheric Science, 8(1), 38.](https://www.nature.com/articles/s41612-025-00926-z.pdf)
	- we propose an attractive approach by initiating regional Weather Research and Forecasting (WRF) model with Pangu-weather, a state-of-the-art AI weather forecasting system (AI-Driven WRF), whose forecasting power can be further augmented by the implementation of dynamic vortex initialization.
	- ![](https://i.imgur.com/1MtDXOA.png)

---

# The NWP Workflow: AI Model to WRF Input

The process involves several standard steps in an NWP setup, with the AI model replacing the initial global model forecast:

## 1. AI Forecast Generation (ECMWF Pangu-Weather)

I utilized the ECMWF implementation of the Pangu-Weather AI model to produce a 10-day forecast.

* **Model Used:** `ai-models` (specifically utilizing the `pangu_weather_6.onnx` and `pangu_weather_24.onnx` models for 6-hourly and 24-hourly steps).
* **Time Interval:** A full 10-day forecast with output at 6-hour intervals.
* **Output File:** A single large GRIB file named `panguweather.grib`.

- <https://waipangsze.github.io/2024/10/09/NWP-AI-for-Numerical-Weather-Prediction-NWP/#practice-2025-12-09>

> **Key takeaway:** The Pangu-Weather model is trained primarily to forecast atmospheric variables like geopotential, wind components, temperature, and specific humidity. It does not natively output the necessary land surface variables (soil temperature and soil moisture) required for the WRF model's `ungrib` and `metgrid` steps.


## 2. Preparing GRIB Files for WRF

The single 10-day GRIB file needed to be processed for the WRF Preprocessing System (WPS).

* **Step 2a: Splitting the GRIB file**
    The monolithic `panguweather.grib` file, containing all timesteps, was split into individual GRIB2 files, one for each 6-hour forecast time. This is often necessary for robust processing by the WPS utilities.

	* Method 1: Using CDO (Climate Data Operators)

		* CDO is the simplest command-line solution and is often pre-installed or easily available in scientific computing environments.

		* **CDO Command: `splitsel`**

		* The `splitsel,1` operator is designed to split a file into one output file per timestep (or more accurately, one file per selection/record). Since IFS GRIB files often have multiple fields (parameters, levels) per timestep, this command works by splitting based on the internal record count.

		```bash
		# Syntax: cdo -splitsel,N <input_file> <output_prefix>
		# N=1 means split after every single record/timestep.

		cdo -splitsel,1 input_ifs.grib2 output_timestep_
		```

		* **Output Files:** This will generate files named sequentially, like `output_timestep_000001.grib2`, `output_timestep_000002.grib2`, etc.

* **Step 2b: The `ungrib` Step**
    The `ungrib.exe` utility reads the meteorological data from the GRIB files and extracts/interpolates them onto a model-ready intermediate file format (e.g., `AI:YYYY-MM-DD_HH`).

    * **Action:** Ran `ungrib.exe` on the individual Pangu-Weather GRIB files.
    * **Result:** `ungrib` processed the atmospheric variables successfully.



* **Step 2c: The `metgrid` Step**
    The `metgrid.exe` utility takes the intermediate files and horizontally interpolates the data onto the user-defined WRF grid domains.

    * **Action:** Ran `metgrid.exe`.
    * **Result:** Successful, but the resulting `met_em.*` files were deficient.

{% fold info @ncdump -h met_em.d01.2025-12-08_00\:00\:00.nc %}
```log
netcdf met_em.d01.2025-12-08_00\:00\:00 {
dimensions:
	Time = UNLIMITED ; // (1 currently)
	DateStrLen = 19 ;
	west_east = 151 ;
	south_north = 117 ;
	num_metgrid_levels = 14 ;
	south_north_stag = 118 ;
	west_east_stag = 152 ;
	z-dimension0003 = 3 ;
	z-dimension0132 = 132 ;
	z-dimension0012 = 12 ;
	z-dimension0016 = 16 ;
	z-dimension0020 = 20 ;
variables:
	char Times(Time, DateStrLen) ;
	float PRES(Time, num_metgrid_levels, south_north, west_east) ;
	float VV(Time, num_metgrid_levels, south_north_stag, west_east) ;
	float UU(Time, num_metgrid_levels, south_north, west_east_stag) ;
	float TT(Time, num_metgrid_levels, south_north, west_east) ;
	float PMSL(Time, south_north, west_east) ;
	float OL4SS(Time, south_north, west_east) ;
	float OL3SS(Time, south_north, west_east) ;
	float OL2SS(Time, south_north, west_east) ;
	float OL1SS(Time, south_north, west_east) ;
	float OA4SS(Time, south_north, west_east) ;
	float OA3SS(Time, south_north, west_east) ;
	float OA2SS(Time, south_north, west_east) ;
	float OA1SS(Time, south_north, west_east) ;
	float VARSS(Time, south_north, west_east) ;
	float CONSS(Time, south_north, west_east) ;
	float OL4LS(Time, south_north, west_east) ;
	float OL3LS(Time, south_north, west_east) ;
	float OL2LS(Time, south_north, west_east) ;
	float OL1LS(Time, south_north, west_east) ;
	float OA4LS(Time, south_north, west_east) ;
	float OA3LS(Time, south_north, west_east) ;
	float OA2LS(Time, south_north, west_east) ;
	float OA1LS(Time, south_north, west_east) ;
	float VARLS(Time, south_north, west_east) ;
	float CONLS(Time, south_north, west_east) ;
	float IRRIGATION(Time, south_north, west_east) ;
	float SANDFRAC(Time, south_north, west_east) ;
	float CLAYFRAC(Time, south_north, west_east) ;
	float EROD(Time, z-dimension0003, south_north, west_east) ;
	float CANFRA(Time, south_north, west_east) ;
	float IMPERV(Time, south_north, west_east) ;
	float FRC_URB2D(Time, south_north, west_east) ;
	float URB_PARAM(Time, z-dimension0132, south_north, west_east) ;
	float LAKE_DEPTH(Time, south_north, west_east) ;
	float VAR_SSO(Time, south_north, west_east) ;
	float OL4(Time, south_north, west_east) ;
	float OL3(Time, south_north, west_east) ;
	float OL2(Time, south_north, west_east) ;
	float OL1(Time, south_north, west_east) ;
	float OA4(Time, south_north, west_east) ;
	float OA3(Time, south_north, west_east) ;
	float OA2(Time, south_north, west_east) ;
	float OA1(Time, south_north, west_east) ;
	float VAR(Time, south_north, west_east) ;
	float CON(Time, south_north, west_east) ;
	float SNOALB(Time, south_north, west_east) ;
	float LAI12M(Time, z-dimension0012, south_north, west_east) ;
	float GREENFRAC(Time, z-dimension0012, south_north, west_east) ;
	float ALBEDO12M(Time, z-dimension0012, south_north, west_east) ;
	float SCB_DOM(Time, south_north, west_east) ;
	float SOILCBOT(Time, z-dimension0016, south_north, west_east) ;
	float SCT_DOM(Time, south_north, west_east) ;
	float SOILCTOP(Time, z-dimension0016, south_north, west_east) ;
	float SOILTEMP(Time, south_north, west_east) ;
	float HGT_M(Time, south_north, west_east) ;
	float LU_INDEX(Time, south_north, west_east) ;
	float LANDUSEF(Time, z-dimension0020, south_north, west_east) ;
	float COSALPHA_V(Time, south_north_stag, west_east) ;
	float SINALPHA_V(Time, south_north_stag, west_east) ;
	float COSALPHA_U(Time, south_north, west_east_stag) ;
	float SINALPHA_U(Time, south_north, west_east_stag) ;
	float XLONG_C(Time, south_north_stag, west_east_stag) ;
	float XLAT_C(Time, south_north_stag, west_east_stag) ;
	float LANDMASK(Time, south_north, west_east) ;
	float COSALPHA(Time, south_north, west_east) ;
	float SINALPHA(Time, south_north, west_east) ;
	float F(Time, south_north, west_east) ;
	float E(Time, south_north, west_east) ;
	float MAPFAC_UY(Time, south_north, west_east_stag) ;
	float MAPFAC_VY(Time, south_north_stag, west_east) ;
	float MAPFAC_MY(Time, south_north, west_east) ;
	float MAPFAC_UX(Time, south_north, west_east_stag) ;
	float MAPFAC_VX(Time, south_north_stag, west_east) ;
	float MAPFAC_MX(Time, south_north, west_east) ;
	float MAPFAC_U(Time, south_north, west_east_stag) ;
	float MAPFAC_V(Time, south_north_stag, west_east) ;
	float MAPFAC_M(Time, south_north, west_east) ;
	float CLONG(Time, south_north, west_east) ;
	float CLAT(Time, south_north, west_east) ;
	float XLONG_U(Time, south_north, west_east_stag) ;
	float XLAT_U(Time, south_north, west_east_stag) ;
	float XLONG_V(Time, south_north_stag, west_east) ;
	float XLAT_V(Time, south_north_stag, west_east) ;
	float XLONG_M(Time, south_north, west_east) ;
	float XLAT_M(Time, south_north, west_east) ;
// global attributes:
		:TITLE = "OUTPUT FROM METGRID V4.6.0" ;
		:SIMULATION_START_DATE = "2025-12-08_00:00:00" ;
		:WEST-EAST_GRID_DIMENSION = 152 ;
		:SOUTH-NORTH_GRID_DIMENSION = 118 ;
		:BOTTOM-TOP_GRID_DIMENSION = 14 ;
		:WEST-EAST_PATCH_START_UNSTAG = 1 ;
		:WEST-EAST_PATCH_END_UNSTAG = 151 ;
		:WEST-EAST_PATCH_START_STAG = 1 ;
		:WEST-EAST_PATCH_END_STAG = 152 ;
		:SOUTH-NORTH_PATCH_START_UNSTAG = 1 ;
		:SOUTH-NORTH_PATCH_END_UNSTAG = 117 ;
		:SOUTH-NORTH_PATCH_START_STAG = 1 ;
		:SOUTH-NORTH_PATCH_END_STAG = 118 ;
		:GRIDTYPE = "C" ;
		:DX = 54000.f ;
		:DY = 54000.f ;
		:DYN_OPT = 2 ;
		:CEN_LAT = 33.86901f ;
		:CEN_LON = 101.666f ;
		:TRUELAT1 = 33.87f ;
		:TRUELAT2 = 33.87f ;
		:MOAD_CEN_LAT = 33.86901f ;
		:STAND_LON = 102.186f ;
		:POLE_LAT = 90.f ;
		:POLE_LON = 0.f ;
		:corner_lats = 1.56295f, 50.91934f, 51.36064f, 1.790321f, 1.498215f, 50.79367f, 51.2374f, 1.72683f, 1.36425f, 51.11355f, 51.55564f, 1.590767f, 1.299759f, 50.98765f, 51.43218f, 1.527496f ;
		:corner_lons = 69.9118f, 43.32767f, 159.9401f, 133.7513f, 69.71295f, 43.01984f, 160.2521f, 133.951f, 69.97626f, 43.1283f, 160.1375f, 133.688f, 69.77777f, 42.81989f, 160.4501f, 133.8874f ;
		:MAP_PROJ = 1 ;
		:MMINLU = "MODIFIED_IGBP_MODIS_NOAH" ;
		:NUM_LAND_CAT = 20 ;
		:ISWATER = 17 ;
		:ISLAKE = -1 ;
		:ISICE = 15 ;
		:ISURBAN = 13 ;
		:ISOILWATER = 14 ;
		:grid_id = 1 ;
		:parent_id = 1 ;
		:i_parent_start = 1 ;
		:j_parent_start = 1 ;
		:i_parent_end = 152 ;
		:j_parent_end = 118 ;
		:parent_grid_ratio = 1 ;
		:sr_x = 1 ;
		:sr_y = 1 ;
		:NUM_METGRID_SOIL_LEVELS = 0 ;
		:FLAG_METGRID = 1 ;
		:FLAG_EXCLUDED_MIDDLE = 0 ;
		:FLAG_SLP = 1 ;
		:FLAG_MF_XY = 1 ;
		:FLAG_LAI12M = 1 ;
		:FLAG_VAR_SSO = 1 ;
		:FLAG_LAKE_DEPTH = 1 ;
		:FLAG_URB_PARAM = 1 ;
		:FLAG_FRC_URB2D = 1 ;
		:FLAG_IMPERV = 1 ;
		:FLAG_CANFRA = 1 ;
		:FLAG_EROD = 1 ;
		:FLAG_CLAYFRAC = 1 ;
		:FLAG_SANDFRAC = 1 ;
}
```
{% endfold %}

---

## The Critical Problem: Missing Soil Variables

Upon inspecting the `met_em.*` files generated by `metgrid`, a crucial issue was identified (often logged as an error or warning during `metgrid`, or discovered later during the `real` step):

**The `met_em` files contained only atmospheric variables (geopotential height, temperature, wind, pressure, specific humidity, ...) but were completely missing land surface variables, specifically:**

* **Soil Temperature (ST000010, etc.)**
* **Soil Moisture (SM000010, etc.)**

![Left: IFS met_em. Right: AI met_em](https://i.imgur.com/DMNgLdw.png)

The WRF model requires these variables to correctly initialize the land surface physics (Land Surface Model, LSM) and maintain a realistic energy and water balance at the lower boundary. Initializing WRF without them will lead to incorrect surface fluxes, potentially causing model "blow-up" or severe biases in near-surface forecasts.

---

## The Solution: Auxiliary Data Sourcing

The fix requires obtaining the missing soil variables from a reliable source and merging them into the initialization data.

### Strategy: Using ECMWF-IFS Operational Data

Since `Pangu-Weather` is a re-forecast product based on ECMWF training data (**ERA5**), the logical step is to use the **ECMWF's Integrated Forecasting System (IFS)** operational analysis or short-range forecast for the same initialization time.

The solution steps are:

1.  **Download Auxiliary Data:** Obtain the required soil variables (Soil Temperature and Volumetric Soil Water/Moisture) from the ECMWF's high-resolution operational model (HRES) or reanalysis (ERA5) for the forecast start time.
    * **Variables:** Typically required at four soil levels.
2.  **Separate GRIB Processing:** Process this auxiliary IFS GRIB file *separately* through `ungrib` and `metgrid`.
    * The IFS data will be processed by WPS to create a set of `met_em.*` files containing **only the soil variables**.
3.  **Data Merging (Copy/Overwrite):** Use a utility to copy/merge the soil variables from the IFS-derived `met_em.*` files into the Pangu-Weather derived `met_em.*` files.

> **Conceptual Code Snippet (Using NetCDF tools like NCO):**
>
> 1.  Generate `met_em_pangu.*` (Pangu-Weather, missing soil)
> 2.  Generate `met_em_ifs_soil.*` (IFS, only soil variables)
> 3.  Merge:
>     ```bash
>     # Example using NCO/NetCDF command to copy variable 'TSLB' (Soil Temp)
>     ncks -A met_em_ifs_soil.d01.2025-12-11_00:00:00 met_em_pangu.d01.2025-12-11_00:00:00 -v TSLB
>     ```
4.  **Verification:** Verify the final, merged `met_em.*` files contain all necessary atmospheric (from Pangu) and land surface (from IFS) fields.


