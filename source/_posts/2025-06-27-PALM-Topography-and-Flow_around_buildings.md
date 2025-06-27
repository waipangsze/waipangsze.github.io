---
layout: post
title: PALM | Topography and Flow_around_buildings
categories: [PALM]
tags: [NWP, MPAS, WRF, PALM, LES, Urban, PALM, Topography]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-06-27 11:00:00
index_img: https://i.imgur.com/Unyeq0D.png
banner_img: https://i.imgur.com/Unyeq0D.png
---

# Exercise presentations

- <https://palm.muk.uni-hannover.de/trac/wiki/doc/tut/palm#Exercisepresentations>

![](https://i.imgur.com/vli06Jo.png)

## E3: Flow around a cubical building

### Required lecture

Required lectures:

- Topography
- Non-cyclic boundary conditions
- Restart runs

Required files:

- Parameter file (`*_p3d`, `*_pcr`)
  - Input files can be downloaded from the supplementary material. Within the parameter files, some options are left blank (marked as “???”) and need to be filled in before starting the simulation.
- Topography file (`*_static`)

#### flow_around_cube_cyclic

- namelist file: `flow_around_cube_noncyclic_p3d`
  - set `topography` = `read_from_file`
- topography file: `flow_around_cube_noncyclic_topo`
  - alternatively use provided python script to create **static input file** `flow_around_cube_noncyclic_static`
- set `topography`
  - ![](https://i.imgur.com/ZgeuKkS.png)

{% gi 4 2-2 %}
![](https://i.imgur.com/cVrBqQ5.png)
![](https://i.imgur.com/71HGboz.png)
![](https://i.imgur.com/sEDjfww.png)
![](https://i.imgur.com/yCikg50.png)
{% endgi %}

##### Prapare `*_static`

![](https://i.imgur.com/JFTbGlz.png)

- <https://palm.muk.uni-hannover.de/trac/wiki/doc/app/iofiles/pids/static#topo>
- <https://palm.muk.uni-hannover.de/trac/wiki/doc/app/initialization_parameters#topography>
- <https://palm.muk.uni-hannover.de/trac/wiki/doc/app/iofiles#TOPOGRAPHY_DATA>

##### my toy case

```python
......
# specify general grid parameters
# these values must equal those set in the initialization_parameters
self.nx = 245
self.ny = 83
self.nz = 80
self.dx = 2.0
self.dy = 2.0
self.dz = 2.0
......
# Set index ranges for buildings
build_inds_start_x = [ 80, 120, 160 ]
build_inds_end_x   = [ 99, 139, 179 ]

build_inds_start_y = [20, 50] #build_inds_start_x
build_inds_end_y   = [39, 69] #build_inds_end_x
......
```

![](https://i.imgur.com/HJTScbq.png){width=400}

#### flow_around_cube_noncyclic

##### Modify `*_p3d`, `*_pcr`

- namelist file: `flow_around_cube_noncyclic_p3d`
- topography file: `flow_around_cube_noncyclic_topo`
  - alternatively use provided python script to create **static input file** `flow_around_cube_noncyclic_static`

1. psolver [link](https://palm.muk.uni-hannover.de/trac/wiki/doc/app/initialization_parameters#psolver)
   1. 'poisfft'
2. end_time
   1. 9000.0
3. Message: psolver = "poisfft" requires that boundary conditions along x and y are both either cyclic or non-cyclic
   1. 'https://docs.palm-model.com/25.04/Reference/LES_Model/Logging/#PAC0077'
   2. Description: Switch to `psolver = 'multigrid'` or set `bc_lr = bc_ns`.
