---
layout: post
title: "CFD | Boussinesq approximation (buoyancy)"
categories: [CFD]
tags: [OpenFOAM, HPC]
author: wpsze
date: 2025-01-03 15:48:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/fFT42Yk.png
banner_img: https://i.imgur.com/fFT42Yk.png
---

# Boussinesq approximation (buoyancy)

In fluid dynamics, the Boussinesq approximation is used in the field of buoyancy-driven flow (also known as natural convection). It ignores density differences except where they appear in terms multiplied by $g$, the acceleration due to gravity. 

Boussinesq flows are common in nature (such as atmospheric fronts, oceanic circulation, katabatic winds), industry (dense gas dispersion, fume cupboard ventilation), and the built environment (natural ventilation, central heating). The approximation can be used to simplify the equations describing such flows, whilst still describing the flow behaviour to a high degree of accuracy.


$$ 
\frac{\partial\rho}{\partial t} + \nabla\cdot\left(\rho\mathbf{u}\right) = 0
$$

If density variations are ignored, this reduces to
$$
\nabla\cdot\mathbf{u} = 0
$$

The general expression for conservation of momentum of an incompressible, Newtonian fluid (the Navier–Stokes equations) is

$$
\frac{\partial \mathbf{u}}{\partial t} + \left( \mathbf{u}\cdot\nabla \right) \mathbf{u} = -\frac{1}{\rho}\nabla p + \nu\nabla^2 \mathbf{u} + \frac{1}{\rho}\mathbf{F}
$$

where $F$ is the sum of any body forces such as gravity. In this equation, density variations are assumed to have a fixed part and another part that has a linear dependence on temperature:

$$\rho = \rho_0 - \alpha\rho_0(T-T_0),$$

where $\alpha$ is the coefficient of **thermal expansion**. The Boussinesq approximation states that the density variation is only important in the buoyancy term.

If $F = \rho \mathbf{g}$ is the gravitational body force, the resulting conservation equation is

$$ 
\frac{\partial \mathbf{u}}{\partial t} + \left( \mathbf{u}\cdot\nabla \right) \mathbf{u} = -\frac{1}{\rho_0}\nabla (p-\rho_0\mathbf{g}\cdot\mathbf{z}) + \nu\nabla^2 \mathbf{u} - \mathbf{g}\alpha(T-T_0)
$$

In the equation for heat flow in a temperature gradient, the heat capacity per unit volume, $$\rho C_p$$, is assumed constant and the dissipation term is ignored. The resulting equation is

$$
\frac{\partial T}{\partial t} + \mathbf{u}\cdot\nabla T = \frac{k}{\rho C_p}\nabla^2T +\frac{J}{\rho C_p}
$$

where $J$ is the rate per unit volume of internal heat production and $k$ is the **thermal conductivity**.

The three numbered equations are the basic convection equations in the Boussinesq approximation.

# References

- [The Boussinesq Approximation](https://www.comsol.com/multiphysics/boussinesq-approximation)
- [What is the Boussinesq Approximation?](https://www.simscale.com/docs/simwiki/cfd-computational-fluid-dynamics/what-is-boussinesq-approximation/)
- [The Boussinesq approximation for buoyant flows](https://arxiv.org/abs/2206.03046)