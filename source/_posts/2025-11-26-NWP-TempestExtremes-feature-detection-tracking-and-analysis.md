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
