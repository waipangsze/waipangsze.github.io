---
layout: post
title: MPAS | Physics and Physics Configurations, Profiling computing time
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS]
author: wpsze
date: 2025-02-11 11:07:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/ABJTiRl.png
banner_img: https://i.imgur.com/ABJTiRl.png
---

# Physics and Physics Configurations

{% gi 6 3-3 %}
![](https://i.imgur.com/siMEUS9.png)
![](https://i.imgur.com/BO0rB71.png)
![](https://i.imgur.com/rc5XBgi.png)
![](https://i.imgur.com/ABJTiRl.png)
{% endgi %}

# Profiling computing time of MPAS

the timer information that is printed at the bottom of the log.atmosphere.0000.out file.

The total runtime for a simulation includes time not spent in initialization or counted as time integration; for example, time spent writing output files isn't counted as "time integration".

The "parallel efficiency" is defined as the average time for a region across all MPI tasks divided by the maximum time for that region across all tasks.

Hopefully this helps, and if you have any additional questions about timers, please feel free to follow up in this thread.

{% gi 6 3-3 %}
![](https://i.imgur.com/TJAfYme.png)
![](https://i.imgur.com/wtb6Ilv.png)
{% endgi %}

```console
  Timer information:
     Globals are computed across all threads and processors
 
  Columns:
     total time: Global max of accumulated time spent in timer
     calls: Total number of times this timer was started / stopped.
     min: Global min of time spent in a single start / stop
     max: Global max of time spent in a single start / stop
     avg: Global max of average time spent in a single start / stop
     pct_tot: Percent of the timer at level 1
     pct_par: Percent of the parent timer (one level up)
     par_eff: Parallel efficiency, global average total time / global max total time
```

# References

1. [Physics and Physics Configurations in MPAS| Wei Wang | NCAR/MMM MPAS-A and MPAS-JEDI Tutorials, 23-26 October 2023, Taiwan](http://www.gpsarc.ncu.edu.tw/MPAS2023/slide/MPAS_A/6-MPAS_physics_202310.pdf)
2. [Kim, Jae Youp, Ji-Sun Kang, and Minsu Joh. "GPU acceleration of MPAS microphysics WSM6 using OpenACC directives: Performance and verification." Computers & Geosciences 146 (2021): 104627.](https://www.sciencedirect.com/science/article/pii/S0098300420306051)
   1. [PPT](https://www.cisl.ucar.edu/sites/default/files/2021-10/KISTI%20-%20Joh%2C%20Kang%2C%20%26%20Kim.pdf)
      1. **Profiling computing time of MPAS**
3. [Capodaglio, G., & Petersen, M. (2022). Local time stepping for the shallow water equations in MPAS. Journal of Computational Physics, 449, 110818.](https://www.sciencedirect.com/science/article/am/pii/S0021999121007130)