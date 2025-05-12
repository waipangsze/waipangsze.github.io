---
layout: post
title: MPAS | graph.info (Mesh Connectivity file)
categories: [MPAS]
tags: [NWP, MPAS, WRF, mesh generation, MPAS-Tools, jigsaw, Lloyd iteration, graph.info]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-05-11 14:24:00
index_img: https://i.imgur.com/hvaWxeW.png
banner_img: https://i.imgur.com/hvaWxeW.png
---

- [**MPAS | Mesh Connectivity (graph.info) file for the mesh**](https://waipangsze.github.io/2025/04/14/MPAS-Mesh-Connectivity-graph-info-file-for-the-mesh/)
- [**WRF | MPAS | MPI | halo/ghost cell**](https://waipangsze.github.io/2024/12/07/wrf-mpas-MPI-halocell-ghostcell/)
- [**MPAS | mesh generation**](https://waipangsze.github.io/2025/05/09/MPAS-mesh-generation/)
- [**MPAS | Variable-resolution mesh generation**](https://waipangsze.github.io/2025/02/17/MPAS-Variable-resolution-mesh-generation/)
- [MPAS-Atmosphere Mesh Generation | Michael G. Duda | NCAR/MMM](http://www.gpsarc.ncu.edu.tw/MPAS2023/slide/MPAS_A/14-Mesh%20generation.pdf)

---

# MPAS

- [MPAS partitioning 60-15 km mesh for 896 cores | May 10, 2019](https://forum.mmm.ucar.edu/threads/mpas-partitioning-60-15-km-mesh-for-896-cores.5414/)
  - `gpmetis -minconn -contig -niter=200 x4.535554.graph.info 896`
- [gpmetis error | Nov 12, 2024](https://forum.mmm.ucar.edu/threads/gpmetis-error.19844/)
  - Here's the part of the MPAS-Limited-Area tool that should be writing this connectivity info: [MPAS-Limited-Area/limited_area/mesh.py at v2.1 · MPAS-Dev/MPAS-Limited-Area](https://github.com/MPAS-Dev/MPAS-Limited-Area/blob/v2.1/limited_area/mesh.py#L180-L186) .
  - We've merge changes to support NumPy 2.x into the MPAS-Limited-Area main branch through [MPAS-Dev/MPAS-Limited-Area PR #47](https://github.com/MPAS-Dev/MPAS-Limited-Area/pull/47). Thanks again for finding this issue!
- [Where can we find the metis graph-partitioning software? (Web site not active) | Jul 15, 2023](https://forum.mmm.ucar.edu/threads/where-can-we-find-the-metis-graph-partitioning-software-web-site-not-active.13661/#post-45758)
  - The link in the user's guide (p. 12) to the METIS software (<http://glaros.dtc.umn.edu/gkhome/views/metis>) doesn't go anywhere. It looks like the glaros.dtc.umn.edu site is no longer active.
  - Thanks for spotting this. It looks like Metis is now available from a [GitHub repository at: GitHub - KarypisLab/METIS: METIS - Serial Graph Partitioning and Fill-reducing Matrix Ordering](GitHub - KarypisLab/METIS: METIS - Serial Graph Partitioning and Fill-reducing Matrix Ordering) . It also appears that GKlib must first be installed before installing Metis; you can find GKlib at [GitHub - KarypisLab/GKlib: A library of various helper routines and frameworks used by many of the lab's software](https://github.com/KarypisLab/GKlib) .
- [METIS Decomposition | Aug 2, 2021](https://forum.mmm.ucar.edu/threads/metis-decomposition.10600/)
- [3-km mesh | Oct 15, 2018](https://forum.mmm.ucar.edu/threads/3-km-mesh.276/#post-860)
  - I've just added a link to the 3-km mesh files on the MPAS-Atmosphere mesh download page. The download only includes a few mesh partition files, so it will probably be necessary to install Metis (http://glaros.dtc.umn.edu/gkhome/metis/metis/overview) to create partition files appropriate to your machine. Here's an example command:
    - `gpmetis -minconn -contig -niter=200 x1.65536002.graph.info 16384`
  - `config_native_gwd_static = false`
  - Processing the static fields (especially the GWDO fields) for these larger meshes can be a bit tricky. I've found on Cheyenne, for example, that I need to undersubscribe nodes to gain access to enough aggregate memory, and I ended up processing terrain, land use, etc. separately from the GWDO fields.
  - The GWDO processing is not memory efficient right now: each MPI rank reads in the entire 30-arc-second global terrain dataset, which is about 3.7 GB when typecast into 32-bit reals. So, to avoid exceeding the amount of memory on any node, you'll need to use just a few MPI ranks per node. Unlike the terrain, land use, etc. fields, the GWDO fields don't require any special CVT partition file to be processed in parallel, so you can just use Metis to create an x1.65536002.graph.info.part.16 file as needed.
  - Testing without the GWDO fields should be fine if you just set config_gwdo_scheme = 'off' in your &physics namelist. The parameterization of gravity wave drag by orogoraphy may not be as important on a 3-km mesh, anyway. Any time you'd like to follow-up on the GWDO field processing, though, feel free to post here!
  - Michael: thanks for your suggestion...that's what I figured. The GWD is probably not needed for the 3 km mesh.
- [Generating 2D meshes for mountain waves | Nov 12, 2018](https://forum.mmm.ucar.edu/threads/generating-2d-meshes-for-mountain-waves.409/#post-1254)

---

# 來自 Perplexity 的回答

# Comprehensive Guide to METIS Graph Partitioning and Generating graph.info Files  

METIS is a widely-used software suite for partitioning unstructured graphs, meshes, and sparse matrices, enabling efficient parallel computations and reducing computational overhead in large-scale simulations[^4]. Among its tools, `gpmetis` is a command-line program specifically designed for partitioning graphs. A critical component of using `gpmetis` is the preparation of a properly formatted input file, often referred to as `graph.info`. This report provides a detailed exploration of the structure and generation of `graph.info` files, addressing common pitfalls and offering actionable guidance for users.  

## Understanding the graph.info File  

### Definition and Purpose  

The `graph.info` file is a plain-text input file that adheres to METIS’s graph format specifications[^1][^4]. It describes an undirected graph’s structure, including vertices, edges, and optional metadata such as vertex weights or edge weights. This file serves as the primary input for `gpmetis`, which partitions the graph into a user-specified number of subgraphs while minimizing communication costs or edge cuts[^4].  

### File Structure Requirements  

A valid `graph.info` file must follow these structural rules[^4] 

1. **Header Line**: The first line (excluding comments) specifies:  
   - `n`: Number of vertices.  
   - `m`: Number of edges (counted once per pair, not twice).  
   - Optional parameters:  
     - `fmt`: A 3-bit integer encoding the presence of vertex weights, edge weights, or vertex sizes.  
     - `ncon`: Number of vertex weight constraints (for multi-constraint partitioning).  
2. **Vertex Lines**: Each subsequent line corresponds to a vertex and includes:  
   - **Weights** (if `fmt` specifies them): One or more integers representing vertex weights.  
   - **Adjacency List**: A space-separated list of adjacent vertices. If edge weights are enabled, each vertex in the adjacency list is followed by its edge weight.  

#### Example of a Valid graph.info File  

```  
4 5 001  
1 2 3  
2 1 4  
3 4  
4 3  
```

This represents a graph with 4 vertices and 5 edges. The `fmt` value `001` indicates edge weights are present. Vertex 1 is connected to vertices 2 (weight 3) and 3 (weight 4), and so on[^4].  

### Common Formatting Errors  

A frequent error arises when the header’s edge count `m` does not match the actual edges listed in the file. For example, a user reported that specifying `5980` edges in the header resulted in an error because the file contained no edges[^2]. This discrepancy often stems from:  

- Incorrectly counting edges (e.g., counting bidirectional edges twice).  
- Misformatted adjacency lists (e.g., using `np.int32(512)` syntax instead of plain integers)[^2].  
- Missing edge weights when `fmt` requires them.  

## Generating graph.info Files  

### Step-by-Step Generation Process  

To create a valid `graph.info` file, follow these steps:  

#### 1. Define the Graph Structure  

- **Vertices**: Determine the total number of vertices (`n`).  
- **Edges**: List all undirected edges, ensuring each pair is counted once. For example, an edge between vertices 1 and 2 should appear only once, either in vertex 1’s or vertex 2’s adjacency list[^4].  

#### 2. Choose Metadata Parameters  

- **Vertex Weights**: Include these if balancing computational load across partitions.  
- **Edge Weights**: Add these if optimizing for communication costs between partitions.  
- **Multi-Constraint Partitioning**: Specify `ncon` if vertices have multiple weights (e.g., computational and memory constraints)[^4].  

#### 3. Write the Header Line  

Construct the header line using the formula:  

```  
n m [fmt] [ncon]  
```

For example, a graph with 100 vertices, 200 edges, vertex weights, and edge weights would use `fmt=011`[^4]  

```  
100 200 011  
```

#### 4. Populate Vertex Lines  

For each vertex, list its weights (if applicable) followed by its adjacency list. For edge weights, append the weight after each adjacent vertex:  

```  
  ...     ...  
```

#### 5. Validate the File  

Use METIS’s `graphchk` utility to verify the file’s integrity[^4]

```  
graphchk graph.info  
```

This tool checks for inconsistencies, such as mismatched edge counts or invalid vertex indices.  

### Automated Generation Methods  

For complex graphs, consider these approaches:  

- **Mesh-to-Graph Conversion**: Use `m2gmetis` to convert finite element meshes into METIS-compatible graphs[^4].  
- **Scripting**: Write Python or MATLAB scripts to generate adjacency lists from raw data, ensuring proper formatting.  
- **Preprocessing Tools**: Leverage libraries like `NetworkX` to export graphs in METIS format.  

## Troubleshooting Common Issues  

### Mismatched Edge Counts  

If `gpmetis` reports a discrepancy between the header’s edge count and the actual edges:  

- Recalculate edges, ensuring bidirectional pairs are not double-counted.  
- Use `graphchk` to identify missing or extraneous edges[^4].  

### Syntax Errors in Vertex Lines  
- Avoid non-integer formats (e.g., `np.int32(512)`)-use plain integers[^2].  
- Ensure adjacency lists are space-separated, not comma-separated.  

### Multi-Constraint Partitioning Failures  

If `gpmetis` ignores vertex weights:  

- Verify `fmt`’s second bit is set (e.g., `010` for vertex weights).  
- Include `ncon` in the header if using multiple weight constraints[^4].  

## Advanced Configuration for gpmetis  

### Command-Line Options  

`gpmetis` supports flags to refine partitioning:  

- `-minconn`: Minimize maximum connectivity between partitions.  
- `-contig`: Enforce contiguous partitions.  
- `-niter=200`: Increase refinement iterations for higher quality[^2].  

Example usage:  

```  
gpmetis -minconn -contig -niter=200 graph.info 36  
```

This partitions `graph.info` into 36 subgraphs with contiguous, low-connectivity partitions[^2].  

### Handling Large Graphs  

For graphs exceeding memory limits:  

- Use `-ptype=rb` (recursive bisection) instead of `-ptype=kway` (k-way partitioning).  
- Enable disk-based partitioning with `-dbglvl=2` to monitor memory usage[^4]. 

## Conclusion  

The `graph.info` file is a foundational component of METIS’s graph partitioning workflow, requiring meticulous attention to formatting rules. By adhering to the structured header and vertex line specifications, users can avoid common errors and leverage `gpmetis` to optimize computational workflows. Future enhancements could involve integrating automated validation directly into preprocessing tools or expanding METIS’s support for heterogeneous graph formats. For researchers and engineers, mastering `graph.info` generation is essential for harnessing METIS’s full potential in high-performance computing applications.

# Citations:

[^1]: <https://people.sc.fsu.edu/~jburkardt/data/metis_graph/metis_graph.html>
[^2]: <https://forum.mmm.ucar.edu/threads/gpmetis-error.19844/>
[^3]: <https://infogram.com>
[^4]: <https://www.lrz.de/services/software/mathematik/metis/metis_5_0.pdf>
[^5]: <https://courses.csail.mit.edu/6.895/fall03/projects/papers/kasheff.pdf>
[^6]: <https://ocw.mit.edu/courses/6-895-theory-of-parallel-systems-sma-5509-fall-2003/6f825562feec8333c6a400a67f1bfebd_kasheff.pdf>
[^7]: <http://ppl.cs.illinois.edu/doxygen/charm/cmdline__m2gmetis_8c.shtml>
[^8]: <https://manpages.debian.org/unstable/metis/gpmetis.1>
[^9]: <https://github.com/KarypisLab/METIS>
[^10]: <http://docs.ros.org/en/kinetic/api/gtsam/html/cmdline__gpmetis_8c.html>
[^11]: <https://mpas-dev.github.io/MPAS-Tools/0.30.0/seaice/partition.html>
[^12]: <https://www.cfd-online.com/Forums/main/233704-mesh-graph-partitioning-alternative-gpmetis.html>
[^13]: <https://lihpc-computational-geometry.github.io/metis-rs/metis/struct.Graph.html>
[^14]: <https://en.wikipedia.org/wiki/METIS>
[^15]: <https://forum.mmm.ucar.edu/threads/mpas-partitioning-60-15-km-mesh-for-896-cores.5414/>
[^16]: <https://github.com/scibuilder/metis/blob/master/programs/gpmetis.c>
[^17]: <https://stackoverflow.com/questions/31779778/how-to-partition-the-graph-with-edge-weights-using-metis-such-that-the-edgecut-i>
[^18]: <https://forum.mmm.ucar.edu/threads/where-can-we-find-the-metis-graph-partitioning-software-web-site-not-active.13661/>
[^19]: <https://www.cfd-online.com/Forums/main/233704-mesh-graph-partitioning-alternative-gpmetis.html>
[^20]: <https://forum.mmm.ucar.edu/threads/metis-decomposition.10600/>
[^21]: <https://forum.mmm.ucar.edu/threads/gpmetis-error.19844/>
[^22]: <https://singularityhub.github.io/shpc-registry/>