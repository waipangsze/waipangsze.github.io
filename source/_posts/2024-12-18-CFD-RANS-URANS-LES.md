---
layout: post
title: "CFD | RANS vs. URANS vs. LES"
categories: [CFD]
tags: [OpenFOAM, HPC]
author: wpsze
date: 2024-12-18 16:06:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/GkV6pMK.png
banner_img: https://i.imgur.com/GkV6pMK.png
---

# LES

Consider the 1D Burgers equation in terms of the velocity field $u(x, t)$
$$
\begin{equation*}
\frac{\partial u}{\partial t}+\frac{\partial u^{2} / 2}{\partial x}=\frac{\partial}{\partial x}\left(v \frac{\partial u}{\partial x}\right) \tag{1}
\end{equation*}
$$

# LES

In the continuous LES approach the scale separation is obtained via low-pass filtering. The spatial filtering operation (no time-filtering is considered here) can be expressed as the convolution product between the unfiltered point-wise velocity field and some suitable filter function $G$, that is
$$
\begin{equation*}
\bar{u}(x, t ; \Delta)=\int_{R} G\left(x-x^{\prime} ; \Delta\right) u\left(x^{\prime}, t\right) d x^{\prime} \tag{2}
\end{equation*}
$$
$\Delta$ being the characteristic filter width. The LES equation is obtained by applying (2) to each term of (1)
$$
\begin{equation*}
\frac{\partial \bar{u}}{\partial t}+\frac{\partial \bar{u}^{2} / 2}{\partial x}=\frac{\partial}{\partial x}\left(v \frac{\partial \bar{u}}{\partial x}\right)+\underbrace{\frac{\partial\left(\bar{u}^{2}-u^{2}\right) / 2}{\partial x}}_{\frac{\partial f(\bar{u}, u)}{\partial x}} \tag{3}
\end{equation*}
$$

Commutation between filtering and derivative being applied

# URANS

As a counterpart, in the continuous URANS approach a statistical averaging is adopted.
$$
\begin{equation*}
\langle u\rangle(x, t)=\lim _{N \rightarrow \infty} \frac{1}{N} \sum_{k=1}^{N} u_{k}(x, t) \tag{4}
\end{equation*}
$$


$N$ being the number of sample of the ensemble averaging. The URANS equation is obtained by applying (4) to each term of (1)

$$
\begin{equation*}
\frac{\partial\langle u\rangle}{\partial t}+\frac{\partial\langle u\rangle^{2} / 2}{\partial x}=\frac{\partial}{\partial x}\left(v \frac{\partial\langle u\rangle}{\partial x}\right)+\underbrace{\frac{\partial\left(\langle u\rangle^{2}-u^{2}\right) / 2}{\partial x}}_{\frac{\partial g(\langle u\rangle, u)}{\partial x}} \tag{5}
\end{equation*}
$$

# Close equations

In order to close Eqs.(3) and (5), suitable models are used, that is 

$$
f(\bar{u}, u) \cong f_{\text {LES }}(\bar{u}), \qquad g(\langle u\rangle, u) \cong g_{\text {URANS }}(\langle u\rangle)
$$

# Discretization

If we consider now the practical solution, a discretization of the derivative is introduced. Assume the same discretization is adopted for the LES and URANS equations, so that we can write a unique equation
$$
\frac{\delta u_{j}}{\delta t}+\frac{\delta u_{j}^{2} / 2}{\delta x}=\frac{\delta}{\delta x}\left(v \frac{\delta u_{j}}{\delta x}\right)+\frac{\delta}{\delta x}\left\{\begin{array}{c}
f_{L E S}\left(u_{j}\right)  \tag{6}\\
g_{\text {URANS }}\left(u_{j}\right)
\end{array}\right.
$$
that characterizes LES or URANS depending on the closure model.

{% danger note}
One can deduce that the discrete equation (6) never implied a real application of a spatial filtering or an ensemble averaging. The only assessment is the spatial filtering implicitly induced by the discretization, a fact that is congruent to the LES assumption but not to the URANS one.
{% endnote}

The Eq.(6) must be well posed by the set of initial and boundary conditions. This point adds a distinction as they should be different for LES and URANS.
