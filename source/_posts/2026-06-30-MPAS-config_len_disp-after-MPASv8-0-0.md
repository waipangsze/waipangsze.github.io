---
layout: post
title: MPAS | CRITICAL ERROR NaN detected in 'w' field" crash after MPASv8.0.0 | config_len_disp
categories: [MPAS]
tags: [MPAS, NWP, WRF, NaN, config_len_disp]
author: wpsze
date: 2026-06-30 06:27:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/SLlQ3bs.png
banner_img: https://i.imgur.com/SLlQ3bs.png
---

# `config_len_disp`

Regarding the '`config_len_disp`' option, with `MPAS v8.0` it's no longer necessary to specify this length scale **if you're using new meshes from the mesh download page**, since these new mesh NetCDF files have a variable '`nominalMinDc`' that provides this length scale. If you're curious, you can see the corresponding code changes to read and use the 'nominalMinDc' value in [MPAS-Dev/MPAS-Model PR 1079](https://github.com/MPAS-Dev/MPAS-Model/pull/1079). 

- However, if you're **using old meshes (downloaded before the v8.0.0 release)**, you will still need to add config_len_disp to your namelist.atmosphere file.

# CRITICAL ERROR: NaN detected in 'w' field

- <https://forum.mmm.ucar.edu/threads/critical-error-nan-detected-in-w-field.12713/> (Jan 4, 2025)

1. I tried a 15 s timestep an it crashed after simulating one day. Attached are the atmosphere log and screenshot of error message with files written. It could not write the restart file. My guess is that `io_type="pnetcdf,cdf5"` should be **included for restart** as well in the `streams.atmosphere`!
2. Looking through the release notes for `MPAS v8.1.0`, it doesn't seem that much has changed in the way of the MPAS-A dynamical core. However, we did make changes to how we interface with the `MPI` library (through the `mpi_f08` module, if it is available). I recall that some older versions of MPICH had issues with certain routines in the `mpi_f08` module, so perhaps there's some issue there. Which MPI library are you using, and which version of that library do you have? In any case, it could be worth trying the dynamics-only (no-physics) simulation again after adding
```namelist.atmosphere
&development
    config_halo_exch_method = 'mpas_dmpar'
/
```
    1. to your `namelist.atmosphere` file. This will cause MPAS-A to use an older infrastructure module for communicating halos among MPI tasks.
    2. I have **upgraded the mpich library from version 4.0.2 to the latest version 4.2.3** and now the model works perfectly.
    3. I have also tested the model with **mpich version 4.0.2 using your suggested setting in namelist.atmosphere file and it also worked**.


{% note primary %}
**upgraded the mpich library from version 4.0.2 to the latest version 4.2.3**
{% endnote %}

{% note primary %}
**mpich version 4.0.2 using your suggested setting in namelist.atmosphere file and it also worked**

```namelist.atmosphere
&development
    config_halo_exch_method = 'mpas_dmpar'
/
```

- **Why use it?** It is a common troubleshooting workaround in MPAS-A v8.1.0 and v8.2.2 to resolve the "CRITICAL ERROR: NaN detected in 'w' field" crash.
- **Primary Solution**: This error is frequently caused by bugs in newer MPI libraries; upgrading your MPI library (e.g., MPICH to v4.2.3 or newer) is often the more permanent fix.
{% endnote %}