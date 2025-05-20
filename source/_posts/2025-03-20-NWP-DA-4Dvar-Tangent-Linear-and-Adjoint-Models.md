---
layout: post
title: NWP | 4D-Var | Tangent Linear and Adjoint Models 
categories: [NWP]
tags: [NWP, MPAS, WRF, WRFDA, DA, 4Dvar]
author: wpsze
date: 2025-03-20 17:35:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/aymZVsL.png
banner_img: https://i.imgur.com/aymZVsL.png
---

- [**Data assimilation (DA)**](https://waipangsze.github.io/2024/09/24/DA-intro/)

---

# What Are Tangent Linear and Adjoint Models?

A mathematical explanation of the tangent linear and adjoint models in the context of variational data assimilation such as WRFDA:

## Tangent Linear Model (TLM)

Let the nonlinear forecast model be represented as a nonlinear operator $\mathbf{M}$ that evolves the model state $\mathbf{x}$ from time $t_i$ to $t_{i+1}$:

$$
\mathbf{x}_{i+1} = \mathbf{M}_i(\mathbf{x}_i)
$$

The tangent linear model is the linearization of $\mathbf{M}$ around a reference trajectory $\mathbf{x}_i$. It describes how a small perturbation $\delta \mathbf{x}_i$ in the initial state evolves:

$$
\delta \mathbf{x}_{i+1} = \mathbf{M}_i'(\mathbf{x}_i) \, \delta \mathbf{x}_i
$$

where $\mathbf{M}_i'(\mathbf{x}_i) = \frac{\partial \mathbf{M}_i}{\partial \mathbf{x}} \bigg|_{\mathbf{x}_i}$ is the Jacobian (linearized operator) of $\mathbf{M}_i$ evaluated at $\mathbf{x}_i$.

T**his linearization allows efficient computation of how small changes in the initial state affect the forecast trajectory**, which is critical for calculating the cost function in 4D-Var data assimilation.

In practical terms, the TLM is used to propagate small perturbations (or errors) in the initial conditions forward in time. For example, **if there is a small error in the initial temperature, the TLM helps predict how this error grows or diminishes over the forecast period**. This is crucial for understanding the sensitivity of the model to initial conditions and for assessing how uncertainties evolve, which is a fundamental aspect of data assimilation.

## Adjoint Model (ADM)

The adjoint model is the transpose (or adjoint) of the tangent linear model operator TLM, a concept rooted in linear algebra and optimization theory. **It propagates sensitivities backward in time and is used to compute the gradient of the cost function $J$ with respect to the initial conditions or control variables**.

- **While the TLM propagates perturbations forward, the ADM works backward**, computing the gradient of a scalar function (such as the cost function in 4DVAR) with respect to the initial conditions or other control variables.
- In data assimilation, **the cost function typically measures the mismatch between the model’s trajectory and the observational data**, weighted by their respective uncertainties. **The ADM efficiently calculates how much each component of the initial conditions contributes to this mismatch**. For instance, it can determine how a small change in initial wind speed at a specific location affects the overall forecast error, enabling targeted adjustments.
- **This capability is vital for optimization algorithms in 4DVAR**, which iteratively adjust the initial conditions to minimize the cost function. The ADM’s efficiency in computing gradients makes it indispensable for large-scale weather prediction systems, where direct computation would be computationally prohibitive.

If the tangent linear model operator is $\mathbf{M}_i'$, then the adjoint operator is $\mathbf{M}_i'^*$, defined such that for any vectors $\mathbf{a}$ and $\mathbf{b}$:

$$
\langle \mathbf{a}, \mathbf{M}_i' \mathbf{b} \rangle = \langle \mathbf{M}_i'^* \mathbf{a}, \mathbf{b} \rangle
$$

where $\langle \cdot, \cdot \rangle$ is the inner product.

The adjoint model evolves the gradient of the cost function backward:

$$
\lambda_i = \mathbf{M}_i'^* \lambda_{i+1}
$$

where $\lambda_i$ is the adjoint variable at time $t_i$, representing the sensitivity of the cost function to the model state at time $t_i$.

## Cost Function and Gradient in 4D-Var

The 4D-Var cost function $J$ typically measures the misfit between the model trajectory and observations plus a background term:

$$
J(\mathbf{x}_0) = \frac{1}{2} (\mathbf{x}_0 - \mathbf{x}_b)^T \mathbf{B}^{-1} (\mathbf{x}_0 - \mathbf{x}_b) + \frac{1}{2} \sum_{i=0}^N \left( \mathbf{y}_i - \mathbf{H}_i(\mathbf{x}_i) \right)^T \mathbf{R}_i^{-1} \left( \mathbf{y}_i - \mathbf{H}_i(\mathbf{x}_i) \right)
$$

where

- $\mathbf{x}_0$ is the initial state to optimize,
- $\mathbf{x}_b$ is the background state,
- $\mathbf{B}$ is the background error covariance,
- $\mathbf{y}_i$ are observations at time $t_i$,
- $\mathbf{H}_i$ is the observation operator,
- $\mathbf{R}_i$ is the observation error covariance.

The gradient of $J$ with respect to $\mathbf{x}_0$ is computed efficiently using the adjoint model:

$$
\nabla_{\mathbf{x}_0} J = \mathbf{B}^{-1} (\mathbf{x}_0 - \mathbf{x}_b) - \sum_{i=0}^N \mathbf{M}_{0 \to i}'^* \mathbf{H}_i'^* \mathbf{R}_i^{-1} \left( \mathbf{y}_i - \mathbf{H}_i(\mathbf{x}_i) \right)
$$

where $\mathbf{M}_{0 \to i}'^*$ is the adjoint propagator from time $t_i$ back to $t_0$, and $\mathbf{H}_i'$ is the linearized observation operator.

{% gi 2 2 %}
![](https://i.imgur.com/fUORsqe.png)
![](https://i.imgur.com/WwEjOcz.png)
{% endgi %}

## Integration in WRFDA and 4DVAR

In WRFDA, the TLM and ADM are critical components of the 4DVAR system. The 4DVAR process involves finding the initial conditions that, **when evolved through the WRF model, best fit the observations over a time window**. This is achieved by minimizing a cost function that balances the background (prior) information and the observational data.

- **Role of TLM**: The TLM is used to propagate the model forward, simulating how perturbations in the initial conditions evolve over time. **This is essential for understanding the dynamics of error growth and for computing the model’s response to small changes**.
- **Role of ADM**: The ADM computes the gradient of the cost function with respect to the initial conditions, guiding the optimization process. This gradient information is used in iterative algorithms (e.g., conjugate gradient methods) to adjust the initial state iteratively, improving the fit to observations

---

### Summary

| Concept              | Mathematical Expression                                     | Description                                  |
|----------------------|-------------------------------------------------------------|----------------------------------------------|
| Nonlinear model      | $\mathbf{x}_{i+1} = \mathbf{M}_i(\mathbf{x}_i)$           | Forecast model evolution                      |
| Tangent linear model | $\delta \mathbf{x}_{i+1} = \mathbf{M}_i'(\mathbf{x}_i) \delta \mathbf{x}_i$ | Linearized evolution of perturbations        |
| Adjoint model        | $\lambda_i = \mathbf{M}_i'^* \lambda_{i+1}$               | Backward propagation of sensitivities        |
| Cost function gradient | $\nabla_{\mathbf{x}_0} J = \mathbf{B}^{-1}(\mathbf{x}_0 - \mathbf{x}_b) - \sum_i \mathbf{M}_{0 \to i}'^* \mathbf{H}_i'^* \mathbf{R}_i^{-1} (\mathbf{y}_i - \mathbf{H}_i(\mathbf{x}_i))$ | Gradient used for optimization in 4D-Var |

This framework enables efficient computation of gradients needed for optimization in variational data assimilation, making the assimilation of observations into models computationally feasible.

---

### References

- ECMWF tutorial on tangent linear and adjoint models for variational data assimilation.
  - <https://www.ecmwf.int/sites/default/files/elibrary/2018/18542-tangent-linear-and-adjoint-models-data-assimilation.pdf>
  - <https://www2.atmos.umd.edu/~dkleist/docs/da/ECMWF_DA_TUTORIAL/Benedetti-Tangent_linear_adjoint_models.pdf>
- Mathematical foundations of variational data assimilation and adjoint methods.
  - <https://www.scielo.br/j/rbmet/a/ZP8nwSWzS9TsPLSTYjsYBGH/?lang=en>
- Description of tangent linear and adjoint models in 4D-Var data assimilation.
  - <https://gmao.gsfc.nasa.gov/pubs/docs/Yang131.pdf>
  - <https://journals.ametsoc.org/view/journals/mwre/151/1/MWR-D-22-0164.1.xml>

---

# References

1. [4D-Var for Dummies | 2009](https://gmao.gsfc.nasa.gov/events/adjoint_workshop-8/present/Sunday/Kepert1.pdf)
2. [Tangent linear and adjoint models for variational data assimilation](https://events.ecmwf.int/event/376/contributions/4287/attachments/2460/4259/M_Chrust_ADJOINT.pdf)
3. [Linearised physics: the heart of ECMWF’s 4D-Var | 2023](https://www.ecmwf.int/en/newsletter/175/earth-system-science/linearised-physics-heart-ecmwfs-4d-var)
4. [WRF 多重解析度四維變分資料同化方法之研究 | 劉志權 | 2019](http://mopl.as.ntu.edu.tw/web/ASJ/46/46-4-1.pdf)
5. [25 years of 4D-Var: how machine learning can improve the use of observations | EMCWF | 2022](https://www.ecmwf.int/en/about/media-centre/news/2022/25-years-4d-var-how-machine-learning-can-improve-use-observations)
6. [The Future of Data Assimilation: 4D-Var or Ensemble Kalman Filter?4D-Var or Ensemble Kalman Filter](https://www2.atmos.umd.edu/~ekalnay/pubs/4DVarEnKFKalnaySAMSI.ppt.pdf)
7. [ROMS 4D-Var data assimilation algorithms](https://www.myroms.org/wiki/4DVar_Tutorial_Introduction)