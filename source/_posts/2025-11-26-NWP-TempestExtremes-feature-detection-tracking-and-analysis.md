---
layout: post
title: NWP | TempestExtremes | feature detection, tracking, and analysis
categories: [NWP]
tags: [MPAS, NWP, WRF, ERA5, GFS, TC, cyclones]
author: wpsze
date: 2025-11-26 06:27:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/mzty5m2.png
banner_img: https://i.imgur.com/mzty5m2.png
---

# TempestExtremes

- <https://climate.ucdavis.edu/tempestextremes.php>
- <https://github.com/ClimateGlobalChange/tempestextremes>

TempestExtremes is a growing collection of **detection and characterization algorithms for large climate datasets**, leveraging C++ for rapid throughput and a command line interface that maximizes flexibility of each kernel. The tracking kernels in this package have been already 

- **used for tracking and characterizing tropical cyclones (TCs), extratropical cyclones (ETCs), monsoonal depressions, atmospheric blocks, atmospheric rivers, and mesoscale convective systems (MCSs).** 

By considering multiple extremes within the same framework, we can study the joint characteristics of extremes while minimizing the total data burden.

{% note primary %}
TempestExtremes (TE) is a multifaceted framework for feature detection, tracking, and scientific analysis of regional or global Earth system datasets on either rectilinear or **unstructured/native grids**.
{% endnote %}

