---
layout: post
title: NWP | ECMWF
categories: [NWP]
tags: [NWP, MPAS, WRF, ML, AI]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://www.ecmwf.int/sites/default/files/flags-council-chamber-650px.jpg
banner_img: https://www.ecmwf.int/sites/default/files/flags-council-chamber-650px.jpg
---

The organisation was **established in 1975** and now employs around 500 staff from more than 30 countries. ECMWF is one of the six members of the Co-ordinated Organisations, which also include the North Atlantic Treaty Organisation (NATO), the Council of Europe (CoE), the European Space Agency (ESA), the Organisation for Economic Co-operation and Development (OECD), and the European Organisation for the Exploitation of Meteorological Satellites (EUMETSAT).

**ECMWF is headquartered in Reading, UK, with additional sites in Bologna, Italy, and Bonn, Germany**.

The European Centre for Medium-Range Weather Forecasts (ECMWF) is an independent intergovernmental organisation supported by most of the nations of Europe. It is based at three sites: Shinfield Park, Reading, United Kingdom; Bologna, Italy; and Bonn, Germany. It operates one of the largest supercomputer complexes in Europe and the world's largest archive of numerical weather prediction data.

# Major changes introduced with Cy48r1 Spring 2023

[Some major changes were made to the IFS with the introduction of the Cy48r1 in Spring 2023.](https://confluence.ecmwf.int/display/FUG/Section+2.1.2+Model+Configurations)

After the introduction of  Cy48r1 in Spring 2023 the:

**High Resolution (HRES)**:

- is run four times daily:
  - base times 00 UTC and 12 UTC, producing a 10 day forecast (T+0 to T+240).
  - base times 06UTC and 18UTC, producing a 3.75 day forecast (T+0 to T+90).
- has **horizontal resolution of 9 km** and vertical resolution of **137** model levels.

**Medium range ensemble (ENS)**:

- is run four times daily:
  - base times 00 UTC and 12 UTC, producing a 10 day forecast (T+0 to T+240) and also a 15 day forecast (T+0 to T+360).
  - base times 06UTC and 18UTC, producing a 6 day forecast (T+0 to T+144). Results from these runs are:
- used in Early cut off analysis (SCDA) and Long window data analysis (LWDA).
- disseminated T+0 to T+72.
- has **horizontal resolution of 9 km** and vertical resolution of **137**  model levels.  These values are the same as those of the High Resolution (HRES).
- has **50 ensemble members** plus an unperturbed control member (CTRL).
- the unperturbed control member (CTRL) corresponds to the High Resolution (HRES) in earlier IFS versions.  CTRL forecasts maintain continuity of output for users.

**Extended range ensemble**:

- is run once daily, base time 00 UTC, producing a 46 day forecast (Day0 to Day46).
  - has **horizontal resolution of 36 km** and vertical resolution of **137** model levels.  
  - has **100 members** plus an unperturbed control member.

# ECMWF IFS 9km computing resource requirements (2024)

- <https://mp.weixin.qq.com/s/Qu8JSdAMSJywmlCJM6QDzw>
  - 根据ECMWF官网“How to optimise the computing aspects of numerical weather forecasts”文章, IFS 9km模式预报15天需要大约50个计算节点
  - 推算IFS预报一天能够在3.6分钟墙钟时间内完成。
  - IFS运行计算机应该是博洛尼亚计算中心机器，每个计算节点为128核心，总计算资源数大概在6784核左右。