---
layout: post
title: NWP | DA | Optimal weight matrix W
categories: [NWP]
tags: [NWP, MPAS, WRF, DA, KF, EKF, EnKF]
author: wpsze
date: 2025-04-02 14:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/Mk1n43r.png
banner_img: https://i.imgur.com/Mk1n43r.png
---

- [**Data assimilation (DA)**](https://waipangsze.github.io/2024/09/24/DA-intro/)
- [**NWP | DA | Kalman Filter**](https://waipangsze.github.io/2025/04/02/NWP-DA-Kalman-Filter/)
- [**NWP | DA | 4D-Var**](https://waipangsze.github.io/2025/03/20/NWP-DA-4Dvar/)

---

To derive the **optimal weight matrix** $\mathbf{W} $in DA, we follow a minimum variance approach to combine background (model) and observation data. Here’s a step-by-step mathematical formulation:

---

# Step 1: Define Analysis and Errors
- **Analysis**: $\mathbf{x}^a = \mathbf{x}^b + \mathbf{W}(\mathbf{y}^o - \mathbf{H}\mathbf{x}^b)$
- **Analysis error**: $\mathbf{\epsilon}^a = \mathbf{x}^a - \mathbf{x}^t = \mathbf{\epsilon}^b - \mathbf{W}(\mathbf{H}\mathbf{\epsilon}^b + \mathbf{\epsilon}^o)$
  - $\mathbf{x}^t$: True state
  - $\mathbf{\epsilon}^b$: Background error ($\mathbf{x}^b - \mathbf{x}^t$)
  - $\mathbf{\epsilon}^o$: Observation error ($\mathbf{y}^o - \mathbf{H}\mathbf{x}^t$)

---

# Step 2: Analysis Error Covariance Matrix
The covariance matrix of the analysis error is:
$$
\mathbf{P}^a = \mathbb{E}[\mathbf{\epsilon}^a (\mathbf{\epsilon}^a)^T] = (\mathbf{I} - \mathbf{W}\mathbf{H})\mathbf{B}(\mathbf{I} - \mathbf{W}\mathbf{H})^T + \mathbf{W}\mathbf{R}\mathbf{W}^T
$$
- $\mathbf{B} = \mathbb{E}[\mathbf{\epsilon}^b (\mathbf{\epsilon}^b)^T]$: Background error covariance
- $\mathbf{R} = \mathbb{E}[\mathbf{\epsilon}^o (\mathbf{\epsilon}^o)^T]$: Observation error covariance

---

# Step 3: Minimize Analysis Error Variance
Minimize the **trace** of $\mathbf{P}^a $(total analysis error variance) with respect to $\mathbf{W}$:
$$
\frac{\partial \, \text{tr}(\mathbf{P}^a)}{\partial \mathbf{W}} = -2(\mathbf{I} - \mathbf{W}\mathbf{H})\mathbf{B}\mathbf{H}^T + 2\mathbf{W}\mathbf{R} = 0
$$

---

# Step 4: Solve for $\mathbf{W}$
Rearrange the derivative equation:
$$
(\mathbf{I} - \mathbf{W}\mathbf{H})\mathbf{B}\mathbf{H}^T = \mathbf{W}\mathbf{R}
$$
Expand and solve:
$$
\mathbf{B}\mathbf{H}^T = \mathbf{W}(\mathbf{H}\mathbf{B}\mathbf{H}^T + \mathbf{R})
$$
**Optimal Weight Matrix**:
$$
\mathbf{W} = \mathbf{B}\mathbf{H}^T (\mathbf{H}\mathbf{B}\mathbf{H}^T + \mathbf{R})^{-1}
$$

---

## Key Insights
1. **Structure of $\mathbf{W}$**:
   - $\mathbf{B}\mathbf{H}^T$: Covariance between background and observation spaces.
   - $(\mathbf{H}\mathbf{B}\mathbf{H}^T + \mathbf{R})^{-1}$: Normalizes by total uncertainty (background + observation errors).

2. **Assumptions**:
   - Background and observation errors are **uncorrelated**: $\mathbb{E}[\mathbf{\epsilon}^b (\mathbf{\epsilon}^o)^T] = 0$.
   - Linear observation operator $\mathbf{H}$.

---

# Comparison to 3D-Var and Kalman Filter
| Method       | Weight Equation                          | Covariance Treatment          |
|--------------|------------------------------------------|--------------------------------|
| **OI**       | $\mathbf{W} = \mathbf{B}\mathbf{H}^T (\mathbf{H}\mathbf{B}\mathbf{H}^T + \mathbf{R})^{-1}$| Static $\mathbf{B}$, $\mathbf{R}$|
| **3D-Var**   | Implicit via cost function minimization  | Static $\mathbf{B}$       |
| **Kalman**   | $\mathbf{K}_k = \mathbf{P}_{k\|k-1}\mathbf{H}^T (\mathbf{H}\mathbf{P}_{k\|k-1}\mathbf{H}^T + \mathbf{R})^{-1}$| Dynamic $\mathbf{P}_{k\|k}$|

---

# Practical Example
For a **2-observation system** (simplified from[3]):
- Background error variance: $(\sigma^b)^2$
- Observation error variance: $(\sigma^o)^2$
- Correlation coefficients: $\rho_{10}, \rho_{20}, \rho_{12}$
- Weights:
 $$
  w_1 = \frac{\rho_{10}(1 + \alpha) - \rho_{12}\rho_{20}}{(1 + \alpha)^2 - \rho_{12}^2}, \quad w_2 = \frac{\rho_{20}(1 + \alpha) - \rho_{12}\rho_{10}}{(1 + \alpha)^2 - \rho_{12}^2}
 $$
  where $\alpha = (\sigma^o)^2 / (\sigma^b)^2$.

This ensures observations with smaller errors ($\sigma^o$) or higher correlation ($\rho$) receive greater weight.

# Rerferences

1. <https://dspace.library.uu.nl/bitstream/1874/580/16/c4.pdf>
2. <http://psc.apl.washington.edu/nonwp_projects/PHC/oi.html>
3. <https://www.atmosp.physics.utoronto.ca/PHY2509/ch3.pdf>
4. <https://twister.caps.ou.edu/OBAN2019/3DVAR.pdf>
5. <https://maths.ucd.ie/~plynch/LECTURE-NOTES/NWP-2004/NWP-CH05-4-1.pdf>
6. <https://people.duke.edu/~hpgavin/Risk/interpolation.pdf>
7. <https://pro.arcgis.com/en/pro-app/latest/tool-reference/image-analyst/learn-more-about-optimal-interpolation.htm>
8. <https://twister.caps.ou.edu/OBAN2019/METR5303_Lecture14.pdf>
