---
layout: post
title: "CFD | Gmsh | meshing"
categories: [CFD]
tags: [Gmsh, meshing, OpenFOAM, HPC]
author: wpsze
date: 2026-04-01 06:36:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/13E7aLb.png
banner_img: https://i.imgur.com/13E7aLb.png
---

# Gmsh

Gmsh is an open source 3D finite element mesh generator with a built-in CAD engine and post-processor. Its design goal is to provide a fast, light and user-friendly meshing tool with parametric input and flexible visualization capabilities. 

- <https://gmsh.info/>

Gmsh is built around four modules

- geometry, 
- mesh, 
- solver and 
- post-processing

# Examples

## 2D cylinder

{% fold info @cylinder2d.geo %}
```txt
//+
Point(1) = {0, 0, 0, 1.0};
//+
Point(2) = {-2.5, 0.1, 0, 1.0};
//+
Recursive Delete {
  Point{2}; 
}
//+
Point(2) = {6, 0, 0, 1.0};
//+
Point(3) = {6, 4, 0, 1.0};
//+
Point(4) = {0, 4, 0, 1.0};
//+
Point(5) = {1.5, 2, 0, 1.0};
//+
Point(6) = {1.5, 1.5, 0, 1.0};
//+
Point(7) = {1.5, 2.5, 0, 1.0};
//+
Point(8) = {1, 2, 0, 1.0};
//+
Point(9) = {2, 2, 0, 1.0};
//+
Line(1) = {1, 2};
//+
Line(2) = {2, 3};
//+
Line(3) = {3, 4};
//+
Line(4) = {4, 1};
//+
Circle(5) = {7, 5, 8};
//+
Circle(6) = {8, 5, 6};
//+
Circle(7) = {6, 5, 9};
//+
Circle(8) = {9, 5, 7};
//+
Curve Loop(1) = {3, 4, 1, 2};
//+
Curve Loop(2) = {8, 5, 6, 7};
//+
Plane Surface(1) = {1, 2};
//+
Extrude {0, 0, 0.1} {
  Surface{1}; Layers {1}; Recombine;
}
//+
Physical Surface("outlet", 51) = {33};
//+
Physical Surface("inlet", 52) = {25};
//+
Physical Surface("wall", 54) = {45, 41, 37, 49};
//+
Physical Volume("internal", 55) = {1};
//+
Physical Surface("top", 56) = {21};
//+
Physical Surface("bottom", 57) = {29};
//+
Physical Surface("frontandback", 58) = {50, 1};
```
{% endfold %}

# Step by Step

