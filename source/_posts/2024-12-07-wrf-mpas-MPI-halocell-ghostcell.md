---
layout: post
title: WRF | MPAS | MPI | halo/ghost cell
categories: [WRF]
tags: [WRF, MPAS, HPC, NWP, MPI, halo cell, ghost cell]
author: wpsze
date: 2024-12-07 20:50:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/raumO6Y.png
banner_img: https://i.imgur.com/raumO6Y.png
---

# Message Passing Interface (MPI)

訊息傳遞介面（Message Passing Interface，縮寫MPI）是一個平行計算的應用程式介面（API），常在超級電腦、電腦叢集等**非共用主記憶體環境**程式設計。

The message passing interface effort began in the summer of **1991** when a small group of researchers started discussions at a mountain retreat in Austria. Out of that discussion came a Workshop on Standards for Message Passing in a **Distributed Memory Environment**, held on April 29–30, **1992** in Williamsburg, Virginia.

MPI is a communication protocol for programming parallel computers. 

# Numerical Approach

## The problem: diffusion in a two-dimensional domain

$$
\frac{\partial{u}}{\partial{t}} = D\left(\frac{\partial^2{u}}{\partial{x^2}} + \frac{\partial^2{u}}{\partial{y^2}}\right) 
\tag{1}
$$

and apply the finite difference mehtod and above equation is expressed as,

$$ 
\frac{u_{i,k}^{j+1} - 2u_{i,k}^{j}}{\Delta t} = D \left(\frac{u_{i+1,k}^j-2u_{i,k}^j+u_{i-1,k}^j}{\Delta x^2}+ \frac{u_{i,k+1}^j-2u_{i,k}^j+u_{i,k-1}^j}{\Delta y^2}\right)
\tag{2}
$$

Equation (2) is really all is needed in order to find the solution across the whole domain at each subsequent time step. It is rather easy to implement a code that does this sequentially, with one process (CPU). **However, here we want to discuss a parallel implementation that uses multiple processes**.

