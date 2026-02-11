---
layout: post
title: MPAS | Tangential Velocity at Edge 
categories: [MPAS]
tags: [WRF, MPAS, NWP, Tangential Velocity]
author: wpsze
date: 2026-02-11 06:56:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/VNoG2zS.png
banner_img: https://i.imgur.com/VNoG2zS.png
---

# Literature review

1. [Skamarock, W. C., Klemp, J. B., Duda, M., Fowler, L., & Park, S. H. (2010, November). **Global nonhydrostatic modeling using Voronoi meshes: the MPAS model. In Proceedings of the ECMWF Workshop on Nonhydrostatic Modelling**, European Center for Medium Range Forecasting, Reading (pp. 8-10).](https://www.ecmwf.int/sites/default/files/medialibrary/2020-08/12299-global-nonhydrostatic-modelling-using-voronoi-meshes.comp_.pdf)
   1. The determination of the horizontal velocity tangential to the cell edges lies at the heart of the MPAS formulation. Consider the determination of the velocity perpendicular to u1 in figure 2.
   2. Thuburn et al (2009) developed a **reconstruction of the tangential velocity** that recovered the stationary mode. It consists of a weighted sum of all the prognostic velocities from the edges of the two cells sharing the edge where the tangential velocity reconstruction is required.
   3. The reconstruction has the important property that the mass divergence computed on the dual (Delauney) triangulation, using the reconstructed mass fluxes (velocities) normal to the Delaunay cell edges, is the weighted sum of the mass divergences of the three Voronoi cells.
   4. We can **define the Thuburn et al (2009) tangential velocity reconstruction** (eq 6)
2. [MPAS-A_tech_note.pdf](https://www2.mmm.ucar.edu/projects/mpas/mpas_website_linked_files/MPAS-A_tech_note.pdf#page=22.47)
   1. Tangential Velocity Reconstruction
      1. ![](https://i.imgur.com/7CDniFk.png)
      2. ![](https://i.imgur.com/VBOhGtW.png)

# Horizontal Velocity Components

How MPAS handles its staggered horizontal velocity components. Because MPAS uses a **C-grid staggering** on a **non-traditional mesh (centroidal Voronoi tessellation)**, the way it handles vector components is a bit more nuanced than a standard rectangular grid.
Here is a breakdown of how those components interact and why that tangential reconstruction is so vital.

1. The Geometry of the Staggering
   1. In a traditional Arakawa C-grid on a square mesh, $u$ and $v$ are naturally orthogonal. In MPAS, the "normal" velocity $u$ is the primary prognostic variable, representing the flow directly across the cell face (the edge). 
      1. **Normal Velocity ($u$)**: Located at the edge midpoint, perpendicular to the edge.
      2. **Tangential Velocity ($v$)**: Directed along the edge. As you noted, it is defined by the orientation between `verticesOnEdge(1, iEdge)` and `verticesOnEdge(2, iEdge)`.
2. The Role of Tangential Velocity
   1. **Since $v$ is not a primary prognostic variable (we don't "evolve" it directly through its own partial differential equation in the same way**), **it must be reconstructed**. This is typically done using the RBF (Radial Basis Function) interpolation or a similar weighted linear combination of the surrounding normal velocities.
   2. This reconstruction is essential for:
      1. **Vorticity ($\zeta$)**: Relative vorticity is defined at the vertices. To calculate the circulation around a vertex, you need the tangential components along the three edges that meet at that point.
      2. **Coriolis Force**: The vector-invariant form of the momentum equations requires a full velocity vector at the edge to compute the $f \times u$ term.
      3. **Advection**: To accurately move tracers across the mesh, the model needs to understand the full flow field, not just the "through-flow" at the edges.
3. Coordinate System and Orientation
   1. A key detail in MPAS is the local coordinate system for each edge. Because the edges aren't aligned to a global X-Y axis (due to the icosahedral structure), the "positive" direction for $v$ is determined by the indexing of the vertices. If you swap the order of verticesOnEdge, the sign of your tangential velocity flips.

| Feature     | Normal Velocity (u)         | Tangential Velocity (v)              |
|-------------|-----------------------------|--------------------------------------|
| Location    | Edge Midpoint               | Along the Edge                       |
| Type        | Prognostic (Primary)        | Reconstructed (Diagnostic)           |
| Grid Role   | Mass flux between cells     | Calculation of vorticity at vertices |
| Orientation | Cell 1 $\rightarrow$ Cell 2 | Vertex 1 $\rightarrow$ Vertex 2      |


# Key Aspects of MPAS Tangential Velocity

In the Model for Prediction Across Scales (MPAS), the definition and reconstruction of tangential velocity ($v$) are fundamental to maintaining accuracy on an unstructured Voronoi mesh. While **the normal velocity ($u$)** is the primary prognostic variable located at the centers of cell edges, **the tangential velocity ($v$)** is a diagnostic quantity necessary for computing the full horizontal velocity vector.

## Key Aspects of MPAS Tangential Velocity

Based on the technical documentation for MPAS-Atmosphere, here is how the tangential velocity is structured and used:

1. **Primary Definition**: The horizontal velocity is staggered using a **C-grid** configuration. In this setup, the prognostic velocity is defined as the component normal to the Voronoi cell faces (edges). The tangential velocity is the component oriented along that same edge, flowing between the two vertices.
2. **Vector-Invariant Formulation**: MPAS utilizes a vector-invariant form of the horizontal momentum equations. In this formulation, nonlinear momentum transport is recast into terms involving the **gradient of kinetic energy** and a **nonlinear Coriolis** term. **This approach requires a reconstructed tangential velocity at the edges to accurately compute these terms without the complexities of standard advective forms on unstructured meshes.**
3. **Reconstruction for Vorticity**: Tangential velocities are essential for **calculating relative vorticity**, which is defined at the vertices of the Voronoi cells. To determine the circulation around a vertex, the model uses the tangential components of the three edges that intersect at that vertex.
4. **Numerical Accuracy and SCVT Properties**: The mesh used is a Spherical Centroidal Voronoi Tessellation (SCVT). A critical property of this mesh is that the lines connecting cell centers (Delaunay triangles) are perpendicular to the cell edges at their intersection points. This orthogonality is vital for the accuracy of the reconstruction schemes, as it ensures that the normal and tangential components are strictly orthogonal.
5. **Transport and Conservation**: While $v$ is reconstructed, the overall discretization of the flux divergence on the Voronoi mesh guarantees the exact conservation of air mass, scalar mass, and entropy. The reconstruction of the full velocity vector allows for high-order transport schemes (typically third-order accurate) to be used for scalars and potential temperature.

# Mathematical Foundation of Reconstruction

The reconstruction follows the methodology described by Thuburn et al. (2009), which **ensures that the discrete system maintains key physical properties such as stationary geostrophic modes and conservation of potential vorticity (PV), enstrophy, and energy**.

1. General Reconstruction Formula
The tangential velocity at a specific edge is computed as a weighted sum of the normal velocities from the neighboring edges of the adjacent Voronoi cells. The general form can be expressed as:


$$v_e = \sum_{e' \in \text{nb}(e)} w_{e,e'} u_{e'}$$

where:

- $v_e$ is the reconstructed tangential velocity at edge $e$.
- $u_{e'}$ represents the prognostic normal velocities at neighboring edges $e'$.
- $w_{e,e'}$ are pre-computed weights based on the mesh geometry.


2. Connection to Absolute Vorticity

The tangential velocity is directly tied to the **calculation of absolute vorticity** ($\eta$), which lives at the vertices of the Voronoi cells. For a vertex $a$ shared by three cells (forming a Delaunay triangle $ABC$), the absolute vorticity is defined by the circulation around that triangle:


$$\eta_a = f_a + \frac{1}{\text{Area}(ABC)} \left( u_{13} \cdot \overline{CA} + u_{14} \cdot \overline{AB} + u_{15} \cdot \overline{BC} \right)$$

(Note: $u_{13}, u_{14}, u_{15}$ are normal velocities on the edges of the triangle, and $\overline{CA}, \overline{AB}, \overline{BC}$ are the lengths of the corresponding triangle edges).

The absolute vorticity at an edge center ($e_{13}$) is then reconstructed as the average of the vorticity at its two endpoints (vertices $a$ and $b$):

$$\eta_{13} = \frac{1}{2} (\eta_a + \eta_b)$$

## Why this specific math is used

- **Geostrophic Stationarity**: Simple weighted sums of the four nearest neighbors fail because they do not allow geostrophically balanced flow to remain stationary in a discrete system. The Thuburn et al. (2009) approach solves this "nonstationary geostrophic mode" problem.
- **Consistency**: This method produces a consistent divergence on both the primal (Voronoi) and dual (Delaunay) grids. In the discrete analogue, the divergence on the Delaunay triangle is identical to the divergence on the Voronoi hexagons integrated over that triangle.
- **Radial Basis Functions (RBF)**: For certain operations, such as computing horizontal momentum components $(U, V)$ at cell centers for the vertical momentum equation, MPAS may use an RBF reconstruction instead of the simple weighted sum.

# References

1. [04.mesh_structure.pdf](https://www2.mmm.ucar.edu/projects/mpas/tutorial/Boulder2019/slides/04.mesh_structure.pdf)