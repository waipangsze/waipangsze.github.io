---
layout: post
title: MPAS | Gradient, Curl, Divergence, Laplacian on Unstructured Grids
categories: [MPAS]
tags: [MPAS, NWP, WRF]
author: wpsze
date: 2026-10-27 06:27:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/8pWaglh.png
banner_img: https://i.imgur.com/8pWaglh.png
---

- [Scalable Vector Calculus for Geoscientific Analysis on Unstructured Grids | Uxarray | 2025](https://www.cisl.ucar.edu/sites/default/files/2025-08/Gallmeier_Esther_Slides.pdf#1#1#1)
- [A Description of the Model for Prediction Across Scales, Atmosphere, Version 8](https://www2.mmm.ucar.edu/projects/mpas/mpas_website_linked_files/MPAS-A_tech_note.pdf)
  - 4.2 Transport for Cell-Centered Variables

---

# Horizontal Flux Divergences on the Voronoi Mesh

- Skamarock, William C., and Almut Gassmann. "Conservative transport schemes for spherical geodesic grids: High-order flux operators for ODE-based time integration." Monthly Weather Review 139.9 (2011): 2962-2975.
- Wicker, Louis J., and William C. Skamarock. "Time-splitting methods for elastic models using forward time schemes." Monthly weather review 130.8 (2002): 2088-2097.
- Hundsdorfer, Willem, Barry Koren, and J. G. Verwer. "A positive finite-difference advection scheme." Journal of computational physics 117.1 (1995): 35-46.

---

From deepseek

---

## the discrete horizontal divergence of mass flux

In MPAS-A, the discrete horizontal divergence of mass flux is calculated using a **finite-volume method**. The core idea is to apply the divergence theorem: the divergence within a cell equals the net mass flux across all its edges divided by the cell area.

### 📐 Step-by-Step Derivation

**Step 1: Start from the Continuous Conservation Law**

The dry-air mass conservation equation in flux form is:
$$
\frac{\partial \tilde{\rho}_d}{\partial t} = -\nabla \cdot \mathbf{V}_H
$$
where $\tilde{\rho}_d$ is the dry-air density (scaled by the vertical coordinate), and $\mathbf{V}_H$ is the horizontal mass flux vector (prognostic variable, located at cell edges).

**Step 2: Integrate Over a Voronoi Cell and Apply the Divergence Theorem**

Integrate over the volume of cell $i$ at vertical level $k$:
$$
\int_{V_{i,k}} \frac{\partial \tilde{\rho}_d}{\partial t} \, dV = -\int_{V_{i,k}} \nabla \cdot \mathbf{V}_H \, dV
$$
Applying the divergence theorem converts the volume integral on the right-hand side into a surface integral over the cell faces:
$$
-\int_{V_{i,k}} \nabla \cdot \mathbf{V}_H \, dV = -\sum_{n_{e_i}} \int_{A_{e_i}} \mathbf{V}_H \cdot \mathbf{n} \, dA - \int_{A_{top}} \Omega \, dA + \int_{A_{bot}} \Omega \, dA
$$
where $n_{e_i}$ denotes all lateral edges of the cell, $\mathbf{n}$ is the outward unit normal vector of the edge, and the vertical flux is represented by the $\Omega$ term.

**Step 3: Discretize the Horizontal Flux Integral**
MPAS-A uses the **edge-normal mass flux** $\mathbf{V}_{H,e_i} \cdot \mathbf{n}$ as a prognostic variable. Assuming it is constant along each edge, the lateral flux integral simplifies to:
$$
\int_{A_{e_i}} \mathbf{V}_H \cdot \mathbf{n} \, dA \approx L_{e_i} \, (\mathbf{V}_{H,e_i} \cdot \mathbf{n})
$$
where $L_{e_i}$ is the length of edge $e_i$.

**Step 4: Obtain the Discrete Divergence Expression**
Dividing the net flux by the cell area $A_i$ yields the discrete horizontal mass flux divergence (excluding the vertical term):
$$
(\nabla \cdot \mathbf{V}_H)_i \approx \frac{1}{A_i} \sum_{n_{e_i}} L_{e_i} \, (\mathbf{V}_{H,e_i} \cdot \mathbf{n})
$$
Therefore, the tendency of density due to horizontal mass flux divergence is:
$$
\left( \frac{\partial \tilde{\rho}_d}{\partial t} \right)_i^{horizontal} = -\frac{1}{A_i} \sum_{n_{e_i}} L_{e_i} \, (\mathbf{V}_{H,e_i} \cdot \mathbf{n})
$$

### 📝 Complete Form in the Code (Including Vertical Term)

In the shallow-atmosphere approximation, the horizontal mass flux divergence used in the MPAS-A source code (in `atm_compute_dyn_tend`, stored in the `rho_tend` array) has the complete form :
$$
-\frac{1}{A_i} \sum_{n_{e_i}} L_{e_i} (\mathbf{V}_{H,e_i} \cdot \mathbf{n}) + \frac{\Omega_{k+1,i} - \Omega_{k,i}}{\Delta \zeta_w(k)}
$$
where:
- **$A_i$**: Area of the Voronoi cell
- **$L_{e_i}$**: Length of edge $e_i$
- **$\mathbf{V}_{H,e_i} \cdot \mathbf{n}$**: Prognostic edge-normal mass flux
- **$\Omega$**: Vertical mass flux at layer interfaces
- **$\Delta \zeta_w$**: Cell thickness at that vertical level

This expression is used to compute the dry-air density tendency `rho_tend` and appears early in `mpas_atm_time_integration.F` .

## A scalar field

For a scalar field like water vapor mixing ratio $q_v$, MPAS-A calculates its divergence using the same finite-volume framework, but with one key difference: because $q_v$ is stored at cell centers (not edges like mass flux), the model must first **reconstruct the edge value of $q_v$** before computing the flux divergence.

### 📐 The Discrete Equation for Scalar Transport

The continuous transport equation for a scalar $\phi$ (where $\phi = q_v$) in flux form is:
$$
\frac{\partial(\tilde{\rho}_d \phi)}{\partial t} = -\nabla \cdot (\mathbf{V} \phi)
$$

Applying the finite-volume method and divergence theorem over cell $i$ yields the discrete update :
$$
\frac{\partial(\tilde{\rho}_d \phi)_i}{\partial t} = -\frac{1}{A_i} \sum_{n_{e_i}} \left[ L_{e_i} (\mathbf{V}_{\mathbf{H},e_i} \cdot \mathbf{n}) \, \phi_{e_i} \right]
$$

Here, the sum is over the $n_{e_i}$ edges of cell $i$, and the key term is **$\phi_{e_i}$**, the scalar mixing ratio interpolated to the edge.

### 🔍 How MPAS-A Handles the Edge Scalar Value

Unlike the dry-air density (which is simply averaged to the edge), the scalar $q_v$ often has sharp gradients and requires a **higher-order projection** to maintain accuracy and avoid spurious oscillations .

MPAS-A uses a **3rd or 4th-order flux reconstruction** adapted from the WRF model. On a structured mesh, the flux at an edge is constructed using neighboring cell values and their second derivatives. On the unstructured Voronoi mesh, MPAS-A uses a **least-squares-fit polynomial** to estimate the second derivatives needed for this high-order flux .

The general form of the reconstructed edge value (for a positive-definite flux) is conceptually:
$$
\phi_{e_i} = \text{high-order interpolation of } \phi \text{ from cells sharing the edge}
$$

Then, the flux is assembled as:
$$
F_{e_i} = L_{e_i} (\mathbf{V}_{\mathbf{H},e_i} \cdot \mathbf{n}) \, \phi_{e_i}
$$

### ⚠️ Important: Conservation and Limiting

A critical point is that the **same edge flux $F_{e_i}$** is used to update **both cells** that share edge $e_i$. This guarantees exact conservation of scalar mass across the mesh .

Additionally, because high-order reconstructions can produce negative values (unphysical for $q_v$), MPAS-A applies a **positive-definite or monotonic limiter**. The high-order correction flux is renormalized before the final update to ensure $q_v \ge 0$ .

### 💎 Summary

For water vapor $q_v$, the divergence is not computed as a simple centered difference. Instead, MPAS-A:
1.  Reconstructs a high-order edge value $\phi_{e_i}$ from surrounding cell-centered $q_v$.
2.  Multiplies it by the prognostic edge mass flux.
3.  Sums the resulting scalar fluxes over the cell edges and divides by the cell area.
4.  Applies a limiter to ensure the updated $q_v$ remains physically bounded.

## The gradient of a scalar field

In MPAS-A, the gradient of a scalar field is not calculated using a simple centered difference between adjacent cell centers. Instead, because of the unstructured Voronoi mesh and the C-grid staggering, the gradient operator is typically defined **at the cell edges** (where the normal velocity resides) and is computed using a finite-volume / line-integral approach.

### 📐 The Discrete Gradient Operator on Edges

The fundamental definition for the gradient at an edge in MPAS-A is derived from the need to compute the pressure gradient force and the gradient of kinetic energy in the momentum equation. For a scalar field $h$ (such as pressure or kinetic energy), the edge-normal gradient is given by:

$$
(\nabla h)_e = \frac{1}{d_e} \sum_{i \in CE(e)} -n_{e,i} \hat{h}_{e,i}
$$

Here:
*   **$d_e$**: The distance between the two cell centers that share edge $e$.
*   **$CE(e)$**: The set of cells adjacent to edge $e$ (typically two cells).
*   **$n_{e,i}$**: The sign indicator (pointing inward or outward relative to the edge).
*   **$\hat{h}_{e,i}$**: The value of the scalar interpolated to the edge (often an average of the neighboring cell-center values) .

This formulation is **second-order accurate** on the Voronoi mesh because the line connecting the two cell centers is perpendicular to and bisected by the shared edge .

### 🔍 Important Context: Where Gradient is Used

It is crucial to note that in the MPAS-A dynamical core, the **prognostic equations are often written in a vector-invariant form**. This means the model does not always explicitly compute a cell-centered gradient of a scalar for the main time tendency. Instead, it uses the edge-based gradient to compute terms like the pressure gradient force and the gradient of kinetic energy ($K$), which are then projected back onto the edges to update the normal velocity .

### 💡 Practical Note for Post-Processing

If you are analyzing MPAS output data (rather than looking at the model source code), and you need the gradient at the **cell centers**, the model's native approach is not directly applicable. In that case, you would typically use a reconstruction method like **Green-Gauss** or **Least-Squares** on the unstructured mesh.

The `uxarray` Python package, developed by the MPAS community, implements the Green-Gauss theorem for this purpose. It computes the cell-centered gradient by summing the fluxes across the cell faces :

$$
\nabla \phi (\mathbf{C}) \approx \frac{1}{Vol(\mathbf{C})} \sum_{i,j} \frac{\phi(\mathbf{C}_i) + \phi(\mathbf{C}_j)}{2} \mathbf{l}_{ij} \tilde{\mathbf{n}}_{ij}
$$

where $\mathbf{l}_{ij}$ is the edge length and $\tilde{\mathbf{n}}_{ij}$ is the unit normal vector.

---
### Summary
*   **In the MPAS-A dynamical core**: The gradient is primarily an **edge-based operator** ($\frac{1}{d_e}\sum \pm h_i$) used to compute forces in the momentum equation.
*   **For cell-centered analysis**: You must use a finite-volume reconstruction (like Green-Gauss) on the unstructured mesh, which is not a native prognostic operation in the core but is available through analysis tools like `uxarray`.

## Vorticity

In MPAS-A, vorticity is not calculated at the center of the hexagonal cells (primal grid) like scalars such as water vapor, but is instead **located at the vertices (corners) of the Voronoi mesh**. This is determined by the C-grid staggering: velocity is defined at cell edges, and vorticity as a derived quantity is naturally defined at vertices.

### 📐 Discrete Calculation Method

The calculation of vorticity (usually referring to the vertical component of relative vorticity) is based on the **circulation theorem**. For each vertex $v$ on the mesh, MPAS-A calculates vorticity using the velocity circulation along the edges around that vertex divided by the associated area.

The formula can be expressed as:
$$
\eta_v = \frac{1}{A_v} \sum_{e \in E(v)} u_e \cdot d_e
$$

Where:
*   **$\eta_v$**: Relative vorticity at vertex $v$.
*   **$A_v$**: The area of the dual grid cell (Delaunay triangle) corresponding to vertex $v$.
*   **$E(v)$**: The set of edges surrounding vertex $v$.
*   **$u_e$**: The normal velocity on edge $e$ (a prognostic variable).
*   **$d_e$**: The length of edge $e$.

This method is essentially a discrete implementation of Stokes' theorem, converting the area integral of vorticity into a line integral along the boundary (i.e., circulation), ensuring geometric consistency.

### 🔍 Important Context

*   **Role in the dynamical core**: MPAS-A uses a **vector-invariant form** of the momentum equations. In the momentum equations, the Coriolis force term involves the product of absolute vorticity ($\eta + f$) and velocity. This term is not directly applied at vertices; instead, absolute vorticity is first reconstructed/interpolated from vertices to edges, and then multiplied by the normal velocity on the edges to calculate the tendency.
*   **Data analysis and visualization**: If you are processing MPAS output data, the vorticity variable (such as `vorticity`) is stored on the **dual grid (triangular mesh)**. Tools like `uxarray` can directly read and visualize these data on the native unstructured grid without the need for regridding to a regular latitude-longitude grid.
*   **Conservation properties**: The discretization scheme adopted by MPAS-A is specifically designed to maintain the Lagrangian conservation of potential vorticity, which is crucial for the long-term stability of the model.

# References

1. [Global Nonhydrostatic Modeling Using Voronoi Meshes](https://www.ecmwf.int/sites/default/files/elibrary/2014/76424-global-nonhydrostatic-modelling-using-voronoi-meshes_0.pdf)