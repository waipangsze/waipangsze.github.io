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
```

## Installation via CMake (Recommended)

**TempestExtremes** can be built and installed on various systems using CMake. Our new script, `./quick_make_unix.sh` , automatically detects your platform(UNIX-based systems only) and loads any required modules before building.

# Tropical cyclone tracking (TCs)

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
