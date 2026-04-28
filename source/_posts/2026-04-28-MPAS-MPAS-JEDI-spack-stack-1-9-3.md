---
layout: post
title: MPAS | MPAS-JEDI | spack-stack 1.9.3
categories: [Spack]
tags: [MPAS, DA, MPAS-JEDI, Spack, Spack-Stack]
author: wpsze
date: 2026-04-28 06:25:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/6oGdLJk.png
banner_img: https://i.imgur.com/6oGdLJk.png
---

- [MPAS | MPAS-JEDI](https://waipangsze.github.io/2024/05/29/MPAS-JEDI_note/)
- [MPAS | Joint MPAS/WRF Users Workshop 2025 | MPASv8.3.0](https://waipangsze.github.io/2025/06/05/MPAS-Joint-MPAS-WRF-Users-Workshop-2025/)
- [**Spack-Stack: building JEDIbundles on your own machine | 202506NCAS** ](https://www2.mmm.ucar.edu/projects/mpas-jedi/tutorial/202506NCAS/lectures/12-spackstack.pdf)

---

- <https://spack-stack.readthedocs.io/en/latest/>
  - The spack-stack documentation moved from readthedocs to the JCSDA spack-stack Wiki (https://github.com/jcsda/spack-stack/wiki) with release 2.0.0.
  - The latest documentation here is for spack-stack-1.9.3.

---


# Pre-configured sites

- <https://spack-stack.readthedocs.io/en/latest/PreConfiguredSites.html>

# Creating a new environment

- <https://spack-stack.readthedocs.io/en/latest/NewSiteConfigs.html#linux>

It is recommended to increase the stacksize limit by using **`ulimit -S -s unlimited`**,

```sh
git clone [-b develop OR release/branch-name] --recurse-submodules https://github.com/jcsda/spack-stack.git
cd spack-stack

# Sources Spack from submodule and sets ${SPACK_STACK_DIR}
source setup.sh
```

```sh
spack stack create env --site linux.default [--template skylab-dev] --name skylab-dev.mylinux --compiler=gcc
cd envs/skylab-dev.mylinux/
spack env activate [-p] .
```

# jedi-mpas-env

The `jedi-mpas-env` refers to **setting up the software environment required to run JEDI-MPAS**, a Joint Effort for Data assimilation Integration system for the Model for Prediction Across Scales (MPAS). It enables 3D/4DEnVar and 3DVar data assimilation using containers or HPC module files (e.g., NCAR/MMM, JCSDA).

- <https://www2.mmm.ucar.edu/projects/mpas-jedi/tutorial/202506NCAS/lectures/12-spackstack.pdf>
  - P.6
- `module load jedi-mpas-env`

![](https://i.imgur.com/K8MI4WN.png)

```console
 $ spack info jedi-mpas-env
BundlePackage:   jedi-mpas-env

Description:
    Development environment for mpas-bundle

Homepage: https://github.com/JCSDA/mpas-bundle

Preferred version:  
    1.0.0    

Safe versions:  
    1.0.0    

Deprecated versions:  
    None

Variants:
    build_system [bundle]        bundle
        Build systems supported by the package

Build Dependencies:
    None

Link Dependencies:
    None

Run Dependencies:
    jasper  jedi-base-env  metis  parallel-netcdf  parallelio

Licenses: 
    None
```