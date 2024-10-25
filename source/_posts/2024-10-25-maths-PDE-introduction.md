---
layout: post
title: Maths | PDE | Overview
categories: [Maths]
tags: [NWP, AI, ML, PDE]
author: wpsze
date: 2024-10-25 11:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Partial_func_eg.svg/1280px-Partial_func_eg.svg.png
banner_img: https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Partial_func_eg.svg/1280px-Partial_func_eg.svg.png
---

In mathematics, a **partial differential equation (PDE)** is an equation which computes a function between various partial derivatives of a multivariable function.

# Classification

A PDE is called **linear** if it is linear in the unknown and its derivatives. For example, for a function $u$ of $x$ and $y$, a second order linear PDE is of the form

$$
\begin{align*} 
a_1(x,y)u_{xx} + a_2(x,y)u_{xy} + a_3(x,y)u_{yx} + a_4(x,y)u_{yy} + a_5(x,y)u_x + a_6(x,y)u_y + a_7(x,y)u = f(x,y)
\end{align*} 
$$

where $a_i$ and $f$ are functions of the independent variables $x$ and $y$ only. (Often the mixed-partial derivatives $u_{xy}$ and $u_{yx}$ will be equated, but this is not required for the discussion of linearity.) 

- If the $a_i$ are constants (independent of $x$ and $y$) then the PDE is called **linear** with constant coefficients. 
- If $f$ is zero everywhere then the linear PDE is **homogeneous**, otherwise it is **inhomogeneous**. (This is separate from asymptotic homogenization, which studies the effects of high-frequency oscillations in the coefficients upon solutions to PDEs.

Nearest to linear PDEs are **semi-linear PDEs**, where only the highest order derivatives appear as linear terms, with coefficients that are functions of the independent variables. The lower order derivatives and the unknown function may appear arbitrarily. For example, a general second order semi-linear PDE in two variables is

$$
\begin{align*} 
a_1(x,y)u_{xx} + a_2(x,y)u_{xy} + a_3(x,y)u_{yx} + a_4(x,y)u_{yy} + f(u_x, u_y, u, x, y) = 0
\end{align*} 
$$

In a **quasilinear PDE** the highest order derivatives likewise appear only as linear terms, but with coefficients possibly functions of the unknown and lower-order derivatives:

$$
\begin{align*} 
a_1(u_x, u_y, u, x, y)u_{xx} + a_2(u_x, u_y, u, x, y)u_{xy} + a_3(u_x, u_y, u, x, y)u_{yx} + a_4(u_x, u_y, u, x, y)u_{yy} + f(u_x, u_y, u, x, y) = 0
\end{align*} 
$$

Many of the fundamental PDEs **in physics are quasilinear**, such as the **Einstein equations** of general relativity and the **Navier–Stokes equations** describing fluid motion.

A PDE without any linearity properties is called **fully nonlinear**, and possesses nonlinearities on one or more of the highest-order derivatives. An example is the **Monge–Ampère equation**, which arises in differential geometry.

# Second order PDE Classification

- [wiki](https://en.wikipedia.org/wiki/Partial_differential_equation#Second_order_equations)
- [Levandosky, Julie. "Classification of Second-Order Equations"](https://web.stanford.edu/class/math220a/handouts/secondorder.pdf)
- [Classification of PDEs](https://www.fluid.tuwien.ac.at/322042?action=AttachFile&do=get&target=Classification_v2.pdf)

The **elliptic/parabolic/hyperbolic classification** provides a guide to appropriate initial- and boundary conditions and to the smoothness of the solutions. Assuming $u_{xy} = u_{yx}$, the general linear second-order PDE in two independent variables has the form

$$
\begin{align*} 
Au_{xx} + Bu_{xy} + Cu_{yy} + Du_x + Eu_y + Fu +G= 0    
\end{align*} 
\tag{1}
$$
where $A,B,C,D,E,F,G$ may be functions of $x,y$. Caution: $A$ and $C$ must be non-zero for this method to work! If this is not the case, then use the generic method for second-order PDEs.

| Type       | Discriminant     | is reducible to            | Example            | Example equation      |
|------------|------------------|----------------------------|--------------------|-----------------------|
| `Elliptic`   | $B^2 - 4 AC < 0$ | $u_{xx} + u_{yy} + ...= 0$ | Laplace's equation | $u_{xx} + u_{yy} = 0$ |
| `Parabolic`  | $B^2 - 4 AC = 0$ | $u_{xx} + ...= 0$          | Heat equation      | $u_t - u_{xx} = 0$    |
| `Hyperbolic` | $B^2 - 4 AC > 0$ | $u_{xx} - u_{yy} + ...= 0$ | Wave equation      | $u_{tt} - u_{xx} = 0$ |

The three equations in Example above are of particular interest not only because they are derived from physical principles, but also because **every second-order linear equation of the form (1) can be reduced to an equation of one of those forms (plus lower-order terms) by making a change of variables**.

From the numerical point of view,

|                        |                 | Types                   | Computational Concern |
|------------------------|-----------------|-------------------------|-----------------------|
| Initial Value Problem  | Time evolution  | Parabolic or Hyperbolic | **Stability**             |
| Boundary Value Problem | Static solution | Elliptic                | **Efficiency**            |

## Properties of different types of PDEs

- `Elliptic`
  - Boundary conditions must be prescribed all along the boundary
  - **Local change of the boundary data influences the solution everywhere**
  - **No propagation of waves**
  - No real characteristics
- `Parabolic`
  - Parabolic equations arise by adding time dependence $u_t$ to equation which was originally elliptic.
  - Waves can propagate in one specific direction only, corresponding to time-like variable
  - Equivalent system of $n$ first-order equations has $1$ to $n-1$ real characteristics
  - Boundary conditions are required in space, initial condition in time
  - **Local change in boundary conditions affects immediately the solution at all positions in space, but only in future times**
- `Hyperbolic`
  - Waves can reflect from boundaries
  - Equivalent system of $n$ first-order equations has $n$ real characteristics.
  - Boundary conditions are required in space, initial condition in time
  - **Information about local change of boundary condition travels through space with finite speed.**

## Generalizing to Higher Dimensions

If there are n independent variables x1, x2 , …, xn, a general linear partial differential equation of second order has the form,

$$
\begin{align*} 
L u =\sum_{i=1}^n\sum_{j=1}^n a_{i,j} \frac{\partial^2 u}{\partial x_i \partial x_j} \quad+ \text{lower-order terms} = 0
\end{align*} 
$$

The classification depends upon the signature of the eigenvalues of the coefficient matrix $A = a_{i,j}$.

- `Elliptic`: the eigenvalues are all positive or all negative.
- `Parabolic`: the eigenvalues are all positive or all negative, except one that is zero.
- `Hyperbolic`: there is only one negative eigenvalue and all the rest are positive, or there is only one positive eigenvalue and all the rest are negative.
- `Ultrahyperbolic`: there is more than one positive eigenvalue and more than one negative eigenvalue, and there are no zero eigenvalues.

However, the classification only depends on linearity of the second-order terms and is therefore applicable to semi- and quasilinear PDEs as well. 