![](https://i.imgur.com/EIfd3nA.png){width=400}

## Use mutiple cores, MPI,

It shows how processes #0 and #1 will need to communicate in order to evaluate the solution near the boundary. This is where MPI enters. In the next section, we are going to look at an efficient way of communicating.

- Two neighboring processes need to communicate in order to find the solution near the boundary. Process #0 needs to know the value of the solution in B in order to calculate the solution at the grid point A. Similarly, process #1 needs to know the value at point C in order to calculate the solution at the grid point D. These values are unknown by the processes, until communication between processes #0 and #1 occurs.

![](https://i.imgur.com/gwauub0.png){width=400}

## Communication among processes: ghost cells

Halo cells/ghost cells are additional grid points added around the edges of the computational domain to extend the grid beyond the physical boundaries of the model. They are crucial for ensuring that calculations at the boundaries can utilize information from adjacent grid points, which may not be directly part of the simulation domain.

![](https://i.imgur.com/5b51gg2.png){width=400}

![Non-uniform grid (top figure) decomposed into two uniform grids using a ghost cells generated for the data exchange (bottom figure)(from [35]). (from Perović, N., Frisch, J., Salama, A., Sun, S., Rank, E., & Mundani, R. P. (2017). Multi-scale high-performance fluid flow: Simulations through porous media. Advances in Engineering Software, 103, 85-98.)](https://i.imgur.com/SLlYNCN.png){width=600}


An important notion in computational fluid dynamics is the one of **ghost cells**. This concept is useful whenever the **spatial domain is decomposed into multiple sub-domains**, each of which is solved by one process.

**Here is the problem**: it is very **inefficient** to have process #0 and process #1 **communicate each time** they need a node from the neighboring process: it would result in an **unacceptable communication overhead**.

Instead, what is common practice is to surround the “real” sub-domains with extra cells called **ghost cells**. 

{% note danger %}
These **ghost cells** represent **copies** of the solution at the boundaries of neighboring sub-domains. 
{% endnote %}

At each time step, the old boundary of each sub-domain is passed to its neighbors. This allows the new solution at the boundary of a sub-domain to be calculated with a significantly reduced communication overhead. The net effect is a speedup in the code.

- Communication among processes without (left) and with (right) ghost cells. Without ghost cells, each cell on the boundary of a sub-domain needs to pass its own message to a neighboring process. **Using ghost cells allows to minimize the number of messages passed**, as many cells belonging to the boundaries of a process are exchanged at once with a single message. Here, for example, Process #0 is passing its entire North boundary to Process #1, and its entire East boundary to Process #2.

![](https://i.imgur.com/pYIM9km.png){width=500}

# WRF

## Three level domain decomposition of WRF

The entire study area in the WRF is called a **domain**. The subdomain obtained by dividing the domain with processes is called a **patch**, *a process known as domain decomposition*. The subdomains are assigned to each process. The patch can also be subdivided into **tiles**, *a process known as tile division*.

![](https://i.imgur.com/XgaxGpW.png){width=400}

WRF uses 

- `nproc_x` to denote the *number of processes* in the **east–west direction** of the domain and 
- `nproc_y` to denote the *number of processes* in the **north–south** direction of the domain. 

The domain decomposition should follow the following condition:

$$
\mbox{n} = \mbox{nproc}_x \times \mbox{nproc}_y
\tag{3}
$$

![](https://i.imgur.com/SqQhBMB.png){width=500}

### [Performance Analysis and Optimizations of the Weather Research and Forecasting (WRF) Model | 2018](https://www.cisl.ucar.edu/sites/default/files/2021-09/Dixit_Patel_Presentation.pdf)
- **Intel** 18.0.1 + SGI MPT 2.18 gives best performance
- **Compiler Optimizations**
  - GNU : `-ofast`
  - Intel : `-xHost -fp-model fast =2`
- **Hyperthreaded runs lowered model performance**

{% gi 4 2-2 %}
![1](https://i.imgur.com/jNWRgGL.png)
![2](https://i.imgur.com/QH6rJCB.png)
![3](https://i.imgur.com/QsOLIMW.png)
![4](https://i.imgur.com/EG86Edj.png)
{% endgi %}

- Scalability Summary

![](https://i.imgur.com/82xzzGC.png){width=600}

### [Tutorial Notes: WRF Software 2.1](http://danida.vnu.edu.vn/cpis/files/Refs/WRF/WRF%20Software.pdf)

- WRF can be run serially or as a parallel job 
- WRF uses **domain decomposition** to divide total amount of work over parallel processes • Since the process model has two levels (heavy-weight and light-weight = MPI and OpenMP), the decomposition of the application over processes has two levels:
  - The **domain** is first broken up into rectangular pieces that are assigned to heavy-weight processes. These pieces are called **patches**
  - The **patches** may be further subdivided into smaller rectangular pieces that are called **tiles**, and these are assigned to **threads** within the process.
- Model domains are decomposed for parallelism on two-levels 
  - **Patch**: section of model domain  a located to a **distributed memory  node**, this is the scope of a mediation layer solver or physics driver. 
  - **Tile**: section of a patch allocated to a **shared-memory processor** within a node; this is also the scope of a model layer subroutine. 
  - Distributed memory parallelism is over patches; shared memory parallelism is over tiles within patches


# MPAS

## [An overview of the structure of MPAS meshes | Boulder2023](https://www2.mmm.ucar.edu/projects/mpas/tutorial/Boulder2023/lectures/mesh_structure.pdf)

![](https://i.imgur.com/SsGuRLq.png){width=600}
![](https://i.imgur.com/PpBWRMb.png){width=600}
![](https://i.imgur.com/SsaBchP.png){width=600}
![](https://i.imgur.com/raumO6Y.png){width=600}
![](https://i.imgur.com/S6VjTov.png){width=600}


# References

1. [Parallel programming with Julia using MPI](http://www.claudiobellei.com/2018/09/30/julia-mpi/)
2. [Chapter 2 Hierarchical Grids](https://www.iue.tuwien.ac.at/phd/quell/Hierarchical-Grids.html)
3. Perović, N., Frisch, J., Salama, A., Sun, S., Rank, E., & Mundani, R. P. (2017). **Multi-scale high-performance fluid flow: Simulations through porous media**. Advances in Engineering Software, 103, 85-98.
4. Huang, J., Wang, W., Wang, Y., Jiang, J., Yan, C., Zhao, L., & Bai, Y. (2023). **Performance Evaluation and Optimization of the Weather Research and Forecasting (WRF) Model Based on Kunpeng 920**. Applied Sciences, 13(17), 9800. <https://doi.org/10.3390/app13179800>
5. [Performance Analysis and Optimizations of the Weather Research and Forecasting (WRF) Model | 2018](https://www.cisl.ucar.edu/sites/default/files/2021-09/Dixit_Patel_Presentation.pdf)
   1. **Intel** 18.0.1 + SGI MPT 2.18 gives best performance
6. [MPAS-A Tutorial Agenda | Boulder2023](https://www2.mmm.ucar.edu/projects/mpas/tutorial/Boulder2023/agenda.html)
   1. [An overview of the structure of MPAS meshes](https://www2.mmm.ucar.edu/projects/mpas/tutorial/Boulder2023/lectures/mesh_structure.pdf)
7. [MPAS | skamarock_20170213](https://www.tropmet.res.in/introspect/presentation/13feb/skamarock_20170213.pdf)