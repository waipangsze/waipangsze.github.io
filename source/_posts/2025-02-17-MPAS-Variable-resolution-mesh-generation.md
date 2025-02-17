---
layout: post
title: MPAS | Variable-resolution mesh generation
categories: [MPAS]
tags: [MPAS, NWP, WRF, mesh generation, Lloyd iteration]
author: wpsze
date: 2025-02-17 17:12:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/zs0hiJg.png
banner_img: https://i.imgur.com/zs0hiJg.png
---

# MPAS variable-resolution mesh generation

- [MPAS-Atmosphere Mesh Generation | Michael G. Duda | NCAR/MMM | MPAS-A and MPAS-JEDI Tutorials, 23-26 October 2023, Taiwan](http://www.gpsarc.ncu.edu.tw/MPAS2023/slide/MPAS_A/14-Mesh%20generation.pdf)
  - we employ Lloyd’s Method (Originally developed by Stuart Lloyd at Bell Labs for optimal signal quantization)
  - Density function: the key to refinement
  - generating a mesh is still very slow
  - Can take several months on a single multi-core desktop for high-resolution meshes
  - Even with 24 threads on the desktop in my office, generating a 15-3 km mesh with ~6.5 M cells still took months!
- [MPAS variable-resolution mesh generation | Feb 2023](https://forum.mmm.ucar.edu/threads/mpas-variable-resolution-mesh-generation.12535/)
  - we don't yet support (or even make available) the software that we use internally to generate MPAS-A meshes
  - Also, note that the lead time to generate this mesh will likely be several months
  - The generation of an SCVT can be viewed as an optimization problem, and the method that we use to perform this optimization -- Lloyd's method -- requires a very large number of iterations (typically O(10^6)), and each iteration can take several seconds. Part of each iteration can be parallelized, but there is still a part that is done in serial with our current software
- [20-km benchmark case | March 2022](https://forum.mmm.ucar.edu/threads/20-km-benchmark-case.11528/#post-25917)
  - At present we don't have a 20-km benchmark case, and in order to generate this yourself you would need a 20-km mesh. While we don't actually have a 20-km quasi-uniform mesh at present, we could generate one and make it available on the mesh download page in the near future. I'll follow up in this thread once the 20-km mesh is available.
  - We don't currently provide software for generating new meshes, so generating a 20-km mesh is something that we will do for you.
  - We haven't made our mesh generation software publicly available, so once I have generated the 20-km mesh, I'll follow up here to let you know that the mesh is available.

{% note primary %}
The generation of an SCVT can be viewed as an optimization problem, and the method that we use to perform this optimization -- Lloyd's method -- requires a very large number of iterations (typically O(10^6)), and each iteration can take several seconds. Part of each iteration can be parallelized, but there is still a part that is done in serial with our current software
{% endnote %}