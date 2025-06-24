---
layout: post
title: MPAS | Scalability | Speedup
categories: [MPAS]
tags: [NWP, MPAS, WRF, partition, precise, intel, gfortran, ifort]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-06-23 10:14:00
index_img: https://i.imgur.com/0qiFjTO.png
banner_img: https://i.imgur.com/0qiFjTO.png
---

- [**WRF | MPAS | MPI | halo/ghost cell**](https://waipangsze.github.io/2024/12/07/wrf-mpas-MPI-halocell-ghostcell/)
- [**MPAS | -fp-model precise**](https://waipangsze.github.io/2025/06/11/MPAS-fp-model-precise/)
  - [Speedup](https://waipangsze.github.io/2025/06/11/MPAS-fp-model-precise/#elapsed-time)

---

# Introduction

- `Speedup` tells you how much faster your parallel algorithm can be than its sequential counterpart. 
- `Scalability` tells you how the performance of your parallel algorithm behaves as you add hardware.

## Experiment (no precise mode)

- MPASv8.2.2 by intel compiler
- MPAS-120km: x1.40962
- Core = 2, 4, 8, 16, 24, 32, 48, 68, 68xN
- Initial datetime = 2023-04-02_00UTC
- IC source = ERA5
- T+12days

### Elapsed time

| Step | Mesh                | IC         | Elapsed time  | Core |
|------|---------------------|------------|---------------|------|
| init | MPAS-120km-MPASv822 | 2023040200 | <60s          |   68 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 1h20m         |   68x4  |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 1h33m         |   68x3  |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 2h5m          |   68x2  |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 2h8m          |   68 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 3h30m         |   48 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 4h51m         |   32 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 9h24m         |   24 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 16h50m        |   16 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 25h33m        |    8 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 49h20m        |    4 |
| atm  | MPAS-120km-MPASv822 | 2023040200 | 62h36m        |    2 |

![](https://i.imgur.com/wYM24w2.png)

- Averaging t2m globally

![](https://i.imgur.com/BcO873A.png)

# References

1. [Towards convection-resolving, global atmospheric simulations with the Model for Prediction Across Scales (MPAS): an extreme scaling experiment](https://gmd.copernicus.org/preprints/8/6987/2015/gmdd-8-6987-2015.pdf)
2. [X. Hao et al., "swMPAS-A: Scaling MPAS-A to 39 Million Heterogeneous Cores on the New Generation Sunway Supercomputer," in IEEE Transactions on Parallel and Distributed Systems, vol. 34, no. 1, pp. 141-153, 1 Jan. 2023, doi: 10.1109/TPDS.2022.3215002. keywords: {Atmospheric modeling;Computational modeling;Supercomputers;Predictive models;Task analysis;Computer architecture;Scalability;MPAS-A;atmosphere science;heterogeneous core;sunway supercomputer;I/O},](https://ieeexplore.ieee.org/document/9920218)

