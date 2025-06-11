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