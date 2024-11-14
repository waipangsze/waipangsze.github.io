---
layout: post
title: WRF | unrecognized command line option ‘-f90=gfortran’ & ungrib.exe compile error
categories: [WRF]
tags: [WPS, Installation]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/RwpxULg.png
banner_img: https://i.imgur.com/RwpxULg.png
---

# Issue

When compiling WPSv4.0/v4.2/v3.8.1

> gfortran: error: unrecognized command line option ‘-f90=gfortran’

# Solution

```
# Settings for    Linux x86_64 ppc64le, gfortran compiler with gcc   (dmpar)
#
DESCRIPTION     =       GNU ($SFC/$SCC)
DMPARALLEL      =        1
OMPCPP          =       # -D_OPENMP
OMP             =       # -fopenmp
OMPCC           =       # -fopenmp
SFC             =       gfortran
SCC             =       gcc
CCOMP           =       gcc
DM_FC           =       mpif90 -f90=gfortran
DM_CC           =       mpicc -cc=$(SCC)
FC              =       $(DM_FC)
CC              =       $(DM_CC) -DFSEEKO64_OK 
LD              =       $(FC)
```

modify DM_FC to

> DM_FC           =       mpif90

without -f90-gfortran

Finally,

1. geogrid.exe and metgrid.exe
2. link_grib.csh

are generated.

But, **no ungrib.exe !!!**

## Error

> Error: Arguments of ‘iand’ have different kind type parameters at (1)

References

1. [(RESOLVED) ungrib.exe compile error: Arguments of ‘iand’ have different kind type parameters at (1)ted](https://forum.mmm.ucar.edu/threads/resolved-ungrib-exe-compile-error-arguments-of-%E2%80%98iand%E2%80%99-have-different-kind-type-parameters-at-1-ted.9409/)
    - To dd those two extra lines to ungrib/src/ngl/g2/intmath.f (based on <https://github.com/wrf-model/WPS/pull/119>)
2. [(RESOLVED) Not generating ungrib.exe](https://forum.mmm.ucar.edu/threads/resolved-not-generating-ungrib-exe.9367/)

### Solution

[Fix mismatch integer kind in IAND intrinsic calls in g2 librar](https://github.com/wrf-model/WPS/pull/119/files/74eb62b1bef320b453151e1eb749dae96616da97#diff-93d876794073a0c3c81a225075d33578e88a2d946b833ba92f0bbcd3d6be9c03)

ungrib/src/ngl/g2/intmath.f

```
      ilog2_2=0
      i=i_in
      if(i<=0) return
      if(iand(i,i-1)/=0) then
         !write(0,*) 'iand i-1'
         ilog2_2=1
```
to 
```
      ilog2_2=0
      i=i_in
      if(i<=0) return
! WPS modification for the XL compiler
!      if(iand(i,i-1)/=0) then
      if(iand(i,i-1_2)/=0) then
         !write(0,*) 'iand i-1'
         ilog2_2=1
```
And,
```
      ilog2_1=0
      i=i_in
      if(i<=0) return
      if(iand(i,i-1)/=0) then
         !write(0,*) 'iand i-1'
         ilog2_1=1
```
to 
```
      ilog2_1=0
      i=i_in
      if(i<=0) return
! WPS modification for the XL compiler
!      if(iand(i,i-1)/=0) then
      if(iand(i,i-1_1)/=0) then
         !write(0,*) 'iand i-1'
         ilog2_1=1
```

**Works!!!**