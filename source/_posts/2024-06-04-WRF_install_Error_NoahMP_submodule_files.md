---
layout: post
title: WRF | Error Error Error NoahMP submodule files not populating WRF directories
categories: [WRF]
tags: [Installation]
author: wpsze
mathjax: true
mathjax_autoNumber: true
index_img: 
banner_img: 
---

# Issue

While installing latest WRF, encounter

> Error Error Error NoahMP submodule files not populating WRF directories

## New in Version 4.4

Physics

The NoahMP code has been moved to an external repository and is linked to WRF repository via a submodule. This submodule is automatically pulled in to build the code during compiling, as long as the compilation is done with internet access. If compiling without internet access, or you are not sure if the computer you are using to compile supports git (git is used during the compiling process to access code in submodule), or in the rare case that one checks out another branch, other than ‘master,’ it may be necessary to issue the following commands while connected to the internet to ensure the submodule is available for compiling.

- git clone https://github.com/wrf-model/WRF.git
- git submodule update --init --recursive

Or in one command:
- git clone --recurse-submodule https://github.com/wrf-model/WRF.git

After this, it is okay to go offline to compile. In the case you would like to remove code in the submodule, use ‘clean -aa’ instead of ‘clean -a’. [Details](https://github.com/wrf-model/WRF/commit/a49fe581285d)

If downloading the files from the section below, please choose either the v4.4.tar.gz file, or the v4.4.zip file. DO NOT choose those named "Source Code." They do not include the mandatory NoahMP submodule - needed for compiling WRF.

# Solution

1. Go to specific WRF version and /phys/
2. Download **noahmp @ xxxx** folder to your local /WRF/phys/
    - WRFv4.5.2 is noahmp @ 8a8073e
    - WRFv4.6.0 is noahmp @ 848f54a

It is

```
WRFV4.5/phys/noahmp  
 $ ls
drivers  parameters  README.md  src

.
├── drivers
│   └── wrf
│       └── module_sf_noahmpdrv.F
├── parameters
│   ├── GENPARM.TBL
│   ├── MPTABLE.TBL
│   └── SOILPARM.TBL
├── README.md
└── src
    ├── module_sf_noahmp_glacier.F
    ├── module_sf_noahmp_groundwater.F
    └── module_sf_noahmplsm.F

4 directories, 8 files
```
