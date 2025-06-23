---
layout: post
title: MPAS | -fp-model precise
categories: [MPAS]
tags: [NWP, MPAS, WRF, partition, precise, intel, gfortran, ifort]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-06-11 14:08:00
index_img: https://i.imgur.com/OFGJ40i.png
banner_img: https://i.imgur.com/OFGJ40i.png
---

# Overview

The `-fp-model precise` flag is a compiler option used to control floating-point behavior in programs, ensuring strict adherence to value-safe optimizations when implementing floating-point calculations. This option is primarily available in Intel compilers for Linux and macOS systems, with Microsoft Visual C++ using the equivalent `/fp:precise` syntax on Windows.

## Mathematical Behavior

The compiler strictly adheres to IEEE-754 standards for floating-point operations. It does not perform algebraic transformations on floating-point expressions, such as reassociation or distribution, unless it can guarantee the transformation produces a bitwise identical result. This means expressions like `a * b + a * c` will not be optimized to `a * (b + c)` under this mode.

Special values including NaN (Not a Number), positive infinity, negative infinity, and negative zero are processed according to IEEE-754 specifications. For example, the expression `x != x` evaluates to true if `x` is NaN.

# MPAS

- **MPAS Version 5.0**
  - <https://github.com/MPAS-Dev/MPAS-Model/releases>

{% note primary %}
The ability to obtain **bit-identical results for any MPI task count** (though this may require the addition of compiler flags, e.g., `'-fp-model precise'` for the ifort compiler)
{% endnote %}

## Experiment (no precise mode)

- MPASv8.2.2 by intel compiler
- MPAS-120km: x1.40962
- Core = 2, 4, 8, 16, 24, 32, 48, 68
- Initial datetime = 2023-04-02_00UTC
- IC source = ERA5

### Elapsed time

| Step | Mesh                | IC         | Elapsed time  | Core |
|------|---------------------|------------|---------------|------|
| init | MPAS-120km-MPASv822 | 2023040200 | <60s          |   68 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 2h8m          |   68 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 3h30m         |   48 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 4h51m         |   32 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 9h24m         |   24 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 16h50m        |   16 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 25h33m        |    8 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 49h20m        |    4 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 62h36m        |    2 |

![](https://i.imgur.com/0qiFjTO.png){width=600}

### Plot

1. diag.2023-04-14_00.00.00.nc (Core-68 **minus** Core-2)
{% gi 12 2-2-2-2-2-2 %}
![](https://i.imgur.com/CH5cL1P.png)
![](https://i.imgur.com/1B7FzCR.png)
![](https://i.imgur.com/s6ncKk4.png)
![](https://i.imgur.com/n0CiCtM.png)
![](https://i.imgur.com/NVOrNkR.png)
![](https://i.imgur.com/GUVYxAW.png)
![](https://i.imgur.com/LsW2JFf.png)
![](https://i.imgur.com/JUhZrIS.png)
![](https://i.imgur.com/MsOXCEp.png)
![](https://i.imgur.com/6EKAugi.png)
![](https://i.imgur.com/ANWJQ0J.png)
![](https://i.imgur.com/F4OyKbz.png)
{% endgi %}

![The t2m difference from 2-core. (Averging t2m)](https://i.imgur.com/BccijjL.png)

# WRF/MPAS Forum

1. [different results from different number of processors (resolved) | Feb 9, 2021](https://forum.mmm.ucar.edu/threads/different-results-from-different-number-of-processors-resolved.10017/)
   1. We have seen similar issues that appear to be the result of compiler optimizations of various sorts. Have you tried **turning off optimizations** in the make target for your compiler **in the top-level Makefile** to see whether that enables you to get bitwise identical results for different MPI task counts?
   2. For the Intel Fortran compiler (ifort) specifically, it can help to add the `'-fp-model precise' flag` (as I've done in the above example).
      1. ```makefile
          ifort:
            ( $(MAKE) all \
            "FC_PARALLEL = mpif90" \
            "CC_PARALLEL = mpicc" \
            "CXX_PARALLEL = mpicxx" \
            "FC_SERIAL = ifort" \
            "CC_SERIAL = icc" \
            "CXX_SERIAL = icpc" \
            "FFLAGS_PROMOTION = -real-size 64" \
            "FFLAGS_OPT = -O0 -fp-model precise -convert big_endian -free -align array64byte" \
            "CFLAGS_OPT = -O0 -fp-model precise" \
            "CXXFLAGS_OPT = -O0" \
            "LDFLAGS_OPT = -O0" \
            "FFLAGS_DEBUG = -g -convert big_endian -free -CU -CB -check all -fpe0 -traceback" \
            "CFLAGS_DEBUG = -g -traceback" \
            "CXXFLAGS_DEBUG = -g -traceback" \
            "LDFLAGS_DEBUG = -g -fpe0 -traceback" \
            "FFLAGS_OMP = -qopenmp" \
            "CFLAGS_OMP = -qopenmp" \
            "CORE = $(CORE)" \
            "DEBUG = $(DEBUG)" \
            "USE_PAPI = $(USE_PAPI)" \
            "OPENMP = $(OPENMP)" \
            "CPPFLAGS = $(MODEL_FORMULATION) -D_MPI" )
          ```
    3. Another test that might be worth trying is to turn off all physics schemes and see whether you get bitwise identical results with just the dynamical core; that might help in tracking the issue down to either physics or dynamics. The easiest way to turn off physics in the v7.0 release of the model would be to set in the &physics group in the `namelist.atmosphere` file.
       1. `config_physics_suite = 'none'`
2. [Inconsistent WRF results when using different number of cores | Sep 7, 2024](https://forum.mmm.ucar.edu/threads/inconsistent-wrf-results-when-using-different-number-of-cores.19009/)
   1. I am running a large-domain WRF v4.5.2 simulation on Derecho with varying core counts. The simulation setup is identical across runs, except for the number of cores used. However, when comparing the output at the same timestamps, I noticed significant differences in the results dependent on the core count. **For instance, after a five-day period, the 2-m air temperature between runs can differ by as much as ±10 K in some grid cells**. After several tests, I found that this discrepancy is caused by the optimization settings during compilation. When I disable optimization during compilation, the results are identical regardless of the core count.
      1. So why does the optimization during compilation result in different outcomes when varying the number of cores? Could this be related to the size of the decomposed tiles and their communication across cores?
   2. **We are well aware that different number of processors will lead to slightly different model results**. This is **caused by higher level of optimization**.
   3. **To run WRF with low level of optimization could be expensive**. *As an alternative, we always suggest users to stay with the same number of processors*.