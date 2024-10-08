---
layout: post
title: WRF | Error - Size of put argument of random_seed intrinsic at (1) too small
categories: [WRF]
tags: [WRF, Installation]
author: wpsze
index_img: 
banner_img: 
---

# Issue

While compiling WRFv3.8.1,

```
2065 
2066  3128 |                    call random_seed (PUT=seed)
2067       |                                         1
2068 Error: Size of ‘put’ argument of ‘random_seed’ intrinsic at (1) too small (12/33)
2069 module_cu_g3.f90:3217:41:
2070 
2071  3217 |                    call random_seed (PUT=seed)
2072       |                                         1
2073 Error: Size of ‘put’ argument of ‘random_seed’ intrinsic at (1) too small (12/33)
2074 module_cu_g3.f90:3232:41:
2075 
2076  3232 |                    call random_seed (PUT=seed)
2077       |                                         1
2078 Error: Size of ‘put’ argument of ‘random_seed’ intrinsic at (1) too small (12/33)
2079 make[3]: [../configure.wrf:339: module_cu_g3.o] Error 1 (ignored)
2080 rm -f module_cu_kfeta.o
```

# Solution

Ref:
- [WRFV3.9 compilation error on Ubuntu 18.04 LTS with gfortran-7.4](https://forum.mmm.ucar.edu/threads/wrfv3-9-compilation-error-on-ubuntu-18-04-lts-with-gfortran-7-4.8786/)
- [WRFV3.8 problem building executables](https://forum.mmm.ucar.edu/threads/wrfv3-8-problem-building-executables.8418/)
- [WRFv381 with GNU8.x](https://forum.mmm.ucar.edu/threads/wrfv381-with-gnu8-x.217/)

>This code has been corrected beginning with V4.0. I'm attaching the modified file so that you can see how we changed it. Unfortunately we didn't have a git repo when we released V3.8.1, but we can add the problems to our known problems page.

The file is
> WRF/phys/module_cu_g3.F

on <https://github.com/wrf-model/WRF/blob/v4.0/phys/module_cu_g3.F>

```
$ md5sum module_cu_g3.F
e385527b41d374d6530596e069fb547b  module_cu_g3.F
```

# But


> Fatal Error: Can't open module file 'module_initialize_real.mod' for reading at (1): No such file or directory

## Solution

Then it should end successfully when running the command a second time (do not understand why but it works...)

1. $ ./compile -j 8 em_real 2>&1 | tee compile.log
2. Fatal Error: Can't open module file 'module_initialize_real.mod' ...
3. $ ./compile -j 8 em_real 2>&1 | tee compile.log
4. Then, it works !!

ref: <https://valcap74.blogspot.com/2017/10/how-to-compile-wrf-model-on-ecmwf-cray.html>
