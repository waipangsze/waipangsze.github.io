---
layout: post
title: NWP | Spectral Method
categories: [NWP]
tags: [NWP, ECMWF, spherical harmonics, Truncation for wave numbers]
author: wpsze
date: 2025-03-20 11:56:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/vziet9g.png
banner_img: https://i.imgur.com/vziet9g.png
---

# Spectral methods

**Spectral methods** in numerical weather prediction (NWP) are advanced computational techniques that use global basis functions to solve the atmospheric governing equations with high accuracy. These methods are particularly effective for simulating large-scale weather patterns due to their exponential convergence properties when solutions are smooth. Here's a detailed breakdown:

## Core Concept of Spectral Methods  
Spectral methods approximate meteorological variables (e.g., wind, temperature) by expanding them into series of orthogonal basis functions, such as **spherical harmonics** for global atmospheric models. For example, a variable $$ u(x,t) $$ might be written as:  
$$
u(x,t) = \sum_{n=0}^N a_n(t) \phi_n(x)
$$  
where $\phi_n(x)$ are **pre-defined basis functions** (e.g., **Fourier modes or Chebyshev polynomials**), and $a_n(t)$ are time-dependent coefficients. Derivatives are computed analytically using the properties of these functions, avoiding the numerical errors common in finite-difference approximations.

### ECMWF: Spherical harmonics

**Spherical harmonics** are a set of orthogonal basis functions defined on the surface of a sphere. They are used to represent atmospheric variables such as wind, temperature, and pressure in terms of a series expansion. 