{% gi 4 2-2 %}
![](https://i.imgur.com/GP2mjM2.png)
![](https://i.imgur.com/W9lC2aM.png)
![](https://i.imgur.com/DgBT2Gf.png)
![](https://i.imgur.com/gxqBF38.png)
{% endgi %}

<iframe width="560" height="315" src="https://www.youtube.com/embed/iAM6Yfhb05c" title="Gmsh &amp; OpenFoam &amp; ParaView to create and visualize the flow around cylinder simulation from scratch" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/3K7J8cpD3_8" title="Demo - OpenFoam - External flow past a 2D circular cylinder - icoFoam. Geometry and meshing in Gmsh." frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 2D case: cylinder by simpleFoam

![](https://i.imgur.com/ouIKxrn.jpeg)

# Edit constant/polyMesh/boundary

- For 2D case,

{% fold info @constant/polyMesh/boundary %}
```txt
/*--------------------------------*- C++ -*----------------------------------*\
  =========                 |
  \\      /  F ield         | OpenFOAM: The Open Source CFD Toolbox
   \\    /   O peration     | Website:  https://openfoam.org
    \\  /    A nd           | Version:  8
     \\/     M anipulation  |
\*---------------------------------------------------------------------------*/
FoamFile
{
    version     2.0;
    format      ascii;
    class       polyBoundaryMesh;
    location    "constant/polyMesh";
    object      boundary;
}
// * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //

6
(
    frontandback
    {
        type            empty;
        physicalType    patch;
        nFaces          2816;
        startFace       2054;
    }
    top
    {
        type            symmetryPlane;
        physicalType    patch;
        nFaces          30;
        startFace       4870;
    }
    inlet
    {
        type            patch;
        physicalType    patch;
        nFaces          20;
        startFace       4900;
    }
    bottom
    {
        type            symmetryPlane;
        physicalType    patch;
        nFaces          30;
        startFace       4920;
    }
    outlet
    {
        type            patch;
        physicalType    patch;
        nFaces          20;
        startFace       4950;
    }
    wall
    {
        type            wall;
        physicalType    patch;
        nFaces          16;
        startFace       4970;
    }
)

// ************************************************************************* //
```
{% endfold %}

# Run OpenFOAM

## gmshToFoam

```console
gmshToFoam cylinder.msh
```

then, copy `momentumTransport` and `transportProperties` to `constant/` folder.

## execute solver

- **No `blockMesh`**

```sh
runApplication decomposePar -copyZero -force #  -force option 

runParallel checkMesh
runParallel simpleFoam
```

# Older OpenFOAM --> Gmsh 2.x format

{% note primary %}
**Older OpenFOAM** `gmshToFoam` only supports the classic **Gmsh 2.x format**.
{% endnote %}

- Newer Gmsh files contain sections like `$Entities`, binary data, or different element ordering that confuse the parser.

## How to Fix It

If you are using `Gmsh` → `OpenFOAM` (most frequent case):

- Open the mesh file in a text editor and look at the very beginning. It should start with something like `$MeshFormat` (for `Gmsh`) or clear `ASCII` headers. If it looks binary or has strange characters → wrong format.

```txt
$MeshFormat
2.2 0 8
```

- In `Gmsh`, go to File → Export and choose:
  - Format: `Gmsh 2.2` (or "`Gmsh 2 ASCII`") — not the default "Gmsh" or "Gmsh Unstructured".
  - Uncheck "Save all elements" if you have physical groups.
- If you are on a newer `OpenFOAM` version (`v2312`, `v2406`, etc.), `gmshToFoam` has improved **but still often fails** with very recent Gmsh. Downgrading the export format is safest.
- Using the **Gmsh GUI** (easiest)
  - **Finish meshing your geometry (Mesh → 2D / 3D as needed)**
  - **OpenFOAM must be 3D even your case is 2D !!!**
  - Go to `File` → `Export`.
  - In the `file` dialog:
    - Choose file type: `Gmsh mesh (*.msh)` (or just type a name ending with .msh).
    - Click the `Options` button (or look at the bottom of the dialog).
    - In the `export` options:
      - `Select Version 2 ASCII` (sometimes shown as “MSH 2 ASCII” or “Version 2.2”).
      - **Uncheck “Save all elements”** (important — otherwise gmshToFoam may complain about unhandled element types like points/lines).
      - **Uncheck “Save parametric coordinates”** (recommended).

# OpenFOAM (via gmshToFoam) only accepts 3D volume meshes

- OpenFOAM (via gmshToFoam) only accepts 3D volume meshes.

## How to fix it (for Gmsh 4.15)

### Method 1: Force 3D meshing in Gmsh GUI (recommended)

- Open your geometry in Gmsh.
  - Make sure you have defined Physical Volumes (not only Physical Surfaces).
  - **Go to Geometry → Physical Groups → Add → Volume and select your volume(s)**.
- In the Mesh module:
  - **Click Mesh → 3D (this is crucial — do not click only 2D)**.
- After meshing finishes, go to File → Export.
  - Choose Gmsh mesh (*.msh).
  - Click Options and set:
  - Version: 2 ASCII (or 2.2)
  - Uncheck "Save all elements"
  - Uncheck “Save parametric coordinates”
  - Export as cylinder.msh.

### Method 3: Add this at the end of your .geo file

- `Edit script`

```.geo
// Force 3D mesh and legacy format
Mesh 3;
Mesh.MshFileVersion = 2.2;
Physical Volume("fluid") = {1};   // change "1" to your volume tag if needed
Save "cylinder.msh";
```

then, `Reload script`.

# gmshToFoam issue

```log
Starting to read physical names at line 5
Physical names:7
    Surface 51	outlet
    Surface 52	inlet
    Surface 54	wall
    Surface 56	top
    Surface 57	bottom
    Surface 58	frontandback
    Volume 55	internal

Starting to read points at line 15
Vertices to be read:1524
Vertices read:1524

Starting to read cells at line 1542
Cells to be read:4340

Mapping region 58 to Foam patch 0
Mapping region 56 to Foam patch 1
Mapping region 52 to Foam patch 2
Mapping region 57 to Foam patch 3
Mapping region 51 to Foam patch 4
Mapping region 54 to Foam patch 5
Mapping region 55 to Foam cellZone 0
Cells:
    total:1408
    hex  :0
    prism:1408
    pyr  :0
    tet  :0

CellZones:
Zone	Size
    0	1408

Skipping tag  at line 5885
Patch 0 gets name frontandback
Patch 1 gets name top
Patch 2 gets name inlet
Patch 3 gets name bottom
Patch 4 gets name outlet
Patch 5 gets name wall

--> FOAM Warning : 
    From function Foam::polyMesh::polyMesh(const Foam::IOobject &, Foam::Field<Foam::Vector<double>> &&, const Foam::List<Foam::cellShape> &, const Foam::List<Foam::List<Foam::face>> &, const Foam::List<Foam::word> &, const Foam::List<Foam::word> &, const Foam::word &, const Foam::word &, const Foam::List<Foam::word> &, bool)
    in file meshes/polyMesh/polyMeshFromShapeMesh.C at line 595
    Found 2932 undefined faces in mesh; adding to default patch.
Finding faces of patch 0
Finding faces of patch 1
Finding faces of patch 2
Finding faces of patch 3
Finding faces of patch 4
Finding faces of patch 5
```

- FOAM Warning
  - can be ignored. As gmshToFoam first adds all boundary faces to defaultFaces patch and then move faces to patches defined in MSH file. IIRC I've already posted explanation on the forum, or you can take a look at sources of gmshToFoam. [link](https://www.cfd-online.com/Forums/openfoam-meshing/145192-gmshtofoam-found-undefined-faces-mesh.html)
  - Not necessarily wrong. What this says is that OpenFOAM has found 824 faces external to your geometry that did not have a boundary condition associated with them and it has collected them together in a patch.If you think you have defined boundary types on the complete external surface of the model, there is a reason to worry; otherwise, just have a look at the default patch and see if it looks OK or not.[link](https://www.cfd-online.com/Forums/openfoam-meshing/61585-undefined-faces-mesh.html)
- check `constant/polyMesh/boundary`

# solver: simpleFoam

## Error

### Attempt to cast type patch to type symmetryPlane

- This happens because one (or more) of your boundary patches is defined as type `symmetryPlane`; in `0/U` or `0/p `(or other field files), but in the mesh file `constant/polyMesh/boundary` its actual type is just `patch`.
- Root Cause (Very Common with gmshToFoam)
  - In your earlier `gmshToFoam` output you had patches: frontandback, top, inlet, bottom, outlet, wall.
  - You probably have frontandback (or similar) intended as symmetry planes (common for 3D cylinder or wedge simulations to reduce the domain).
  - **However, `gmshToFoam` creates all unnamed or leftover faces as type `patch`;.**
  - When you (or the tutorial) set type `symmetryPlane`; **in the boundary conditions, it mismatches the mesh definition.**

```console
vim constant/polyMesh/boundary
```

- Please run these and share the output:

```sh
cat constant/polyMesh/boundary | grep -A 20 "frontandback"
cat 0/U | grep -A 10 "frontandback"
cat 0/p | grep -A 10 "frontandback"
checkMesh
```

```txt
frontandback
{
    type            patch;      // <--- this is the problem
    nFaces          XXX;
    startFace       YYY;
}
```

- Edit `constant/polyMesh/boundary` and change type `patch`; to type `symmetryPlane`; for the symmetry patches (usually frontandback).

```txt
frontandback
{
    type            symmetryPlane;
    nFaces          XXX;
    startFace       YYY;
}
```

- Do not change `inlet`, `outlet`, `wall`, `top`, `bottom` — those should stay as `patch` or `wall`.
- **Make boundary conditions consistent in the `0/` folder
In `0/U` and `0/p`, set the same patches to `symmetryPlane`:**

```txt
frontandback
{
    type            symmetryPlane;
}
```

then,

```console
rm -rf 0.* processor*   # if you ran in parallel before
simpleFoam          # or whatever solver you're using
```

### Cannot find patchField entry for frontandback

- **`frontandback` is `empty` for 2D case.**

### Patch type for patch wall must be wall

- You are using a wall function for nut (turbulent viscosity) on the patch named "wall" (very common with kEpsilon model), but in the mesh file constant/polyMesh/boundary, this patch is still defined as:

```txt
type            patch;     // ← Wrong for wall functions
```

{% note primary %}
`OpenFOAM` requires the patch type to be `wall` when you use **any wall function** (`nutkWallFunction`, `nutUWallFunction`, `kqRWallFunction`, etc.).
{% endnote %}

- Open this file:
  - `constant/polyMesh/boundary`
- Find the wall entry and change it from `patch` to `wall`:

```txt
wall
{
    type            wall;
    nFaces          XXXX;
    startFace       YYYY;
}
```

- **Do not change the other patches unless needed:**
  - `inlet`, `outlet`, `top`, `bottom` → keep as `patch`
  - `frontandback` → should be `symmetryPlane` (from previous fix)
- Make sure your boundary conditions are correct
  - In `0/nut` (this is usually where the error is triggered):
    - ```
      wall
      {
          type            nutkWallFunction;   // or nutUWallFunction
          value           uniform 0;
      }
      ```
  - In `0/k` and `0/epsilon` on the wall patch you usually have:
    - ```
      wall
      {
          type            kqRWallFunction;     // or fixedValue + low Re
          value           uniform 0;
      }
      ```
#### low-Reynolds simulation

Alternative (if you prefer not to use wall functions)
If you want a low-Reynolds simulation instead (no wall functions):

- Change wall type back to patch in boundary file, or
  - In `0/nut` use:
    - ```
      wall
      {
          type            calculated;
          value           uniform 0;
      }
      ```
  - Use `lowRe` settings for k and epsilon (fixedValue with small values near wall).

{% note primary %}
But for most industrial cylinder/pipe flows, **wall functions (nutkWallFunction) + type wall**; is the standard and recommended way.
{% endnote %}

### What is `physicalType patch;`?

- `physicalType` is an old, optional entry that `gmshToFoam` (and some other mesh converters) adds for backward compatibility with very old OpenFOAM GUIs (FoamX).
- It has almost no effect on the actual simulation in modern `OpenFOAM` (v8, v9, v10, v11, etc.).
- The important entry is type (e.g. type wall;, type symmetryPlane;, type patch;, type empty;).
- You can safely leave physicalType patch; as it is, or comment it out with `//` if you want a cleaner file. Deleting the whole line is also fine in most cases.

```txt
FoamFile
{
    version     2.0;
    format      ascii;
    class       polyBoundaryMesh;
    location    "constant/polyMesh";
    object      boundary;
}
// * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //

6  // ← number of patches, change if needed

frontandback
{
    type            symmetryPlane;
    // physicalType    patch;     ← you can leave or comment out
    nFaces          XXX;
    startFace       YYY;
}

wall
{
    type            wall;          // ← This must be "wall" for wall functions!
    // physicalType    patch;
    nFaces          XXX;
    startFace       YYY;
}

inlet
{
    type            patch;
    // physicalType    patch;
    nFaces          XXX;
    startFace       YYY;
}

outlet
{
    type            patch;
    // physicalType    patch;
    nFaces          XXX;
    startFace       YYY;
}

top
{
    type            patch;
    // physicalType    patch;
    nFaces          XXX;
    startFace       YYY;
}

bottom
{
    type            patch;
    // physicalType    patch;
    nFaces          XXX;
    startFace       YYY;
}
```

# solver: pisoFoam

- laminar flow, Re=200

![](https://i.imgur.com/yXRLGxO.mp4)

# References