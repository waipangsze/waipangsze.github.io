---
layout: post
title: "CFD | OpenFOAM | fluentMeshToFoam .msh to openfoam"
categories: [CFD]
tags: [OpenFOAM, HPC, fluent, mesh]
author: wpsze
date: 2025-01-15 14:45:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# Issue

- [一个可压缩超音速OpeNFOAM算例，想找个fluent大佬来设置一个](https://cfd-china.com/topic/7815/%E4%B8%80%E4%B8%AA%E5%8F%AF%E5%8E%8B%E7%BC%A9%E8%B6%85%E9%9F%B3%E9%80%9Fopenfoam%E7%AE%97%E4%BE%8B-%E6%83%B3%E6%89%BE%E4%B8%AAfluent%E5%A4%A7%E4%BD%AC%E6%9D%A5%E8%AE%BE%E7%BD%AE%E4%B8%80%E4%B8%AA?lang=en-US)
  - 粘度： 0
  - 湍流模型：无
  - 二维算例，所有壁面都可以为滑移，或者零梯度边界
  - 进口边界：静压：1e5、温度：400K、速度：1000m/s
  - 没有其他进口；右边是出口
  - 稳态
  - 网格数量：10000个

{% note primary %}
OpenFOAM计算大约3秒算完。最大压力 810000 。想看一下Fluent的最大压力多少。
{% endnote %}

- [稳态超音速横向射流](http://dyfluid.com/openfoam.html#id2)
  - [rhoSimpleFoam求解超音速横向射流问题](https://cfd-china.com/topic/7064/rhosimplefoam%E6%B1%82%E8%A7%A3%E8%B6%85%E9%9F%B3%E9%80%9F%E6%A8%AA%E5%90%91%E5%B0%84%E6%B5%81%E9%97%AE%E9%A2%98/11)
  - 

![](https://cfd-china.com/assets/uploads/files/1736863312130-%E6%8D%95%E8%8E%B7.jpg)

# Review OpenFOAM

In OpenFOAM, directories play a crucial role in organizing files and data for simulations. Here's an overview of common directories and their meanings:

1. **Case Directory**: This is the main folder for a specific simulation case. It contains all the files related to that simulation.

2. **0 (Zero Directory)**: Contains initial conditions for the simulation. Files here specify the starting values for variables (e.g., velocity, pressure) at time zero.

3. **constant Directory**: Holds files related to the physical properties of the simulation. This includes:
   - **transportProperties**: Defines fluid properties like viscosity and density.
   - **turbulenceProperties**: Specifies turbulence models and parameters.

4. **system Directory**: Contains files that control the simulation execution, including:
   - **controlDict**: Manages the simulation time, output frequency, and solver settings.
   - **fvSchemes**: Defines numerical schemes for discretization.
   - **fvSolution**: Contains solver settings and tolerances.

5. **Post-processing Directories**: After running a simulation, results can be processed and visualized. Directories like `results` or `output` might be used for storing processed data.

6. **Allrun and Allclean Scripts**: Often found in the case directory, these scripts automate the running and cleaning processes for simulations.

Understanding these directories is essential for effectively using OpenFOAM, as each serves a specific purpose in the overall workflow of setting up, running, and analyzing simulations.

# fluentMeshToFoam/fluent3DMeshToFoam

{% fold info @fluentMeshToFoam/fluent3DMeshToFoam %}
```console
Usage: fluentMeshToFoam [OPTIONS] <Fluent mesh file>
options:
  -2D <thickness>   use when converting a 2-D mesh (applied before scale)
  -case <dir>       specify alternate case directory, default is the cwd
  -fileHandler <handler>
                    override the fileHandler
  -libs <(lib1 .. libN)>
                    pre-load libraries
  -noFunctionObjects
                    do not execute functionObjects
  -scale <factor>   geometry scaling factor - default is 1
  -writeSets        write cell zones and patches as sets
  -writeZones       write cell zones as zones
  -srcDoc           display source code in browser
  -doc              display application documentation in browser
  -help             print the usage

Using: OpenFOAM-8 (see https://openfoam.org)
Build: 8


Usage: fluent3DMeshToFoam [OPTIONS] <Fluent mesh file>
options:
  -case <dir>       specify alternate case directory, default is the cwd
  -cubit            special parsing of (incorrect) cubit files
  -fileHandler <handler>
                    override the fileHandler
  -ignoreCellGroups <names>
                    specify cell groups to ignore
  -ignoreFaceGroups <names>
                    specify face groups to ignore
  -includedAngle <angle>
                    feature angle with which to split cyclics
  -libs <(lib1 .. libN)>
                    pre-load libraries
  -noFunctionObjects
                    do not execute functionObjects
  -scale <factor>   geometry scaling factor - default is 1
  -srcDoc           display source code in browser
  -doc              display application documentation in browser
  -help             print the usage

Using: OpenFOAM-8 (see https://openfoam.org)
Build: 8
```
{% endfold %}

Here's how to convert a .msh file to OpenFOAM format using fluentMeshToFoam:

1. Create a new empty directory for your case:
```bash
mkdir supersonic
cd supersonic
```

2. Create the basic OpenFOAM directory structure:
```bash
mkdir constant
mkdir system
```
and prepare all requried files,

3. Copy your .msh file to the case directory:
```bash
$ file supersonic_injet-stead.msh 
supersonic_injet-stead.msh: ASCII text

cp /path/to/your/supersonic_injet-stead.msh .
```

4. Run the conversion tool:
```bash
fluent3DMeshToFoam supersonic_injet-stead.msh
```

{% fold info @fluent3DMeshToFoam results %}
```console
/*---------------------------------------------------------------------------*\
  =========                 |
  \\      /  F ield         | OpenFOAM: The Open Source CFD Toolbox
   \\    /   O peration     | Website:  https://openfoam.org
    \\  /    A nd           | Version:  8
     \\/     M anipulation  |
\*---------------------------------------------------------------------------*/
Build  : 8
Exec   : fluent3DMeshToFoam supersonic_injet-stead.msh
Date   : Jan 15 2025
Time   : 15:10:16
Host   : "xxx"
PID    : 2856052
I/O    : uncollated
Case   : /home/wpsze/openfoam/simulation/supersonic
nProcs : 1
sigFpe : Enabling floating point exception trapping (FOAM_SIGFPE).
fileModificationChecking : Monitoring run-time modified files using timeStampMaster (fileModificationSkew 10)
allowSystemOperations : Allowing user-supplied system call operations

// * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //
Create time

Dimension of grid: 3
Number of points: 20850
Number of cells: 10180
Number of faces: 40964
PointGroup: 1 start: 0 end: 20849.  Reading points...done.
FaceGroup: 2 start: 0 end: 20115.  Reading mixed faces...done.
FaceGroup: 10 start: 20116 end: 20171.  Reading mixed faces...done.
FaceGroup: 11 start: 20172 end: 20173.  Reading mixed faces...done.
FaceGroup: 12 start: 20174 end: 20355.  Reading mixed faces...done.
FaceGroup: 13 start: 20356 end: 20411.  Reading mixed faces...done.
FaceGroup: 14 start: 20412 end: 20591.  Reading mixed faces...done.
FaceGroup: 15 start: 20592 end: 20603.  Reading mixed faces...done.
FaceGroup: 16 start: 20604 end: 40963.  Reading mixed faces...done.
CellGroup: 1 start: 0 end: 10179 type: 1
Zone: 1 name: fluid-1 type: fluid.  Reading zone data...done.
Zone: 2 name: interior-1 type: interior.  Reading zone data...done.
Zone: 10 name: inlet1 type: pressure-outlet.  Reading zone data...done.
Zone: 11 name: inlet2 type: pressure-outlet.  Reading zone data...done.
Zone: 12 name: top type: pressure-outlet.  Reading zone data...done.
Zone: 13 name: outlet type: pressure-outlet.  Reading zone data...done.
Zone: 14 name: bottom type: wall.  Reading zone data...done.
Zone: 15 name: obstacle type: wall.  Reading zone data...done.
Zone: 16 name: frontandback type: pressure-outlet.  Reading zone data...done.

FINISHED LEXING

Creating patch 0 for zone: 10 name: inlet1 type: pressure-outlet
Creating patch 1 for zone: 11 name: inlet2 type: pressure-outlet
Creating patch 2 for zone: 12 name: top type: pressure-outlet
Creating patch 3 for zone: 13 name: outlet type: pressure-outlet
Creating patch 4 for zone: 14 name: bottom type: wall
Creating patch 5 for zone: 15 name: obstacle type: wall
Creating patch 6 for zone: 16 name: frontandback type: pressure-outlet
Creating cellZone 0 name: fluid-1 type: fluid
Creating faceZone 0 name: interior-1 type: interior
faceZone from Fluent indices: 0 to: 20115 type: interior
patch 0 from Fluent indices: 20116 to: 20171 type: pressure-outlet
patch 1 from Fluent indices: 20172 to: 20173 type: pressure-outlet
patch 2 from Fluent indices: 20174 to: 20355 type: pressure-outlet
patch 3 from Fluent indices: 20356 to: 20411 type: pressure-outlet
patch 4 from Fluent indices: 20412 to: 20591 type: wall
patch 5 from Fluent indices: 20592 to: 20603 type: wall
patch 6 from Fluent indices: 20604 to: 40963 type: pressure-outlet

Writing mesh to "constant/region0"

End
```
{% endfold %}

5. Check the mesh quality (optional but recommended):
```bash
checkMesh
```

{% fold info @checkMesh results %}
```console
/*---------------------------------------------------------------------------*\
  =========                 |
  \\      /  F ield         | OpenFOAM: The Open Source CFD Toolbox
   \\    /   O peration     | Website:  https://openfoam.org
    \\  /    A nd           | Version:  8
     \\/     M anipulation  |
\*---------------------------------------------------------------------------*/
Build  : 8
Exec   : checkMesh
Date   : Jan 15 2025
Time   : 22:33:17
Host   : "xxx"
PID    : 2938734
I/O    : uncollated
Case   : /home/wpsze/openfoam/simulation/supersonic
nProcs : 1
sigFpe : Enabling floating point exception trapping (FOAM_SIGFPE).
fileModificationChecking : Monitoring run-time modified files using timeStampMaster (fileModificationSkew 10)
allowSystemOperations : Allowing user-supplied system call operations

// * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //
Create time

Create polyMesh for time = 0

Time = 0

Mesh stats
    points:           20850
    internal points:  0
    faces:            40964
    internal faces:   20116
    cells:            10180
    faces per cell:   6
    boundary patches: 7
    point zones:      0
    face zones:       1
    cell zones:       1

Overall number of cells of each type:
    hexahedra:     10180
    prisms:        0
    wedges:        0
    pyramids:      0
    tet wedges:    0
    tetrahedra:    0
    polyhedra:     0

Checking topology...
    Boundary definition OK.
    Cell to face addressing OK.
    Point usage OK.
    Upper triangular ordering OK.
    Face vertices OK.
    Number of regions: 1 (OK).

Checking patch topology for multiply connected surfaces...
    Patch               Faces    Points   Surface topology                  
    inlet1              56       114      ok (non-closed singly connected)  
    inlet2              2        6        ok (non-closed singly connected)  
    top                 182      366      ok (non-closed singly connected)  
    outlet              56       114      ok (non-closed singly connected)  
    bottom              180      364      ok (non-closed singly connected)  
    obstacle            12       28       ok (non-closed singly connected)  
    frontandback        20360    20850    ok (non-closed singly connected)  

Checking geometry...
    Overall domain bounding box (-1.33333 0 0) (3 1 1)
    Mesh has 3 geometric (non-empty/wedge) directions (1 1 1)
    Mesh has 3 solution (non-empty) directions (1 1 1)
    Boundary openness (2.3102e-17 -4.76257e-17 4.86259e-16) OK.
    Max cell openness = 1.0842e-16 OK.
    Max aspect ratio = 62.3378 OK.
    Minimum face area = 0.00026736. Maximum face area = 0.03.  Face area magnitudes OK.
    Min volume = 0.00026736. Max volume = 0.00054.  Total volume = 4.32833.  Cell volumes OK.
    Mesh non-orthogonality Max: 0 average: 0
    Non-orthogonality check OK.
    Face pyramids OK.
    Max skewness = 5.92121e-14 OK.
    Coupled point location match (average 0) OK.

Mesh OK.

End
```
{% endfold %}

After conversion, you'll have:
- `constant/polyMesh/` directory containing the mesh files
- Basic OpenFOAM mesh files: points, faces, cells, boundary, etc.


# Run

## Test case

- Importing the setup of windAroundBuildings
  - simpleFoam
  
![](https://i.imgur.com/4BxQnOB.gif){width=500}

# References

1. [FluentMeshToFoam | openfoamwiki](https://openfoamwiki.net/index.php/FluentMeshToFoam)
2. [Fluent3DMeshToFoam | openfoamwiki](https://openfoamwiki.net/index.php/Fluent3DMeshToFoam#Comparison_between_fluentMeshToFoam_and_fluent3DMeshToFoam)
   1. 4 Comparison between fluentMeshToFoam and fluent3DMeshToFoam
   2. From the release notes for OpenFOAM 1.4.1:
      1. New fluent3DMeshToFoam converter for 3D meshes from Fluent format to OpenFOAM format including full support for all BCs, zones etc.;
   3. Comparing the source code for fluentMeshToFoam and fluent3DMeshToFoam in OpenFOAM 1.4.1 and 1.5, the following main differences can be found:
      1. fluentMeshToFoam supports 2D and 3D Fluent meshes, while fluent3DMeshToFoam only supports 3D.
      2. fluent3DMeshToFoam deals with hanging nodes.
      3. fluentMeshToFoam has the options to write sets and zones, while fluent3DMeshToFoam has the option to ignore a cell groups and face groups.
      4. fluentMeshToFoam can only handle the following 3D cell types: tet, hex, pyramid, prism
      5. fluent3DMeshToFoam can handle internal walls/patches, or at least it looks like it does. fluentMeshToFoam requires a workaround: Howto importing fluent mesh with internal walls