---
layout: post
title: MPAS | Minimum computer resources for running MPAS
categories: [MPAS]
tags: [WRF,NWP]
author: wpsze
date: 2024-10-28 14:52:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://mpas-dev.github.io/atmosphere/MPAS-var-res_mesh.png
banner_img: https://mpas-dev.github.io/atmosphere/MPAS-var-res_mesh.png
---

# MPAS-A

{% note primary %}
**Amount of memory needed** to run a particular simulation is to multiply the number of horizontal cells in the mesh by **0.175 MB/cell**
{% endnote %}

{% note primary %}
We find that on Cheyenne, MPAS-A scales with around **70% parallel efficiency down to around 100 grid columns per MPI task**
{% endnote %}

- [Feb 16, 2021 | Minimum computer requirements for running MPAS | mgduda](https://forum.mmm.ucar.edu/threads/minimum-computer-requirements-for-running-mpas.10047/)
  - Regarding memory requirements, we've found that a reasonable way to estimate the **amount of memory needed to run a particular simulation is to multiply the number of horizontal cells in the mesh by 0.175 MB/cell**; this estimate is based on a single-precision model run with around 55 vertical levels. So, for example, a global, quasi-uniform 30-km simulation might require around 655362 cells * 0.175 MB/cell = 115,000 MB of memory. Note that this is the aggregate amount of memory across all nodes of a computing cluster, and not the amount of memory on each node. Also, this is a rough estimate only, and the amount of memory required for a simulation can vary a little based on, for example, your choice of physics parameterizations.
  - In principle, there is no minimum requirement for the number of processors (as long as there is at least one processor), since using fewer processors simply means that a simulation will take longer to run. If one were patient enough, and if enough memory were available, a single processor could be used for any simulation. For a few notes on estimating simulation throughput with MPAS-Atmosphere with different numbers of processors, [this forum thread](https://forum.mmm.ucar.edu/phpBB3/viewtopic.php?f=12&t=9002&p=16332&hilit=core+hours#p16332) may be helpful.
  - Regarding the kind of system to use, I can offer that we have run MPAS-Atmosphere on a wide variety of computing systems: Raspberry Pi single-board computers, macOS laptops, linux desktops with 4 or 8 cores, and some large clusters like Cheyenne and Summit. So there isn't necessarily a specific system that I could recommend -- all come with pros and cons (cost, reliability, performance). One point worth mentioning, however, is that a higher-performance interconnect between nodes would generally be beneficial, as MPI communication is performed extensively throughout each time step in the model.
  - Again, the 0.175 MB/cell figure is a rough estimate. Using more cores will generally increase the model simulation rate
  - It may be a bit difficult to accurately calculate the memory required by a simulation at the init_atmosphere stage, since we don't yet know, e.g., which physics schemes will be used in the simulation or which diagnostics will be requested in output streams. In principle this could be done when the atmosphere_model program starts, but gauging memory requirements of physics and diagnostics would still be quite a challenge, I think.
  - I do agree that it would be nice to give better indications of out-of-memory errors. One tractable step in this direction might be to check the status of all major memory allocations in the model; for example, just checking allocations of model fields defined in the Registry.xml file might help. We'll give this some serious consideration, and if you have any other suggestions, we'd be glad to hear them
- [Apr 2, 2020 | Computing power necessary for global WRF/MPAS](https://forum.mmm.ucar.edu/threads/computing-power-necessary-for-global-wrf-mpas.9002/#p16332)
  - For both WRF and MPAS, **the computational costs scale more or less linearly with the number of grid cells, and inversely with the integration time step**. So, if you have the computational cost for one simulation, you can quickly estimate the cost of other simulations.
  - On the **NWSC Cheyenne computer**, the **computational cost for a global, quasi-uniform 15-km simulation with 2,621,442 grid** columns and 56 vertical layers is about **530 core-hours per simulated day**. This includes no file output and the default "mesoscale reference" suite of physics parameterizations, with the radiation schemes called once every 15 simulated minutes; the model integration time step is 90 seconds.
  - For example, then, if we were to estimate the cost of a global, variable-resolution simulation on a mesh with **6,488,066 grid** columns and a **minimum grid distance of 3 km**, which would probably dictate an integration time step of around **18 seconds**, a rough estimate would be:
    - 530 core-hours/day * (6488066 / 2621442) * (90 / 18) = 6559 core-hours per simulated day
  - A few other factors that influence computational cost include:
    - The suite of physics parameterizations that are used in the simulation.
    - The amount of model output written to the filesystem: more file output implies more time spent writing data.
    - The compiler used to build the model executables, as well as the optimization flags used for that compiler.
    - The number of MPI tasks used to run the model: there is a general downward trend in parallel efficiency as more MPI tasks are used in a model simulation. **We find that on Cheyenne, MPAS-A scales with around 70% parallel efficiency down to around 100 grid columns per MPI task**.
    - The simulation length: there is a cost associated with "starting up" the model (reading initial conditions, allocating memory, initializing data structures, etc.), and longer simulations will allow that start-up cost to be amortized over more integration time steps.