- [Ullrich, P. A., Zarzycki, C. M., McClenny, E. E., Pinheiro, M. C., Stansfield, A. M., and Reed, K. A.: TempestExtremes v2.1: a community framework for feature detection, tracking, and analysis in large datasets, Geosci. Model Dev., 14, 5023–5048, https://doi.org/10.5194/gmd-14-5023-2021, 2021.](https://gmd.copernicus.org/articles/14/5023/2021/)
- [Ullrich, P.A. and C.M. Zarzycki (2017) "TempestExtremes v1.0: A framework for scale-insensitive pointwise feature tracking on unstructured grids" Geosci. Model. Dev. 10, pp. 1069-1090, doi: 10.5194/gmd-10-1069-2017.](http://dx.doi.org/10.5194/gmd-2016-217)
- [Zarzycki, C.M. and P.A. Ullrich (2017) "Assessing sensitivities in algorithmic detection of tropical cyclones in climate data" Geophys. Res. Lett. 44 (2), pp. 1141-1149, doi: 10.1002/2016GL071606.](http://dx.doi.org/10.1002/2016GL071606)

# Installation

## Installation via conda

TempestExtremes can be found on conda-forge here:

- <https://anaconda.org/conda-forge/tempest-extremes>

To install from conda use the command line:

```console
conda install -c conda-forge tempest-extremes
conda install -c conda-forge openmpi
```

## Installation via CMake (Recommended)

**TempestExtremes** can be built and installed on various systems using CMake. Our new script, `./quick_make_unix.sh` , automatically detects your platform(UNIX-based systems only) and loads any required modules before building.

# **Tropical cyclone tracking (TCs)

## Step 1: identify candidate storms 

```sh
DetectNodes
--in_data_list ERA5_TC_files.txt
--timefilter "6hr"
--out_file_list ERA5_DN_files.txt
--searchbymin MSL
--closedcontourcmd "MSL,200.0,5.5,0;
             _DIFF(Z(300hPa),Z(500hPa)),
             -58.8,6.5,1.0"
--mergedist 6.0
--outputcmd "MSL,min,0;
      _VECMAG(VAR_10U,VAR_10V),
       max,2;ZS,min,0"
```

- The first criterion we use for TCs is “MSL,200.0,5.5,0”, which indicates that mean sea level pressure must increase by 200 Pa over a 5.5∘ great circle distance (GCD) from the candidate point (the low-pressure region must be of sufficient magnitude and sufficiently compact to be considered coherent). 
- The second criterion is “_DIFF(Z(300hPa),Z(500hPa)), -58.8,6.5,1.0”, which indicates that the difference between geopotential (Z) on the 300 and 500 hPa surfaces must decrease by 58.8 m2 s−2 (equal to 6 m geopotential height) over a 6.5∘ GCD, using the maximum value of this field within 1∘ GCD as reference. 
- This second criterion indicates that there must be a coherent upper-level warm core attached to the local low so as to structurally differentiate these features from extratropical systems. 
- `mergedist`
	- DetectNodes merges candidate points with a distance (in degrees great-circle-distance) shorter than the specified value
- `outputcmd` indicates three additional outputs that are calculated and written as additional columns in each nodefile. 
	- here,  `“MSL,min,0”` outputs the value of MSL at the candidate point, `“_VECMAG(VAR_10U,VAR_10V),max,2”` outputs the maximum magnitude of the vector wind at 10 m altitude within 2∘ GCD of the candidate, and `“ZS,min,0”` outputs the surface height at the candidate point. **These variables are needed in the subsequent `StitchNodes` step to construct and filter TC trajectories.**

{% gi 2 2 %}
![The fairly stable warm core low is termed a “thermal low” and the potentially violent warm core low is termed a “tropical low”.](https://i.imgur.com/ec5zy6O.png)
![The cold core high is more common and significant near and during winter. In the polar and high latitude regions, very little sunlight absorbs into the ground surface. Thus, the ground is able to cool off to extremely cold temperatures. Cold air gets denser the colder it gets. Cold air is also very stable and will sink thus an air mass of very cold air will develop at the surface. ](https://i.imgur.com/KBMmlul.png)
{% endgi %}

- [HABYTIME MINI LECTURE 30: WARM CORE LOWS](https://theweatherprediction.com/habyhints2/613/)
- [HABYTIME MINI LECTURE 29: COLD VS. WARM CORE HIGH](https://theweatherprediction.com/habyhints2/612/)

## Step 2: connect candidate storms in time

Once TC candidates have been identified on each time slice, the `“stitching”` step in the algorithm ties these candidates together in time to form TC trajectories (the “Reduce” operation in the MapReduce paradigm). 

- Some features that are too weak, too short-lived, or too disorganized are eliminated from contention at this stage. 
- Also, features that are more likely related to topographic anomalies rather than real storms are also removed.

```sh
StitchNodes
--in_list ERA5_DN_files.txt
--out ERA5_TC_tracks.txt
--in_fmt "lon,lat,slp,wind,zs"
--range 8.0 --mintime "54h"
--maxgap "24h"
--threshold "wind,>=,10.0,10;
              lat,<=,50.0,10;
              lat,>=,-50.0,10;
              zs,<=,150.0,10"
```

The relevant tuning parameters are specified by 

- `range` 
	- the maximum distance (in ∘ GCD) that a feature can move between subsequent detections
- `mintime`
	- the minimum persistence time of each trajectory (calculated as the time between initiation and termination),
- `maxgap`
	- the maximum duration between two sequential detections
	- In particular, `maxgap` is a novel option that allows a path to be missing candidates for some time slices (for instance due to **temporary weakening of a TC** as it passes over land).
- `thresholds`
	- Four field-dependent thresholds are then specified for a trajectory to be accepted. 
		- The first threshold “wind,>=,10.0,10” indicates that the wind magnitude (derived from the “wind” column in the nodefile) must be greater than 10 m s−1 for at least 10 time slices; this ensures that these features are sufficiently intense to be classified as tropical storms. 
		- The next two thresholds “lat,<=,50.0,10;lat,>=,-50.0,10” indicate that the latitude of the feature must be between 50∘ S and 50∘ N for at least 10 time slices, so as to eliminate any extratropical features that could not have existed as tropical storms. 
		- The final threshold “zs,<=,150.0,10” indicates that the feature must exist at an elevation below 150 m for at least 10 time slices; this removes false alarms that can often appear in regions of rough topography that are associated with the sea level pressure correction.

### (Optional) Step 3: compute the outer radius of each tracked TC

1. [**tempestextremes/src/nodes/NodeFileEditor.cpp**](https://github.com/ClimateGlobalChange/tempestextremes/blob/v2.4/src/nodes/NodeFileEditor.cpp)

- As argued by Schenkel et al. (2017), the largest radius outside of the eyewall where the **azimuthally averaged wind speed exceeds 8 m s−1 (r8)** tends to be a good measure of the **size of a TC**. 
- In Stansfield et al. (2020), TE was used to examine the distribution of r8 among TCs in reanalysis data in ERA5 and a series of runs with the Community Earth System Model (CESM). 

```sh
NodeFileEditor
--in_data_list ERA5_TC_files.txt
--in_nodefile ERA5_TC_tracks.txt
--in_fmt "lon,lat,slp,wind,zs"
--out_nodefile ERA5_TC_radprofs.txt
--out_fmt "lon,lat,rsize,rprof"
--time_filter "6hr"
--calculate "rprof=radial_wind_profile
         (VAR_10U,VAR_10V,159,0.125);
          rsize=lastwhere(rprof,>,8)"
```

- The calculations requested from NodeFileEditor are specified by the calculate argument, executed from left to right. First the radial profile is computed with `radial_wind_profile (VAR_10U,VAR_10V,159,0.125)` and stored in variable `rprof`. These arguments indicate which variables should be used for the calculation and that the radial profile should **consist of 159 bins of width 0.125 deg GCD (~13.8km or 0.25deg/2=0.125deg)**.
	- very large range: 159 * 0.125 ~= 20 deg
	- may set half of it
- After the radial profile is calculated, the **last value where the radial wind profile** is greater than 8 m s−1 is located and written to the nodefile. The last value in the array is taken because we want to avoid recording the radius of the 8 m s−1 wind within the TC inner core. 
- **The number of bins and the bin width** were chosen based on the horizontal grid spacing of the ERA5 wind data, which is approximately 31 km. The bin width of 0.125∘ adequately samples points at this grid spacing to create the radial wind profiles. The number of bins ensures the radial averaging extended out far enough from the TC center points to capture the storms' complete wind circulations.


#### Error

##### EXCEPTION "time" variable in "/diag.nc" missing "calendar" attribute

- for `make_time_te_compliant.py`, modify 

```python
    if 'time' not in ncfile.variables:
        print("Add numeric time variable.")
        time_var = ncfile.createVariable('time', 'f', ('time',))
        time_var.units = f'hours since {ref_time_str}'
        time_var.long_name = 'valid time'
        time_var.calendar = 'gregorian'
        time_var[:] = time_diff_str
```

#### For MPAS, nodefile from StitchNodes,

```sh
echo "-------------- NodeFileEditor ---------------------"
#----------- settings from Ullrich et al. 2021 (GMD)
#----------- https://climate.ucdavis.edu/tempestextremes.php#DetectNodes
# -- The type of nodefile indicated by --in_nodefile: "DN" if from DetectNodes or "SN" if from StitchNodes.

NodeFileEditor \
--in_data_list "${tmpdir}/diag_filelist.txt" \
--in_connect $connectfile \
--in_nodefile "${tmpdir}/tc_tracks.txt" \
--in_nodefile_type SN \
--in_fmt "lon,lat,slp,wind,ter" \
--out_nodefile tc_tracks_radprofs.txt \
--out_fmt "lon,lat,slp,wind,ter,r8size,rprof" \
--calculate "rprof=radial_wind_profile(VAR_10U,VAR_10V,79,0.125);r8size=lastwhere(rprof,>,8)"
```

### (Optional) Step 4: build a mask using the outer radius of the storm

With the r8 value for each TC now in hand, we define **“TC-related precipitation”** as any precipitation which occurs at grid points that are considered part of a TC. Employing TE's NodeFileFilter command, precipitation outside of the circle with radius r8 centered on each TC is set to zero:

```sh
NodeFileFilter
--in_nodefile ERA5_TC_radprofs.txt
--in_fmt "lon,lat,rsize,rprof"
--in_data_list ERA5_precip_files.txt
--out_data_list ERA5_filtered_
                precip_files.txt
--var "PRECT"
--bydist "rsize"
```

- **TODO list on MPAS**

## Example

### `20250921_00 UTC`

- [Typhoon | Ragasa | 2025 | 颱風樺加沙](https://waipangsze.github.io/2025/09/24/Typhoon-Ragasa-2025/)

```log
There are 10 unique track IDs
There are 2 unique track IDs reaching at least CAT1 intensity
Number of TCs 10 
    Number of TD: 0
    Number of TS: 0
    Number of CAT1: 0
    Number of CAT2: 0
    Number of CAT3: 1
    Number of CAT4: 1
    Number of CAT5: 0
```

{% gi 4 1-1-2 %}
![](https://i.imgur.com/BPfdkJk.png)
![](https://i.imgur.com/mB6RcWt.png)
![1-hourly](https://i.imgur.com/AAEzwUu.png)
![6-hourly](https://i.imgur.com/4J8HANE.png)
{% endgi %}

### `20251005_00 UTC`

```log
There are 9 unique track IDs
There are 3 unique track IDs reaching at least CAT1 intensity
Number of TCs 9 
    Number of TD: 0
    Number of TS: 0
    Number of CAT1: 0
    Number of CAT2: 2
    Number of CAT3: 1
    Number of CAT4: 0
    Number of CAT5: 0
```

{% gi 4 1-1-2 %}
![](https://i.imgur.com/JEmJY5n.png)
![](https://i.imgur.com/TEYo3Lq.png)
![1-hourly](https://i.imgur.com/gOdbbOt.png)
{% endgi %}

# Extratropical cyclones (ETCs)

## Step 1: generate extratropical cyclone trajectories

```sh
DetectNodes
--in_data_list B20TRC5CNBDRD.
               001.PS_list.txt
--out cyclone_candidates
--closedcontourcmd "PSL,200.0,6.0,0"
--mergedist 6.0
--searchbymin "PSL"
--outputcmd "PSL,min,0"
```

- The algorithm applied here identifies cyclonic storms as sea level pressure minima. 
	- To avoid topographic lows and features that are not meteorological in character, additional criteria are imposed on the minimum lifetime and propagation distance.
- Our criterion for cyclonic storms is that the minimum pressure must be enclosed by a closed contour of 200 Pa within 6.0∘ of cyclone center. This minimum pressure location also defines the cyclone center. 

```sh
StitchNodes
--in_fmt "lon,lat,slp"
--in_list candidate_list.txt
--out etc-all-traj.txt
--range 6.0
--mintime 60h
--maxgap 18h
--min_endpoint_dist 12.0
--threshold "lat,>,24,1;lon,>,234,1;
       lat,<,52,1;lon,<,294,1"
```

- Here the StitchNodes thresholds require that storms persist for at least 60 h, with a maximum gap (time between sequential detections satisfying the DetectNodes criteria) of at most 18 h. 
- Furthermore, at least one point must pass through a geographic region (representing CONUS) bounded by 24 and 52∘ N latitude and 234 and 294∘ E longitude. 
- We also require ETCs move at least 12∘ GCD from the start to the end of the trajectory, as specified by the min_endpoint_dist argument, in order to eliminate stationary features (e.g., the Icelandic Low) and spurious shallow lows generated over regions of high topography.

## Step 2: filter out ETCs with sea level pressure above 990 hPa

In some cases, it may be desirable to filter out the more intense, potentially more impactful events. While the **definition of a strong ETC is inherently subjective**, we define a central pressure of 990 hPa as the demarcation between strong and moderate/weak ETCs as in Zhang and Colle (2017).

-  To do so, all ETCs tracked in step 1 are passed into `NodeFileEditor`, in which a new trajectory file specified by argument out_nodefile is generated with storms only possessing intensities of 990 hPa or lower:

```sh
NodeFileEditor
--in_nodefile etc-all-traj.txt
--in_data_list B20TRC5CNBDRD.
              001.PRECT_list.txt
--in_fmt "lon,lat,slp"
--out_fmt "lon,lat,slp"
--out_nodefile etc-strong-traj.txt
--colfilter "slp,<=,99000."
```

## Step 3: filter data within 25∘ of storm center and composite

Corresponding precipitation rate outputs from the same ensemble member are masked within 25∘ GCD of a storm center tracked in step 1. Here, all precipitation associated with the cyclone is retained, while all precipitation not within 25∘ of a storm is set to zero.

```sh
NodeFileFilter
--in_nodefile etc-all-traj.txt
--in_fmt "lon,lat,slp"
--in_data_list B20TRC5CNBDRD.
                001.PRECT_list.txt
--out_data_list B20TRC5CNBDRD.
              001.PRECT_FILT_list.txt
--var "PRECT"
--bydist 25.0
--maskvar "mask"
```

- As a last step, storm-centered composites are generated using the following command:

```sh
NodeFileCompose
--in_nodefile etc-strong-traj.txt
--in_fmt "lon,lat,slp"
--in_data_list B20TRC5CNBDRD.001.
               PRECT_FILT_list.txt
--out_data "composite_PRECT.nc"
--var "PRECT"
--max_time_delta "2h"
--op "mean"
--dx 1.0
--resx 80
```

Here, only ETCs filtered above to have **central SLP values below 990 hPa** are composited. Although we can composite any 2D field, here we apply the compositing tool to precipitation filtered by `NodeFileFilter`. The argument `max_time_delta` indicates that the data slice nearest in time to the tracked feature (within 2 h) should be composited – this is useful when the discrete times from data and features are not exactly aligned. The arithmetic mean is calculated centered on the storm location (see Sect. 2.4). The resulting stereographic composite has a grid spacing of 1∘ and a resolution of 80 × 80 grid points.

# Atmospheric rivers (ARs)

- **Atmospheric rivers (ARs) are thin and long filamentary structures characterized by high integrated vapor transport (IVT)**

## Step 1: detect ridges in the IVT field

- Here we have adopted the nomenclature of **ERA5** for **vertically integrated eastward vapor transport (VIWVE) and northward vapor transport (VIWVN)**.
- **Ridge points** are associated with high downward curvature and identified as those points where the Laplacian of the IVT field is below a fixed threshold (here chosen to be  $-2 \times 10^4$ kg m−2 s−1 rad−2).

$$
\mbox{IVT} = \sqrt{\mbox{VIWVE}^2 + \mbox{VIWVN}^2}
$$

```sh
DetectBlobs
--in_data_list ERA5_IVT_files.txt
--out_list ERA5_AR_files.txt
--timefilter "6hr"
--thresholdcmd "_LAPLACIAN{8,10}
  (_VECMAG(VIWVE,VIWVN)),<=,-20000,0"
--minabslat 15
--geofiltercmd "area,>=,4e5km2"
```

## Step 2: filter out tropical cyclones

-  This can be done **using the TC tracks produced above** to filter out points within a prescribed distance of each detected TC:

```sh
NodeFileFilter
--in_nodefile ERA5_TC_tracks.txt
--in_fmt "lon,lat,slp,wind,zs"
--in_data_list ERA5_AR_files.txt
--out_data_list ERA5_AR_NFF_files.txt
--var "binary_tag"
--bydist 8.0
--invert
```

## Step 3: apply AR mask to northward vapor transport field

- To now investigate AR and non-AR poleward moisture transport, we apply the mask generated in step 2 to the VIWVN field (northward vapor transport). Here we leverage the `VariableProcessor` executable, which allows us to apply TE's built-in operations on a set of input files. 

```sh
VariableProcessor
--in_data_list ERA5_VPIN.txt
--out_data_list ERA5_VPOUT.txt
--timefilter "6hr"
--var "_PROD(binary_tag,VIWVN);
     _PROD(_DIFF(1,binary_tag),VIWVN)"
--varout "VIWVN_PW_AR,VIWVN_PW_NONAR"
```

# Atmospheric blocking 

- Our final example addresses the development of a climatology of atmospheric blocking frequency. Atmospheric blocking events are synoptic-scale weather phenomena characterized by persistent obstruction of the normal westerly flow and are associated with heat waves, cold spells, flooding, and drought.

## Step 1: generate the blocking threshold

```sh
Climatology
--in_data_list MERRA2_H_files.txt
--out_data MERRA2_H_LTDM.nc
--var "H(500hPa)"
--period "daily"
--type "mean"
--missingdata
Climatology
--in_data_list MERRA2_H_files.txt
--out_data MERRA2_H2_LTDM.nc
--var "H(500hPa)"
--period "daily"
--type "meansq"
--missingdata
```

```sh
VariableProcessor
--in_data "MERRA2_H_LTDM.nc;
           MERRA2_H2_LTDM.nc"
--out_data "MERRA2_H_mean_stddev.nc"
--var "dailymean_H,
       _SQRT(_DIFF(dailymeansq_H,
       _POW(dailymean_H,2)))"
--varout "dailymean_H,stddev_H"
```

```sh
FourierFilter
--in_data MERRA2_H_mean_stddev.nc
--out_data MERRA2_H_mean_stddev
     _timesmoothed.nc
--var "dailymean_H,stddev_H"
--dim "time"
--modes 4
FourierFilter
--in_data MERRA2_H_mean_stddev
          _timesmoothed.nc
--out_data MERRA2_H_mean_stddev
           _smoothed.nc
--var "stddev_H"
--preserve "dailymean_H"
--dim "lon" --modes 2
```

```sh
VariableProcessor
--in_data MERRA2_H_mean
          _stddev_smoothed.nc
--out_data MERRA2_threshold
           _H_filtered.nc
--var "_SUM(dailymean_H,
       _MAX(100.0,
       _PROD(1.5,stddev_H)))"
--varout "threshold_H"
```

## Step 2: identify regions of geopotential above the blocking threshold

```sh
DetectBlobs
--in_data_list MERRA2_DB_files.txt
--out_list MERRA2_blocktag_files.txt
--thresholdcmd "_DIFF(H(500hPa),
                threshold_H),>=,0,0"
--minabslat 25
--maxabslat 75
--geofiltercmd "area,>,1e6km2"
--tagvar "block_tag"
```

## Step 3: enforce a minimum duration for blocks and build climatology

```sh
StitchBlobs
--in_list MERRA2_blocktag_files.txt
--out_list MERRA2_blockid_files.txt
--var "block_tag"
--mintime "5d"
--min_overlap_prev 20
--flatten
```

```sh
Climatology
--in_data_list MERRA2_blockid
              _files.txt
--out_data MERRA2_blocking_climo.nc
--var "object_id"
--period "seasonal"
```

# **Classification of Low-Pressure Systems (SyCLoPS)

- <https://github.com/yepkids/SyCLoPS/tree/main>
- [Han, Y. and P.A. Ullrich (2025) "The System for Classification of Low-Pressure Systems (SyCLoPS): An all-in-one objective framework for large-scale datasets" J. Geophys. Res. Atm. 130 (1), e2024JD041287, doi: 10.1029/2024JD041287.](https://doi.org/10.1029/2024JD041287)

The System for classification of Low-Pressure Systems (SyCLoPS) is an all-in-one framework for objective detection and **classification of any type of surface low-pressure systems (LPSs)** in any large atmospheric dataset and model outputs in one workflow. 

![](https://i.imgur.com/JgBTcS6.png)

- <https://github.com/yepkids/SyCLoPS/blob/main/TE_commands.sh>

{% fold info @TE_commands.sh %}
```sh
#!/bin/bash
# Please direct any questions to the author of this script: Yushan Han (yshhan@ucdavis.edu)
# Version: 2025-07-07
# This is a shell version of the SyCLoPS main Python program (SyCLoPS_main.py). It might help you to to debug the TempestExtremes (TE) commands if you ran into issues.
# A detailed TE documentation can be found at: https://climate.ucdavis.edu/tempestextremes.php. 
# We also provide a manual that explains some procedures and usages of SyCLoPS in more details. See README on GitHub.
# To debug, we recommended running the commands in this file one at a time.

# latname and lonname in the commands below only need to be specified when the latitude and longitude variables in the given dataset use different names other than the standard “lat” and “lon”. 
# Specify logdir to store temporary log files in the desired folder.

# Point to your TempestExtremes directory by specifying: 
TEMPESTEXTREMESDIR=~/tempestextremes/bin

# Define your input and output files (or file lists), for example:
inputfile="ERA5_lpsnode_in.txt"
outputfile="ERA5_lpsnode_out.txt"

# If there's a time dimension in some invariant surface geopotential (ZS) files, it needs to be removed (averaged) prior to the following procedures, which can be achived by sth like: "ncwa -a time ZS_in.nc ZS_out.nc".
# Please remember to change the variable names below according to your model data.

# The first TE command is to detect LPS nodes and output parameters for classification using DetectNodes:
# TE can parallel files by each time slice in the list when computing.
# Parameters are calculated under DetectNodes' outputcmd using variables in ERA5 in the following order: 
# MSLP, MSLPCC20, MSLPCC55, DEEPSHEAR, UPPTKCC, MIDTKCC, LOWTKCC, Z500CC, VO500AVG, RH100MAX, RH850AVG, T850, Z850, ZS, U850DIFF, WS200PMX.
# 300 hPa and 200 hPa data used in DetectNodes can be replaced by 250 hPa data (See SyCLoPS Appendix A & B). If using 250 hPa data, it's recommended to change the parameter name "WS200PMX" to "WS250PMX".
$TEMPESTEXTREMESDIR/DetectNodes \
--in_data_list $inputfile --out_file_list $outputfile \
--searchbymin MSL --closedcontourcmd "MSL,10,5.5,0" --mergedist 6.0 \
--outputcmd "MSL,min,0;MSL,posclosedcontour,2.0,0;MSL,posclosedcontour,5.5,0;\
_DIFF(_VECMAG(U(200hPa),V(200hPa)),_VECMAG(U(850hPa),V(850hPa))),avg,10.0;\
_DIFF(Z(300hPa),Z(500hPa)),negclosedcontour,6.5,1.0;\
_DIFF(Z(500hPa),Z(700hPa)),negclosedcontour,3.5,1.0;\
_DIFF(Z(700hPa),Z(925hPa)),negclosedcontour,3.5,1.0;\
Z(500hPa),posclosedcontour,3.5,1.0;VO(500hPa),avg,2.5;\
R(100hPa),max,2.5;R(850hPa),avg,2.5;T(850hPa),max,0.0;Z(850hPa),min,0;ZS,min,0;\
U(850hPa),posminusnegwtarea,5.5;_VECMAG(U(200hPa),V(200hPa)),maxpoleward,1.0" \
--timefilter "3hr" --latname "latitude" --lonname "longitude" --mergeequal --logdir "./TE_log"
#"_VECMAG(VAR_10U,VAR_10V),max,2.0" can be added to "--outputcmd" to calculate the optional "WS" parameter. If you add this, you will also need to add "WS" to the codes below and in the Python classifier.
#"Z(850hPa),min,0" (Z850) and "ZS,min,0" (ZS) are not necessary if the dataset contains NaNs or missing values (e.g. 1e20) where 850 hPa level is below the surface. However, we recommend keeping the ZS parameter for potential post-processing and analysis uses.
#"_CURL{8,2.5}(U(500hPa),V(500hPa)),min,0" can replace "VO(500hPa),avg,2.5" for VO500AVG if the relative vorticity (VO) is not directly available. The results will be slightly different, but close enough. Another option is to calculate VO at 500 hPa through 500 hPa U and V.
#"_CURL{16,2.5}(U(500hPa),V(500hPa)),min,0" is recommended to replace VO500AVG for high-resolution datasets (e.g. 0.25°).
#It is suggested to add the "--mergeequal" argument if you are using ERA5 dataset. This argument merge nodes that have the exact same MSLP values nearby in rare scinarios. ERA5 tends to have more of these cases because it has a relatively low precision (2 decimal places).

# Next, detected nodes are stitched in consecutive time with parameters’ name formatted using StitchNodes. The output is a csv file.
inputfile="ERA5_lpsnode_out.txt"
outputfile="ERA5_lps_tracks.csv"
$TEMPESTEXTREMESDIR/StitchNodes \
--in_list $inputfile --out $outputfile \
--in_fmt "lon,lat,MSLP,MSLPCC20,MSLPCC55,DEEPSHEAR,UPPTKCC,MIDTKCC,LOWTKCC,\
Z500CC,VO500AVG,RH100MAX,RH850AVG,T850,Z850,ZS,U850DIFF,WS200PMX" \
--range 4.0 --mintime "18h" --maxgap "12h" --threshold "MSLPCC55,>=,100.0,5" --out_file_format "csv"
# If you are using a different time resolution (e.g. 6hrs), please refer to the "Other Tips" section for information on how to set specific argument parameters.

#The following commands are not necessary if you do not need to classify tropical-like cyclones in the extratropical classification branch.
# To calculate cyclonic relative vorticity at 850 hPa and preserve U,V at 925 hPa for the next step.
# The cyclonic vorticity is defined to be always positive. Thus, we need to flip the sign of the relative vorticity field in the Southern Hemisphere.
inputfile="ERA5_sizeblob_in.txt" 
outputfile="ERA5_crvort_out.txt"
$TEMPESTEXTREMESDIR/VariableProcessor --in_data_list $inputfile --out_data_list $outputfile \
--var "_COND(_LAT(),_CURL{8,3}(U(850hPa),V(850hPa)),_PROD(_CURL{8,3}(U(850hPa),V(850hPa)),-1)),U(925hPa),V(925hPa)" --varout "Cyclonic_Vorticity,U925,V925" --latname latitude --lonname longitude

# Now, detect LPS size blobs for calculating LPS size using DetectBlobs. This command outputs detected features marked by binary masks:
inputfile="ERA5_crvort_out.txt" 
outputfile="ERA5_sizeblob_out.txt"
# The inputfile here should include the cyclonic relative vorticity files output by the last command (files in "ERA5_crvort_out.txt") and files of U and V at 925 hPa for the desired time frame.
$TEMPESTEXTREMESDIR/DetectBlobs --in_data_list $inputfile --out_list $outputfile \
--thresholdcmd "((Cyclonic_Vorticity,>=,2e-5,0) & (_VECMAG(U(925hPa),V(925hPa)),>=,12.0,0)) | (Cyclonic_Vorticity,>=,4e-5,0)" \
--geofiltercmd "area,>=,1e4km2" --tagvar "blobid" --latname latitude --lonname longitude --timefilter "3hr" --logdir "./TE_log"

# Lastly, derive properties of blobs using BlobStats for LPS node pairing and LPSAREA computation in the Python classifier.
# This command will output a list of information about blob's centroid, lat/lon maximum extent, and size along with their IDs.
# One can opt to calculate blobs’ IKE by the sumvar operation in the command (this is slow if single-threaded):
inputfile="ERA5_sizeblob_out.txt"
outputfile="ERA5_size_blob_stats.txt"
$TEMPESTEXTREMESDIR/BlobStats --in_list $inputfile --out_file $outputfile \
--findblobs --var "blobid" --out "centlon,centlat,minlat,maxlat,minlon,maxlon,area" \
--latname latitude --lonname longitude --out_fulltime
# add this optional line to calculate IKE for each blob/node: --sumvar "_PROD(_SUM(_POW(U(925hPa),2),_POW(V(925hPa),2)),0.5)"
# See manual 2.2 for more details on calculating IKE from BlobStats.

# After completing all the necessary steps of TE commands, 
# the last step is to execute the classifier python script to compute the final RAWAREA and LOWAREA values, form the input LPS catalog for the classification and output the final classified LPS catalog:

python SyCLoPS_classifier.py

#-----------------------------------Other Tips---------------------------------------#

# Note that the above TE commands are designed for 3-hourly data frequency. If a
# If the 6-hourly frequency detection rate is adopted, the following changes are required/recommended (follow the same logic for other frequencies):

# 1. In the DetectNodes and DetectBlobs commands, the time_filter argument should be tuned to “6hr" or just delete the argument.”
# 2. In the StitchNodes command, the range argument, which represents the maximum distance in GCD an LPS candidate can move between two consecutive time steps, should be
#   doubled to “8.0” or changed to “6.0” (a 6.0◦ GCD range should be enough to cover the vast majority of fast-moving LPSs).
# 3. In the StitchNodes command, a new argument “ --prioritize MSLP” should be added if the above range argument is set to “8.0”. (This method is experimental and preferred.)
# 4. Recommended: In the --threshold argument of the StitchNodes command, "5" means 5 steps which is equivalent to a (5-1)*3=12 hrs interval.
#   Thus, "MSLPCC55,>=,100.0,5" may be changed to --threshold "MSLPCC55,>=,100.0,3" if using 6-hourly detection rate.

# A sample code for specialized regional (Nordic Sea) PL detection (using 1-hour detection rate):

# $TEMPESTEXTREMESDIR/DetectNodes
# -–in_data_list $inputfile -–out_file_list $outputfile
# -–searchbymin MSL -–closedcontourcmd "MSL,10,5.5,0"
# -–mergedist 1.5
# -–outputcmd "..."
# -–minlat 45 -–maxlat 85 -–minlon 320 -–maxlon 70
# -–latname "latitude" -–lonname "longitude" -–logdir "./TE_log"

# $TEMPESTEXTREMESDIR/StitchNodes
# -–in_list $inputfile -–out $outputfile
# -–in_fmt "lon,lat,MSLP,MSLPCC20,MSLPCC55,DEEPSHEAR,UPPTKCC,MIDTKCC,LOWTKCC,\
# Z500CC,VO500AVG,RH100MAX,RH850AVG,T850,Z850,ZS,U850DIFF,WS200PMX" \
# -–range 1.4 -–mintime "6h" -–maxgap "6h" -–threshold "MSLPCC55,>=,100.0,7"
## Since some TLCs can be very short-lived, it is recommended to keep the step/hour requirements low in the --mintime, --maxgap and --threshold arguments here.
```
{% endfold %}

# **MPAS_tempest-extremes

- <https://github.com/falkojudt/MPAS_tempest-extremes>

```sh
#./src/step1_convert_lonVertex.sh
#./src/step2_SCRIP-file.sh
#./src/step3_connectivity-file.sh
./src/step4_submit_preproc_job.sh
./src/step5_make_filelists.sh
./src/step6_run_detectnodes_stitchnodes_batch.sh
```

# References

1. [Tropical Cyclones analysis with TempestExtremes](https://e3sm.atlassian.net/wiki/spaces/DOC/pages/924419368/Tropical+Cyclones+analysis+with+TempestExtremes)
2. [Zarzycki, C. M., Ullrich, P. A., & Reed, K. A. (2021). Metrics for evaluating tropical cyclones in climate data. Journal of Applied Meteorology and Climatology, 60(5), 643-660.](https://journals.ametsoc.org/view/journals/apme/60/5/JAMC-D-20-0149.1.pdf)
3. [DiGilio Jr, J. G. (2025). Capturing High-Impact Weather in a Fine-Scale Climate Simulation Using the Model for Prediction Across Scales-Atmosphere (MPAS-A) v 7.0 (Master's thesis, Northern Illinois University).](https://www.researchgate.net/publication/395336640_Capturing_High-Impact_Weather_in_a_Fine-Scale_Climate_Simulation_Using_the_Model_for_Prediction_Across_Scales_-_Atmosphere_MPAS-A_v_70)
4. [Han, Yushan, and Paul A. Ullrich. "The system for classification of low‐pressure systems (SyCLoPS): An all‐in‐one objective framework for large‐scale data sets." Journal of Geophysical Research: Atmospheres 130.1 (2025): e2024JD041287.](https://agupubs.onlinelibrary.wiley.com/doi/pdfdirect/10.1029/2024JD041287)
