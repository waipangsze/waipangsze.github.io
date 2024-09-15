---
layout: post
title: fatal error - rpc/types.h - No such file or directory
categories: [WRF]
tags: [WRF, Installation]
author: wpsze
---

# Issue

When compiling WRFv4.0.0,

> mpicc -cc=gcc -DFSEEKO64_OK  -o landread.o -c -w -O3 -c  -DDM_PARALLEL -DMAX_HISTORY=25 -DNMM_CORE=0 landread.c
> landread.c:68:10: fatal error: rpc/types.h: No such file or directory
> #include <rpc/types.h>
>          ^~~~~~~~~~~~~
> compilation terminated.
> make[2]: [../configure.wrf:372: landread.o] Error 1 (ignored)

# Reference

[(RESOLVED) Problem Compiling WRFV4.0 on Fedora 28: landread error](https://forum.mmm.ucar.edu/threads/resolved-problem-compiling-wrfv4-0-on-fedora-28-landread-error.61/)

# Workaround 1

We've seen a few reports of this, and it seems to be specific to particular Linux systems. We have not been able to repeat this at NCAR on our Macs, our department Linux server, or on our Linux HPC, and therefore we haven't come up with a long-term solution yet. However, a work-around would be to do the following:

Issue a ‘clean -a’ and then reconfigure. Then go into your configure.wrf file and look for the line:
```
CFLAGS = $(CFLAGS_LOCAL) -DDM_PARALLEL -DSTUBMPI \
-DMAX_HISTORY=$(MAX_HISTORY) -DNMM_CORE=$(WRF_NMM_CORE)
```

Onto the end of that, add -DLANDREAD_STUFF, so that it now looks like:
```
CFLAGS = $(CFLAGS_LOCAL) -DDM_PARALLEL -DSTUBMPI \
-DMAX_HISTORY=$(MAX_HISTORY) -DNMM_CORE=$(WRF_NMM_CORE) -DLANDREAD_STUB
```
Then save that configure file and recompile. This should get your past the problem. Please let us know if that works for you.
Thanks!

### WRFv4.0.0 Works !

## What is LANDREAD_STUB?

1. The LANDREAD_STUB option removes the ability for a moving nest to horizontally interpolate high-resolution topography and landuse fields. Very few use this option, so this is typically a safe recourse.

2. For those who do need the capability of a moving nest with the high-resolution static fields, if the location of types.h is not found in /usr/include, the alternative would be to look in /usr/include/tirpc.

We can put together a github pull request, but we will need a couple of users to verify that the proposed fix still provide code that works for us and additionally works for the those that have reported the build troubles with WRF.

3. Would you please test this pull request: <https://github.com/wrf-model/WRF/pull/1072>

# Workaround 2

1. I am encountering a similar issue when compiling V3.9.1.1 and the above solution does not seem to work for me.

2. I had to Copy landread.c.dist to landread.c in share directory for WRFV3 and that solved the issue.

3. Just cd share/ and do cp landread.c.dist landread.c
