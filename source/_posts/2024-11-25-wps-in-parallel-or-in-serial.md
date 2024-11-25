---
layout: post
title: "WRF | WPS in parallel or in serial"
categories: [WRF]
tags: [WRF,WPS,MPAS,IC/BC]
author: wpsze
date: 2024-11-25 14:38:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/RwpxULg.png
banner_img: https://i.imgur.com/RwpxULg.png
---

- **keywords: metgrid.exe, geogrid.exe, dmpar, serial**

# [WRF/WPS Compilation Tutorial](https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compilation_tutorial.php)

You should be given a list of various options for compiler types, whether to compile in serial or parallel, and whether to compile ungrib with GRIB2 capability. Unless you plan to create extremely large domains, 

{% note danger %}
**It is recommended to compile WPS in serial mode, regardless of whether you compiled WRF in parallel.**
{% endnote %}

# [(RESOLVED) serial vs. dmpar | Jan 15, 2019](https://forum.mmm.ucar.edu/threads/resolved-serial-vs-dmpar.583/)

That is a good question. **We have just found that the advantage to running WPS in parallel isn't that beneficial (or much faster) than a simple serial run**, as the WPS programs run so quickly anyway. We do find that it is necessary if you have very large domains (1000's x 1000's of grid cells). That being said, if you are finding that it speeds up the process for your runs, then it should be perfectly fine to use parallel processing. As I'm sure you already know, geogrid and metgrid are the only programs that can be run in parallel. Ungrib must still be run serially.

# Using MPAS Output for WRF Initial and Lateral Boundary Conditions 

{% note warning %}
[Using MPAS Output for WRF Initial and Lateral Boundary Conditions](https://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.2/users_guide_chap3.html#_Using_MPAS_Output)
{% endnote %}

- <https://waipangsze.github.io/2024/11/13/WRF-MPAS-as-ICBC/>

{% note warning %}
**It is worth noting that the use of native MPAS output with metgrid.exe has not been thoroughly tested for parallel (i.e., “dmpar”) builds of the WPS; it is therefore recommended to run metgrid.exe in serial when processing MPAS datasets.**
{% endnote %}

**Please use 1 core (serial) to run metgrid.exe.** Otherwise, the wrfout will be fail even (dmpar) of metgrid seems to be done.

{% note danger %}
**sbatch -N 1 -n 1 run_metgrid.sh**
{% endnote %}