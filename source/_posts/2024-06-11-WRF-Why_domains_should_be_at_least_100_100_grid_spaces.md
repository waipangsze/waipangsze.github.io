---
layout: post
title: "WRF | WRF: Why domains should be at least 100x100 grid spaces?"
categories: [WRF]
tags: [WRF]
author: wpsze
index_img: 
banner_img: 
---

# [Why domains should be at least 100x100 grid spaces?](https://forum.mmm.ucar.edu/threads/why-domains-should-be-at-least-100x100-grid-spaces.16793/)

Each WRF domain should be at least 100x100 grid points (e_we and e_sn).

When running a simulation, the input data for the lateral boundaries, every so many intervals (e.g., every 3 hours), are much more coarse than the simulation you’re trying to run.

Synoptic-scale systems typically move through a large area in a short amount of time. If a domains is too small, there isn’t enough space for the model to develop anything of interest before the large-scale parcels have propagated through the domain and exited the other side. This is sometimes referred to as “sweeping,” meaning the **input essentially wipes out what WRF is trying to simulate at high resolution, with different physics and higher-res terrain, landuse, the dynamics, and all the other features you want**. Even if you’re only interested in microscale events, the large-scale factors are important for forcing.

Essentially, if your domain is too small, you’re basically going to get the output as if the domain was still a coarse-resolution. You want your domain to have enough space so that you can actually see the effect.

This is not a computational limitation. It’s more an issue of reasonable and beneficial output.
{:.info}

**Also note that the model will use the rows and columns around the edges of the domain for communication. This is set, by default, to 5 rows and 5 columns (it can be changed with the &bdy_control namelist parameter ‘spec_bdy_width,' but shouldn't be smaller than 5). This means that if e_we = 100 and e_sn = 100, at a minimum, the domain is already cut down to 90x90.**
