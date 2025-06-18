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

## Experiment

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

![](https://i.imgur.com/fDPKj5h.png){width=600}

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