![Some ECMWF data produced by the IFS is stored in GRIB with gridType=sh to indicate that the values are stored as spherical harmonic coefficients.](https://i.imgur.com/vziet9g.png)
![The figure depicts the spherical harmonic (n, m) wave number space for a triangular truncation,  T.](https://i.imgur.com/ZQvmeDV.png)

{% gi 6 3-3%}
![](https://i.imgur.com/jpNOHrs.png)
![](https://i.imgur.com/zplgjZO.png)
![](https://i.imgur.com/aG9HKx7.png)
![](https://i.imgur.com/kCsOEPO.png)
![](https://i.imgur.com/Wny2y5F.png)
![](https://i.imgur.com/Bfpm51t.png)
{% endgi %}

#### [How to find the resolution of a spherical harmonic field in GRIB](https://confluence.ecmwf.int/display/UDOC/How+to+find+the+resolution+of+a+spherical+harmonic+field+in+GRIB+-+ecCodes+GRIB+FAQ)

```sh
$ grib_get -p J t1000.grib
1279
```
In this case, the value of `J=1279` indicates the triangular truncation used is `T1279`.

It is not possible to relate the resolution of the spherical harmonic data directly to the resolution of the corresponding grid as this depends on the specific relationship between the spectral representation and the Gaussian grid used by the IFS.  Three different relationships have been used by ECMWF over the years.  For **a triangular truncation of T**, the corresponding Gaussian grid resolutions are:

|      Relationship       |    Gaussian grid resoluion          |
|-------------|--------------|
| linear grid:  the shortest wavelength is represented by 2 grid point     |  N = (T + 1) / 2 |
| quadratic grid: the shortest wavelength is represented by 3 grid points	  |  N = 3 (T+1) / 4 | 
| cubic	grid: the shortest wavelength is represented by 4 grid points      |  N = T + 1       |

where **N** corresponds to the number of latitude lines between the poles and the equator.  However, to determine which relationship was used when the data were produced requires interrogation of a corresponding grid point field.

![](https://i.imgur.com/p6IPoiG.png)

## Advantages in NWP  

- **Exponential Convergence**: For smooth solutions, spectral methods **achieve errors decreasing faster than any polynomial of the grid size**, making them highly efficient for large-scale simulations.  
- **Global Representation**: Unlike finite-element or finite-volume methods, which approximate solutions locally, **spectral methods inherently model atmospheric waves (e.g., Rossby waves) across the entire domain, crucial for global weather prediction**.  
- **Computational Efficiency**: Operations like differentiation are performed in spectral space **via transforms (e.g., Fast Fourier Transforms), reducing computational cost** compared to traditional grid-based methods.

## Implementation in NWP Models  

Most global NWP models (e.g., *ECMWF’s IFS*) use spectral methods for **horizontal discretization**, combined with finite-difference or finite-volume techniques in the vertical direction. The process involves:  

1. **Transform**: Convert grid-point data to spectral coefficients using spherical harmonic transforms.  
2. **Spectral Computation**: Solve dynamical equations (e.g., momentum, continuity) in spectral space.  
3. **Inverse Transform**: Convert results back to physical space for parameterizations (e.g., cloud physics).

For example, the **PEAK model** (Primitive-Equation Atmospheric Research Model Kernel) demonstrates how spectral discretization handles the shallow-water equations and primitive equations, balancing accuracy and computational cost.

## Limitations  

- **Localized Features**: **Sharp gradients (e.g., thunderstorms) are poorly resolved due to the global nature of basis functions**, often requiring hybrid approaches.  
- **Spectral Truncation**: Retaining only a finite number of basis functions introduces aliasing errors in nonlinear terms, mitigated via techniques like dealiasing.

## Comparison to Finite Methods  

| **Aspect**               | **Spectral Methods**          | **Finite-Difference/Volume**  |  
|--------------------------|--------------------------------|--------------------------------|  
| **Accuracy**             | Exponential for smooth solutions | Polynomial (e.g., $O(h^2)$) |  
| **Domain Handling**       | Global                         | Local                          |  
| **Computational Cost**    | Lower for high accuracy        | Higher for equivalent resolution |  
| **Shock Capture**         | Poor (no built-in dissipation) | Better with flux limiters      |  

## Applications  

Spectral methods dominate **global NWP** due to their efficiency in simulating planetary-scale dynamics. Regional models, which require localized precision, often prefer finite-volume methods. In summary, **spectral methods excel in global NWP by leveraging smooth basis functions for accurate, efficient simulations of large-scale atmospheric behavior**, **while hybrid models address their limitations in resolving localized phenomena**.

# References

1. [Ehrendorfer, Martin. Spectral numerical weather prediction models. Society for Industrial and Applied Mathematics, 2011.](https://sites.google.com/view/spectralnwpmodels)
2. [A new grid for the IFS | 2016](https://www.ecmwf.int/sites/default/files/elibrary/2016/17262-new-grid-ifs.pdf)
   1. [The new ECMWF interpolation package MIR | 2017](https://www.ecmwf.int/en/newsletter/152/computing/new-ecmwf-interpolation-package-mir)
3. [How to access the data values of a **spherical harmonic field** in GRIB | 2023](https://confluence.ecmwf.int/display/UDOC/How+to+access+the+data+values+of+a+spherical+harmonic+field+in+GRIB+-+ecCodes+GRIB+FAQ)
4. [Wedi, Nils P., Mats Hamrud, and George Mozdzynski. "**A fast spherical harmonics transform** for global NWP and climate models." Monthly Weather Review 141.10 (2013): 3450-3461. | ECMEF](https://journals.ametsoc.org/downloadpdf/view/journals/mwre/141/10/mwr-d-13-00016.1.pdf)
5. [The spectral technique | Reading | 1983 | M. Jarraud and A.J. Simmons](https://www.ecmwf.int/sites/default/files/elibrary/1983/10253-spectral-technique.pdf)
6. [Chu, Peter C., Xiong-Shan Chen, and Chenwu Fan. "Comparison between wavenumber truncation and horizontal diffusion methods in spectral models." Monthly weather review 129.1 (2001): 152-158.](https://journals.ametsoc.org/downloadpdf/view/journals/mwre/129/1/1520-0493_2001_129_0152_cbwtah_2.0.co_2.pdf)
7. [Spectral Technique in Atmospheric Modelling | 07 October, 2021](https://imdpune.gov.in/training/training%20notes/07_10_21_Lecture%20Notes%20on_Spectral%20Modelling_%20RKrishnan.pdf)
8. [中央氣象局第二代全球波譜模式之介紹 | 1993](https://photino.cwa.gov.tw/rdcweb/lib/cd/cd01conf/dissertation/1993-1/58.pdf)
9. [截谱模式 | 《中国⼤百科全书》第三版⽹络版 | 2022](https://www.zgbk.com/ecph/words?SiteID=1&ID=48405&SubID=76732)