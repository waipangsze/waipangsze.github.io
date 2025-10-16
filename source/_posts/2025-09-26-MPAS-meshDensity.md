---
layout: post
title: MPAS | meshDensity
categories: [MPAS]
tags: [MPAS, NWP, meshDensity, dx_p, gray zone]
author: wpsze
date: 2025-09-26 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/pxRygfS.png
banner_img: https://i.imgur.com/pxRygfS.png
---

`dx_p`, `len_disp`, and `meshDensity` in NWP and specifically in MPAS-A, and their relationships:

1. Definitions:

- `dx_p`: dx_p generally refers to the horizontal grid spacing or the local mesh resolution. It represents the characteristic length scale of mesh cells and determines the spatial resolution of the model in that region.
- `len_disp` (length displacement): This term is less commonly defined outright in public documents but can refer to the length scale related to displacement for numerical stability or the horizontal length scale used in determining timestep or diffusion length scales. In MPAS forums, it appears in configuration but without a clear official definition.
- `meshDensity`: In MPAS, meshDensity is a variable defining the relative density of the mesh cells. It is related to the cell width (horizontal resolution) by the formula:

$$
\text{meshDensity} = \left(\frac{\text{minCellWidth}}{\text{cellWidth}}\right)^4
$$

where minCellWidth is the smallest cell width in the mesh, and cellWidth is the local cell width. This formula means that meshDensity strongly weights smaller cells as having higher density.

2. Relationship:

- `dx_p` corresponds to the local cell width or spatial resolution of the mesh cells.
- `meshDensity` is computed based on cellWidth using the above power law formula, emphasizing regions of fine resolution.
- len_disp appears related to configuration parameters involving timestep or diffusion length scales, likely tied to `dx_p` but specific details in MPAS-A usage are limited or indirectly referenced.

3. Context in MPAS-A:

- MPAS uses centroidal Voronoi tessellation meshes with variable resolution.
- `meshDensity` is used to manage mesh refinement and define areas of high spatial resolution.
- `dx_p` (cellWidth) is the fundamental measure for resolution at each cell.
- `len_disp` could be related to numerical stability parameters or specific physics/dynamics parameter tunings involving characteristic length scales, but explicit relationship documentation is not readily found.

In summary, `dx_p` represents local mesh spacing, `dx_p` is a function of `meshDensity`, and `len_disp` is likely a length scale parameter related to `dx_p` but specific definitive documentation is sparse.


