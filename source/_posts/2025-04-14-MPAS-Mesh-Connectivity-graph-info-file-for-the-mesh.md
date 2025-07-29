---
layout: post
title: MPAS | Mesh Connectivity (graph.info) file for the mesh
categories: [MPAS]
tags: [WRF, MPAS, HPC, NWP, graph.info, grid.nc, mesh generation]
author: wpsze
date: 2025-04-14 11:12:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/GOFUeXW.png
banner_img: https://i.imgur.com/GOFUeXW.png
---

1. [https://mpas-dev.github.io/](https://mpas-dev.github.io/)
2. [/MPAS-Model/releases](https://github.com/MPAS-Dev/MPAS-Model/releases)
3. [MPAS-Atmosphere Users' Guide V4.0](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_4.0.pdf)
4. [MPAS-Atmosphere Users' Guide V5.3](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_5.3.pdf)
5. [MPAS-Atmosphere Users' Guide V6.3](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_6.3.pdf)
6. [MPAS-Atmosphere Users' Guide V7.0](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_7.0.pdf)
7. [MPAS-Atmosphere Users' Guide V8.2.0](https://www2.mmm.ucar.edu/projects/mpas/mpas_atmosphere_users_guide_8.2.0.pdf)
8. [Joint WRF/MPAS Users Workshop 2024](https://www.mmm.ucar.edu/events/133129/agenda)

---

# MPAS-Atmosphere Meshes

- <https://www2.mmm.ucar.edu/projects/mpas/site/access_code/meshes.html>

Several resolutions of quasi-uniform and refined meshes are available for download. Each mesh download provides the following:

- an SCVT mesh on the unit sphere
- **the mesh connectivity (graph.info) file for the mesh**
- partitionings of the mesh (e.g., graph.info.part.32) for various MPI task counts

Additionally, for quasi-uniform meshes, “static” files using the default datasets are available. The static file downloads provide single-precision static files in CDF-5/64-bit data format and mesh connectivity files.

---

# Script for graph.info

{% fold info @grid2graph.sh %}
```sh
#!/bin/bash

#------------------------------------------------#
# Author:         wpsze
# Email:          wpsze@gmail.com
# File:           grid2graph.sh
# Date:           2025-07-29 17:05:24
# Version:        0.0 
# Description:    The purpose of the script
# Copyright (C):  2025 All rights reserved
#------------------------------------------------#

#set -e # stop the shell on first error
#set -u # fail when using an undefined variable
#set -x # echo script lines as they are executed
set -o pipefail


source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate venv
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

file=$1
ncells=`ncdump -h $file | grep "nCells = " | cut -d ' ' -f3`
nedges=`ncdump -h $file | grep "nEdges = " | cut -d ' ' -f3`
maxedges=`ncdump -h $file | grep "maxEdges = " | cut -d ' ' -f3`
echo $ncells $nedges
ncdump -v cellsOnCell $file | sed -e "s/[\,,;]//g" | grep -E "^  [0-9][0-9]*" | awk '{ while(++i<=NF) printf (!a[$i]++) ? $i FS : ""; i=split("",a); print ""}'
```
{% endfold %}

Take `MPAS-120km` as example,

{% fold info @graph.info %}
```console
40962 122880
10247 10243 10244 10245 10246 0 
10248 10249 10250 10251 10252 0 
10254 10255 10256 10257 10253 0 
10262 10258 10259 10260 10261 0 
10266 10267 10263 10264 10265 0 
10272 10268 10269 10270 10271 0 
10273 10274 10275 10276 10277 0 
10280 10281 10282 10278 10279 0 
10284 10285 10286 10287 10283 0
......
```
{% endfold %}

---

# Metis

METIS is a set of serial programs for partitioning graphs, partitioning finite element meshes, and producing fill reducing orderings for sparse matrices. The algorithms implemented in METIS are based on the multilevel recursive-bisection, multilevel k-way, and multi-constraint partitioning schemes developed in our lab.

Download:
- <http://glaros.dtc.umn.edu/gkhome/metis/metis/download>

[**MPAS | install Metis**](https://waipangsze.github.io/2024/05/31/MPAS_install_metis_error/)

## gpmetis

```sh
gpmetis -minconn -contig -niter=200 graph.info <num_core>
```

which num_core = 12 or ...


# If generating graph.info for new mesh

```limited_area/mesh.py
def create_graph_file(self, graphFname):
    """ Create a graph.info file for the current mesh """

    # In the limited_area program, this function will always create
    # a graph.info file for a regional mesh. Thus, the variables here
    # are not preloaded and are being read from disk
```

```limited_area/limited_area.py
def create_partiton_fname(self, name, mesh, **kwargs):
    """ Generate the filename for the regional graph.info file"""
    return name+'.graph.info'
```

# References

1. [gpmetis error | Nov 12, 2024](https://forum.mmm.ucar.edu/threads/gpmetis-error.19844/#post-48136)
   1. should probably look like `512 513 514 509 510 511`
   2. Here's the part of the MPAS-Limited-Area tool that should be writing this connectivity info: [MPAS-Limited-Area/limited_area/mesh.py at v2.1 · MPAS-Dev/MPAS-Limited-Area](https://github.com/MPAS-Dev/MPAS-Limited-Area/blob/v2.1/limited_area/mesh.py#L180-L186).
   3. We've merge changes to support NumPy 2.x into the [MPAS-Limited-Area main branch through MPAS-Dev/MPAS-Limited-Area PR #47](https://github.com/MPAS-Dev/MPAS-Limited-Area/pull/47). Thanks again for finding this issue!