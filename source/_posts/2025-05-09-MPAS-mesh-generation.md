---
layout: post
title: MPAS | mesh generation
categories: [MPAS]
tags: [NWP, MPAS, WRF, Meteorology, mesh generation, MPAS-Tools, jigsaw, Lloyd iteration]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-05-09 15:24:00
index_img: 
banner_img: 
---

- [**MPAS | Variable-resolution mesh generation**](https://waipangsze.github.io/2025/02/17/MPAS-Variable-resolution-mesh-generation/)
- [MPAS-Atmosphere Mesh Generation | Michael G. Duda | NCAR/MMM](http://www.gpsarc.ncu.edu.tw/MPAS2023/slide/MPAS_A/14-Mesh%20generation.pdf)

---

# Key Points

- **MPAS Mesh**: A grid of hexagonal and pentagonal cells used in the MPAS-Atmosphere model, supporting uniform or variable resolution across the globe.
- **Mesh Generation Tools**: Tools like *MPAS-Tools*, *Jigsaw-GEO*, and *MPAS-Limited-Area Python tool* are commonly used, with NCAR providing additional software.
- **Difficulties**: High computational demands, technical tool issues, and achieving specific mesh properties like symmetry pose challenges.
- **Quasi-Uniform Mesh Script**: Likely involves MPAS-Tools to create a uniform resolution grid, with examples in community resources.
- **Variable-Resolution Mesh Script**: Uses Jigsaw-GEO or MPAS-Tools with a resolution function to define varying cell sizes.

---

# Detailed Report on MPAS-Atmosphere Mesh Generation

## Introduction to MPAS-Atmosphere and Mesh
The Model for Prediction Across Scales (MPAS) is a collaborative project aimed at developing simulation components for atmosphere, ocean, and other earth-system studies, used in climate, regional climate, and weather research. The atmospheric component, MPAS-Atmosphere, relies on a unique grid system known as a mesh to discretize the Earth’s surface. This mesh is critical for enabling simulations that balance computational efficiency with detailed resolution in areas of interest.

## Voronoi Tessellation and C-Grid Discretization  
MPAS employs **Spherical Centroidal Voronoi Tessellations (SCVT)** to construct its unstructured meshes[^1][^7]. Each cell in the mesh corresponds to a Voronoi polygon, where the generating point (or "seed") coincides with the polygon's centroid. This centroidal property ensures numerical stability by aligning mass and momentum variables optimally[^3][^7]. The horizontal discretization uses a **C-grid staggering**, with normal velocity components defined on cell edges and scalar quantities (e.g., temperature, pressure) at cell centers[^3].  

The dual Delaunay triangulation (dashed lines in mesh diagrams) facilitates numerical operators like gradients and curls[^7]. Unlike traditional latitude-longitude grids, MPAS meshes permit smooth resolution transitions-ranging from 3 km to 100 km-**without abrupt nesting boundaries**[^2][^3]. This capability eliminates artifacts common in limited-area models forced by coarse global data. 

An **MPAS mesh** is an unstructured grid composed of **hexagonal and pentagonal cells**, formally known as Spherical Centroidal Voronoi Tessellations (SCVTs). These meshes allow for two primary configurations:

- **Quasi-Uniform Meshes**: Where cell sizes are approximately equal across the globe, suitable for global simulations with consistent resolution (e.g., 120 km, 60 km, 30 km). [^4][^5]
- **Variable-Resolution Meshes**: Where specific regions have higher resolution (e.g., 60-15 km or 92-25 km), ideal for focusing on areas like the Arctic or East Asia while maintaining coarser resolution elsewhere. [^2][^7]
- **Planar meshes**: Cartesian grids for idealized studies, optionally periodic in x/y directions[4][5].  

The SCVT approach ensures that all cells **approximate regular hexagons**, with pentagons/septagons appearing only at resolution transition zones[^3][^7]. 

## Tools

The honeycomb-like structure of the mesh enables MPAS to simulate small-scale weather phenomena (e.g., thunderstorms) in high-resolution areas and large-scale atmospheric flows in low-resolution areas simultaneously. This flexibility is a defining feature of MPAS, developed by the National Center for Atmospheric Research (NCAR) and Los Alamos National Laboratory ([MPAS Overview](https://www.mmm.ucar.edu/models/mpas)).

| **Tool**                     | **Description**                                                                 | **Primary Use**                          |
|------------------------------|--------------------------------------------------------------------------------|------------------------------------------|
| **MPAS-Tools**               | A comprehensive toolset for MPAS, including utilities like `triangle_jigsaw_to_netcdf` for mesh generation. | Generating both quasi-uniform and variable-resolution meshes for atmosphere, ocean, etc. |
| **Jigsaw-GEO**               | A mesh generation tool that supports custom resolution functions to create SCVTs. | Creating variable-resolution meshes with tailored refinement regions. |
| **MPAS-Limited-Area Python Tool** | A Python-based tool for generating regional meshes as subsets of existing global meshes. | Creating limited-area simulations from pre-existing meshes. |
| **NCAR Software**            | Proprietary software provided by NCAR for generating MPAS horizontal meshes, often tailored for specific applications like air quality modeling. | Generating customized meshes for advanced research needs. |

- **MPAS-Tools**: Available on GitHub ([MPAS-Tools GitHub](https://github.com/MPAS-Dev/MPAS-Tools)), this toolset is widely used across MPAS components. For the atmosphere, it supports mesh generation through utilities that convert outputs from other tools (e.g., Jigsaw) into MPAS-compatible formats.
- **Jigsaw-GEO**: This tool is particularly effective for variable-resolution meshes, allowing users to define a resolution function (h-function) that specifies cell sizes across the sphere. It has been used to generate meshes like 60-3km or 60-1km ([Jigsaw-GEO Forum](https://forum.mmm.ucar.edu/threads/mesh-generation-for-mpas-a-by-using-jigsaw-geo.17866/)).
- **MPAS-Limited-Area Python Tool**: This tool is designed for regional simulations, creating meshes as subsets of global meshes. It is supported by documentation provided with MPAS ([MPAS Meshes](https://mpas-dev.github.io/atmosphere/atmosphere_meshes.html)).
- **NCAR Software**: NCAR provides software for generating MPAS meshes, which can be computationally intensive but highly customizable. This software has been used by institutions like the US EPA for air quality modeling ([NCAR Software](https://cfpub.epa.gov/si/si_public_record_Report.cfm?dirEntryId=354703&Lab=CEMM)).

**Community forums, such as the WRF & MPAS-A Support Forum**, often share scripts and examples, like a Python script for generating spherical grids using Jigsaw ([MPAS-PXT Script](https://github.com/pedrospeixoto/MPAS-PXT/blob/master/grids/utilities/jigsaw/spherical_grid.py)).

## Challenges in Mesh Generation
Generating MPAS meshes is a complex process with several challenges:

1. **High Computational Requirements**:
   - Creating highly refined meshes (e.g., 100-1km or 60-3km) involves complex geometric operations, requiring significant computational resources. NCAR notes that generating such meshes can take up to months ([NCAR Software](https://cfpub.epa.gov/si/si_public_record_Report.cfm?dirEntryId=354703&Lab=CEMM)).
   - The EPA’s work on automated mesh generation highlights the need for substantial computational power to handle these operations ([Automated Mesh Generation](https://cfpub.epa.gov/si/si_public_record_report.cfm?Lab=CEMM&dirEntryId=348018)).
   - A 3 km global mesh contains ~65 million cells, requiring:  
     - **Memory**: >1 TB RAM for Lloyd iterations[^7].  
     - **Runtime**: Weeks on multi-core clusters despite parallel Voronoi computations[^7].  
     - **Storage**: NetCDF files exceeding 100 GB for mesh connectivity data[^1][^7].  
2. **Technical Issues with Tools**:
   - Users have reported difficulties with tools like Jigsaw-GEO, including bugs or configuration errors that prevent successful mesh generation ([Jigsaw-GEO Forum](https://forum.mmm.ucar.edu/threads/mesh-generation-for-mpas-a-by-using-jigsaw-geo.17866/)).
   - For example, a user attempting to use MPAS-Tools for atmospheric meshes found it was designed for ocean/sea-ice, requiring adjustments to work with MPAS-Atmosphere ([Refined Mesh Forum](https://forum.mmm.ucar.edu/threads/generate-refiled-mesh-for-mpas-atmosphere.11580/)).
   - Current toolchain gaps include:  
     - No native support for anisotropic refinement (e.g., stretching in jet streams).  
     - Limited parallelization in JIGSAW interface[^7].  
     - Manual tuning of density functions for regional refinement.  
3. **Achieving Specific Mesh Properties**:
   - Users often need meshes with specific characteristics, such as symmetric refinement regions (e.g., a perfect circle instead of an oval). Achieving this can be challenging, as default meshes may not meet these requirements ([MPAS Mesh Generation Forum](https://groups.google.com/g/mpas-atmosphere-help/c/GFDht3Vqjf0)).
   - **Transition zones** between high- and low-resolution areas can exhibit undulations, complicating simulations.
   - **Mesh Quality** Assurance 
     - **Cell distortion**: Non-centroidal cells degrade advection schemes[^3][^7].  
     - **Edge orthogonality**: C-grid requires edges perpendicular to dual Delaunay edges[^7].  
     - **Smooth resolution transitions**: Density functions must avoid sharp gradients to prevent grid imprinting[^7]. 
4. **Automation Complexity**:
   - Automating mesh generation is likened to “herding cats,” indicating the difficulty of streamlining the process due to the need for precise control over resolution and refinement ([Automated Mesh Generation](https://cfpub.epa.gov/si/si_public_record_report.cfm?Lab=CEMM&dirEntryId=348018)).

# MPAS-Tools

- <https://mpas-dev.github.io/MPAS-Tools/0.26.0/mesh_creation.html>
- <https://github.com/MPAS-Dev/MPAS-Tools>
  - The latest documentation for the **conda package** **mpas-tools** can be found here
  - <https://github.com/MPAS-Dev/MPAS-Tools/blob/master/conda_package/dev-spec.txt>

# ** Installing MPAS-Tools

```sh
#  Based on 
#  https://mpas-dev.github.io/MPAS-Tools/0.26.0/mesh_creation.html
#  and Jigsaw scripts: https://github.com/dengwirda/jigsaw-python/tree/master/tests
#  
# Pre-requisites:
# 0) Install the conda enviroment MPAS-Tools
# 1) Get the requirements https://github.com/MPAS-Dev/MPAS-Tools/blob/master/conda_package/dev-spec.txt
# 2) Create enviroment
#     $ micromamba config --add channels conda-forge
#     $ micromamba env create --name mpas-tools --file dev-spec.txt (not used)
#     $ micromamba env create --name mpas-tools --file dev-spec.yml
# 3) Install the mpas-tools pack
#     $ micromamba install conda-forge::mpas_tools
# 4) Use it with $ micromamba activate mpas-tools
#
# - To install jigsaw use conda https://github.com/dengwirda/jigsaw-geo-python/ )
# 5) $ micromamba install conda-forge::jigsaw
# 6) $ micromamba install conda-forge::jigsawpy
```

{% fold info @dev-spec.yml  %}
```yml 
name: mpas-tools
channels:
- anaconda
- conda-forge
dependencies:
- python>=3.9
- cartopy
- cmocean
- dask
- geometric_features>=1.0.1,<2.0.0
- h5py
- hdf5
- inpoly
- libnetcdf
- matplotlib-base>=3.9.0
- netcdf4
- networkx
- numpy>=2.0,<3.0
- progressbar2
- pyamg
- pyevtk
- pyproj
- python-igraph
- scikit-image!=0.20.0
- scipy
- shapely>=2.0,<3.0
- tqdm
- xarray
- flynt
- pip
- pre-commit
- pytest
- ruff
- setuptools
- sphinx
- mock
- sphinx_rtd_theme
```
{% endfold %}

## Core Utilities in MPAS-Tools  
The **MPAS-Tools** repository provides essential mesh manipulation utilities:  

### Planar Mesh Generation  
The `planar_hex` tool generates uniform hexagonal meshes for idealized studies:  

```bash  
planar_hex --nx=100 --ny=50 --dc=5000. --npx --npy --outFileName=mesh_5km.nc  
```

This creates a 5 km-resolution mesh spanning 100×50 grid cells, non-periodic in both directions[^4][^5]. The `dc` parameter specifies cell center-to-center distance. For periodic domains (e.g., channel flows), omit `--npx`/`--npy`.  

### Spherical Mesh Construction  

The `mpas_tools.mesh.creation.build_mesh module` is used create an MPAS mesh using the **JIGSAW and JIGSAW-Python (jigsawpy) packages**.

High-resolution global meshes require **JIGSAW**, a *computational geometry package integrated with MPAS-Tools*. A typical workflow involves:  

1. Defining a **density function** controlling regional resolution[^7].  
2. Generating initial seeds via Monte Carlo sampling[^7].  
3. Iterative Lloyd relaxation to optimize cell centroids[^7].  

- Here is a simple example script for creating a uniform MPAS mesh with 240-km resolution:
  - Spherical meshes are constructed with the function `mpas_tools.mesh.creation.build_mesh.build_spherical_mesh()`. The user provides a 2D array `cellWidth` of cell sizes in kilometers along 1D arrays for the longitude and latitude (the cell widths must be on a lon/lat tensor grid) and the radius of the earth in meters.
  - The result is an MPAS mesh file, called `base_mesh.nc` by default, as well as several intermediate files: `mesh.log`, `mesh-HFUN.msh`, `mesh.jig`, `mesh-MESH.msh`, `mesh.msh`, and `mesh_triangles.nc`.

```python  
#!/usr/bin/env python
import numpy as np
from mpas_tools.ocean import build_spherical_mesh


def cellWidthVsLatLon():
    """
    Create cell width array for this mesh on a regular latitude-longitude grid.
    Returns
    -------
    cellWidth : ndarray
        m x n array of cell width in km
    lon : ndarray
        longitude in degrees (length n and between -180 and 180)
    lat : ndarray
        longitude in degrees (length m and between -90 and 90)
    """
    dlat = 10
    dlon = 10
    constantCellWidth = 240

    nlat = int(180/dlat) + 1
    nlon = int(360/dlon) + 1

    lat = np.linspace(-90., 90., nlat)
    lon = np.linspace(-180., 180., nlon)

    cellWidth = constantCellWidth * np.ones((lat.size, lon.size))
    return cellWidth, lon, lat


def main():
    cellWidth, lon, lat = cellWidthVsLatLon()
    build_spherical_mesh(cellWidth, lon, lat, out_filename='base_mesh.nc')


if __name__ == '__main__':
    main() 
```

```console
 $ ncdump -h base_mesh.nc 
netcdf base_mesh {
dimensions:
	nCells = 10383 ;
	nEdges = 31143 ;
	nVertices = 20762 ;
	maxEdges = 8 ;
	maxEdges2 = 16 ;
	TWO = 2 ;
	vertexDegree = 3 ;
	Time = UNLIMITED ; // (0 currently)
variables:
	double latCell(nCells) ;
		latCell:long_name = "latitudes of cell centres" ;
	double lonCell(nCells) ;
		lonCell:long_name = "longitudes of cell centres" ;
...
```

### Mesh Conversion and Validation  

Post-processing tools ensure MPAS compatibility:  

- **MpasCellCuller.x**: Removes edge cells for non-periodic boundaries[^4].  
- **MpasMeshConverter.x**: **Converts intermediate formats to NetCDF**[^6]. 

## Incremental Mesh Refinement  

For high-resolution meshes (>10 million cells), MPAS uses an incremental bisection approach[^7]:  

1. Start with ~100 seeds for a coarse mesh ($2^n$ × target resolution).  
2. Perform Lloyd iterations to converge centroid positions.  
3. Bisect each cell edge, doubling resolution.  
4. Repeat until achieving target resolution.  

This method reduces convergence time **from months to days** compared to direct high-res generation[^7]. 

# Script for Generating a Quasi-Uniform Mesh

## Sample 1

A **quasi-uniform mesh** has approximately equal cell sizes across the globe, making it suitable for global simulations where consistent resolution is needed. To create a script for generating such a mesh:

- **Tool Choice**: MPAS-Tools is the primary tool, as it supports generating SCVT meshes. The `triangle_jigsaw_to_netcdf` utility can convert mesh outputs into MPAS-compatible formats.
- **Process**:
  1. Install MPAS-Tools from the GitHub repository ([MPAS-Tools GitHub](https://github.com/MPAS-Dev/MPAS-Tools)).
  2. Configure the tool to generate an SCVT with a constant resolution function (e.g., a fixed cell size of 60 km).
  3. Run the mesh generation process to produce a `mesh.nc` file, which includes the SCVT mesh, connectivity (graph.info), and partitionings for MPI tasks.
  4. Validate the mesh using MPAS-Atmosphere’s initialization process to ensure compatibility.

- **Example Script**:
  The MPAS community provides pre-generated quasi-uniform meshes (e.g., 120 km, 60 km, 30 km) as references ([MPAS Meshes](https://mpas-dev.github.io/atmosphere/atmosphere_meshes.html)). A Python script using MPAS-Tools and Jigsaw, shared in a forum, can serve as a starting point ([MPAS-PXT Script](https://github.com/pedrospeixoto/MPAS-PXT/blob/master/grids/utilities/jigsaw/spherical_grid.py)).

```python
import numpy as np
from mpas_tools.mesh.creation import build_spherical_mesh

# Define parameters for quasi-uniform mesh
resolution = 60e3  # 60 km resolution in meters
radius = 6371e3   # Earth's radius in meters

# Generate SCVT mesh with constant resolution
mesh = build_spherical_mesh(radius=radius, resolution=resolution, uniform=True)

# Save mesh to NetCDF file
mesh.to_netcdf("quasi_uniform_mesh.nc")

print("Quasi-uniform mesh generated successfully.")
```

**Note**: This is a simplified example. Actual implementation requires **installing MPAS-Tools** and following its documentation for specific configurations. Community forums and the MPAS-Tools repository provide detailed guidance.

## Sample 2

### Workflow Design  

A robust pipeline should:  

1. Generate initial mesh.  
2. Cull edge cells (if non-periodic).  
3. Validate mesh topology.  
4. Partition for parallel simulation.  

### Example Bash Script  

```bash  
#!/bin/bash  
# Generate 50 km uniform global mesh  
planar_hex --nx=360 --ny=180 --dc=50000. --outFileName=raw_mesh.nc  
MpasCellCuller.x raw_mesh.nc culled_mesh.nc  
MpasMeshConverter.x culled_mesh.nc uniform_50km.nc  

# Partition for 1024 cores  
gpmetis -minconn -contig -niter=1000 -objtype=cut uniform_50km.nc.graph.info 1024  
```

### Python Automation  
Leverage MPAS-Tools' Python API for programmatic control:  

```python  
from mpas_tools.planar_hex import make_planar_hex_mesh  
from mpas_tools.mesh.conversion import cull, convert  

ds_mesh = make_planar_hex_mesh(nx=360, ny=180, dc=50e3, nonperiodic_x=True, nonperiodic_y=True)  
ds_mesh = cull(ds_mesh)  
ds_mesh = convert(ds_mesh)  
ds_mesh.to_netcdf("uniform_50km.nc")  
```

### Hands-on

- `ds_mesh = make_planar_hex_mesh(nx=360, ny=180, dc=50e3, nonperiodic_x=True, nonperiodic_y=True)  `
  - **output = "uniform_50km.nc"**
- Don't use python automation because no **graph.info** is generated
- Q: Difference between CLI and python?
  - `-rw-rw-r-- 1 wpsze wpsze 69M May  9 16:39 python_uniform_50km.nc`
  - `-rw-rw-r-- 1 wpsze wpsze 68M May  9 17:42 uniform_50km.nc`

{% fold info @processing %}
```console
 $ python test.py 
xxx
```
{% endfold %}

generated output file **uniform_50km.nc**,

```
 $ ncdump -h uniform_50km.nc 
xxxx
```

- bug
  - *EXCEPTION (wxImagePanel.cpp, Line 640) X (longitude) dimension has nonpositive range [0.000000,0.000000]*
  - 


# Script for Generating a Variable-Resolution Mesh

A **variable-resolution mesh** allows higher resolution in specific regions (e.g., 60-15 km, where 15 km cells are used in a refined area). To create a script for generating such a mesh:

- **Tool Choice**: Jigsaw-GEO is ideal for defining custom resolution functions, with MPAS-Tools used to convert outputs to MPAS format.
- **Process**:
  1. **Install Jigsaw-GEO and MPAS-Tools**.
  2. Define a **resolution function (h-function)** that specifies cell sizes (e.g., 15 km in a refined region centered at 0° latitude, 0° longitude, and 60 km elsewhere).
  3. Use Jigsaw-GEO to generate the mesh based on the resolution function.
  4. Convert the output to MPAS format using MPAS-Tools’ `triangle_jigsaw_to_netcdf` utility.
  5. Validate the mesh with MPAS-Atmosphere.

- **Example Resolution Function**:
  A user on the WRF & MPAS-A Support Forum shared an h-function for a 60-3km mesh:
  ```python
  hfun.value = res_out - (res_out - res_in) * np.exp(-(extend * (xmat + (-ctr_lon)/180 * np.pi) ** 2 + ...)
  ```
  This function defines a smooth transition from high to low resolution ([Jigsaw-GEO Forum](https://forum.mmm.ucar.edu/threads/mesh-generation-for-mpas-a-by-using-jigsaw-geo.17866/)).

```python
import numpy as np
from mpas_tools.mesh.creation import build_spherical_mesh

# Define parameters for variable-resolution mesh
res_out = 60e3   # 60 km resolution outside refined region
res_in = 15e3    # 15 km resolution in refined region
radius = 6371e3  # Earth's radius in meters
ctr_lat = 0.0    # Center latitude of refined region (degrees)
ctr_lon = 0.0    # Center longitude of refined region (degrees)
extend = 10.0    # Controls transition zone width

# Define resolution function
def h_function(lat, lon):
    dist = np.sqrt((lat - ctr_lat)**2 + (lon - ctr_lon)**2)
    return res_out - (res_out - res_in) * np.exp(-extend * dist**2)

# Generate SCVT mesh with variable resolution
mesh = build_spherical_mesh(radius=radius, h_function=h_function)

# Save mesh to NetCDF file
mesh.to_netcdf("variable_resolution_mesh.nc")

print("Variable-resolution mesh generated successfully.")
```

**Note**: This is a conceptual example. Implementing it requires Jigsaw-GEO and MPAS-Tools, with the resolution function tailored to your specific needs. Refer to MPAS documentation and community examples for precise configurations.

## Generating Variable-Resolution Meshes via Density Functions  

### Defining Regional Refinement  

A density function ρ(λ,φ) controls resolution, where ρ ∝ 1/dx⁴[^7]. For example, to refine over the Andes:  

```python  
import numpy as np  

def andes_refinement(lon, lat):  
    # Base resolution: 100 km  
    base_res = 100e3  
    # Refine to 30 km over Andes (70°W-65°W, 20°S-10°N)  
    if (-70 <= lon <= -65) and (-20 <= lat <= 10):  
        return (base_res / 30e3)**4  
    return 1.0  
```

### Mesh Generation Script  

```bash  
# Generate seeds with density function  
jigsaw --opt=1 --rho=andes_refinement.py --out=seeds.msh  

# Convert to MPAS format  
jigsaw_to_netcdf seeds.msh andes_mesh.nc on_sphere=True sphere_radius=6371e3  

# Partition mesh  
gpmetis -minconn -contig -niter=1000 andes_mesh.nc.graph.info 4096  
```

### Incremental Refinement Workflow  

For a 15 -> 3 km mesh:  

```python  
from mpas_tools.mesh.incremental import refine_mesh  

refine_mesh(  
    initial_mesh="coarse_15km.nc",  
    levels=3,          # 15 → 7.5 → 3.75 → 1.875 km  
    density_func=topography_based_rho,  
    output="final_3km.nc"  
)  
```

# geojson file

- <https://mpas-dev.github.io/MPAS-Tools/0.26.0/mesh_creation.html#signed-distance-functions>

# Conclusion  

MPAS's unstructured mesh framework overcomes limitations of traditional climate model grids but introduces complex toolchain dependencies. While quasi-uniform meshes are straightforward with `planar_hex`, **variable-resolution generation demands careful density function design and computational resources**. Key challenges persist in parallel scalability and mesh quality automation. Future development should prioritize GPU-accelerated Lloyd iterations and machine learning-based density optimization to democratize high-resolution climate modeling.  

The provided scripts offer starting points, but production-grade implementation requires integrating HPC scheduling, mesh validation suites, and performance profiling-a testament to the intricate interplay between computational geometry and climate system modeling.

# Additional Resources

- **MPAS Documentation**: The official MPAS-Atmosphere website provides mesh downloads and documentation ([MPAS Meshes](https://mpas-dev.github.io/atmosphere/atmosphere_meshes.html)).
- **Community Forums**: The WRF & MPAS-A Support Forum and Google Groups offer scripts, troubleshooting tips, and user experiences ([MPAS Help Group](https://groups.google.com/g/mpas-atmosphere-help)).
- **GitHub Repositories**: The MPAS-Tools and MPAS-PXT repositories contain scripts and utilities for mesh generation ([MPAS-Tools GitHub](https://github.com/MPAS-Dev/MPAS-Tools), [MPAS-PXT Script](https://github.com/pedrospeixoto/MPAS-PXT/blob/master/grids/utilities/jigsaw/spherical_grid.py)).
- [Generating Refined Mesh for MPAS-Atmosphere](https://forum.mmm.ucar.edu/threads/generate-refiled-mesh-for-mpas-atmosphere.11580/)
- [MPAS-Tools GitHub Repository](https://github.com/MPAS-Dev/MPAS-Tools)
- [MPAS-PXT Spherical Grid Script](https://github.com/pedrospeixoto/MPAS-PXT/blob/master/grids/utilities/jigsaw/spherical_grid.py)

# Citations:

[^1]: <https://mpas-dev.github.io/atmosphere/atmosphere_meshes.html>
[^2]: <https://gmd.copernicus.org/articles/9/77/2016/>
[^3]: <https://mpas-dev.github.io/atmosphere/atmosphere.html>
[^4]: <https://mpas-dev.github.io/MPAS-Tools/0.26.0/mesh_creation.html>
[^5]: <https://mpas-dev.github.io/MPAS-Tools/0.30.0/mesh_creation.html>
[^6]: <https://mpas-dev.github.io/MPAS-Tools/0.35.0/generated/mpas_tools.mesh.creation.jigsaw_to_netcdf.jigsaw_to_netcdf.html>
[^7]: <http://www.gpsarc.ncu.edu.tw/MPAS2023/slide/MPAS_A/14-Mesh%20generation.pdf>
[^8]: <https://mpas-dev.github.io>
[^9]: <https://forum.mmm.ucar.edu/threads/generating-mesh-using-mpas-mesh_tools.291/>
[^10]: <https://mpas-dev.github.io/MPAS-Tools/0.34.1/mesh_conversion.html>
[^11]: <http://www.gpsarc.ncu.edu.tw/MPAS2023/slide/MPAS_A/7-Mesh%20structure.pdf>
[^12]: <https://cfpub.epa.gov/si/si_public_record_Report.cfm?dirEntryId=354703&Lab=CEMM>
[^13]: <https://github.com/marta-gil/vtx-mpas-meshes>
[^14]: <https://www.ecmwf.int/sites/default/files/elibrary/2012/14043-global-nonhydrostatic-atmospheric-model-mpas-preliminary-results-uniform-and-variable.pdf>
[^15]: <https://e3sm.org/wp-content/uploads/2020/06/MPAS_Mesh_Generation_Petersen_200520.pdf>
[^16]: <https://ncar.ucar.edu/what-we-offer/models/model-prediction-across-scales-mpas>
[^17]: <https://www.newton.ac.uk/files/seminar/20120928113512001-153372.pdf>
[^18]: <http://www.gpsarc.ncu.edu.tw/MPAS2023/slide/MPAS_A/14-Mesh%20generation.pdf>
[^19]: <https://backend.orbit.dtu.dk/ws/portalfiles/portal/263548830/adgeo_56_77_2021.pdf>
[^20]: <https://jointcenterforsatellitedataassimilation-jedi-docs.readthedocs-hosted.com/en/1.2.0/inside/jedi-components/mpas-jedi/introduction.html>
[^21]: <https://mpas-dev.github.io/MPAS-Tools/0.31.0/mesh_creation.html>
[^22]: <https://forum.mmm.ucar.edu/threads/generating-mesh-using-mpas-mesh_tools.291/>
