---
layout: post
title: MPAS | MPAS installation - configure - RUNNING CONFIGURE FOR ch4:ucx
categories: [MPAS]
tags: [MPAS, Installation]
author: wpsze
index_img: 
banner_img: 
---

# Issue

```
configure: RUNNING CONFIGURE FOR ch4:ucx
configure: CH4 UCX Netmod:  Using an external ucx
checking if UCX meets minimum version requirement... no
configure: error: UCX installation does not meet minimum version requirement (v1.9.0). Please upgrade your installation, or use --with-ucx=embedded.
```

# Solution

Configure will use an embedded copy of libfabric or ucx if one is
not found in the user environment. An installation can be specified
by adding

>    --with-libfabric=<path/to/install> or --with-ucx=<path/to/install>

to the configuration.

The previous MPICH default device (ch3) is also available and
supported with option:

>    --with-device=ch3