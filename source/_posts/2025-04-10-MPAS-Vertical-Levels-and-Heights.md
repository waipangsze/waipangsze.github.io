---
layout: post
title: MPAS | Vertical Levels and Heights
categories: [WRF]
tags: [WRF, MPAS, HPC, NWP, UCM]
author: wpsze
date: 2025-04-10 14:19:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/ueKU0IE.png
banner_img: https://i.imgur.com/ueKU0IE.png
---

[WRF | Vertical Levels and Heights](https://waipangsze.github.io/2025/01/27/WRF-Vertical-Levels-and-Heights/)

---

# MPAS's user guide

A description of the compressible nonhydrostatic atmospheric solver can be found in Skamarock et al, 2012. The fully compressible nonhydrostatic equations are cast in terms of a **geometric-height vertical coordinate**, and the solver makes use of a split-explicit time integration scheme that is described in Klemp et al, 2007. The time-integration scheme employs a 3rd-order Runge-Kutta method, and large time step, for the meteorologically significant modes and a forward-backward method with smaller time steps for the acoustic modes (See Wicker and Skamarock, 2002). 

## $\mathbf{C. 2}$ Vertical grid

The vertical coordinate in MPAS-Atmosphere is $\zeta$ and has units of length, where $0\leq\zeta\leq z_{t}$ and $z_{t}$ is the height of the model top. The relationship between the vertical coordinate and height in the physical domain is given as

$$z=\zeta+Ah_{s}(x,y,\zeta)$$

where $(x,y)$ denotes a location on the horizontal mesh and $\zeta$ is the vertical coordinate $(\zeta$ is directed radially outward from the surface of the sphere, or perpendicular to the horizontal $(x,y)$ plane in a Cartesian coordinate MPAS-A configuration). MPAS-A can be configured with the traditional Gal-Chen and Somerville terrain-following coordinate by setting $h_s(x,y,\zeta)=h(x,y)$ and $A=1-\zeta/z_t$, where $h(x,y)$ is the terrain height. Alternatively, A can be modified to allow a more rapid or less rapid transition to the constant-height upper boundary condition. Additionally, a constant-height coordinate can be specified at some intermediate ${\mathrm{height~below~}}h_{t}.$

The influence of the terrain on any coordinate surface $\zeta$ can be influenced by the specification of$h_s( x, y, \zeta ) .$ Specifically$, h_s$ can be set such that $h_s( x, y, 0) = h( x, y)$ ( ie. terrain following at the sur- face), and progressively flltered fields o features in the topography are quickly filtered from the coordinate. Example MPAS-A vertical meshes are given in Figure C.4. 

Further information about the vertical coordinate can be found in
Klemp, J. B. (2011). A Terrain-Following Coordinate with Smoothed Coordinate Surfaces. Mon.Wea Rev. , 139, 2163$-2169. doi:10.1175/MWR-D-10-05046.1

{% gi 4 2-2 %}
![](https://i.imgur.com/ueKU0IE.png)
![](https://i.imgur.com/SVeBD3M.png)
![](https://i.imgur.com/Wowl0HL.png)
{% endgi %}