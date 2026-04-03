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
index_img: 
banner_img: 
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
Point(2) = {6, 0, 0, 1.0};
//+
Point(3) = {6, 4, 0, 1.0};
//+
Point(4) = {0, 4, 0, 1.0};
//+
Point(5) = {1.5, 2, 0, 1.0};
//+
Point(6) = {1.5, 2.5, 0, 1.0};
//+
Point(7) = {2, 2, 0, 1.0};
//+
Point(8) = {2, 2, 0, 1.0};
//+
Point(9) = {1, 2, 0, 1.0};
//+
Point(10) = {1.5, 1.5, 0, 1.0};
//+
Line(1) = {1, 2};
//+
Line(2) = {2, 3};
//+
Line(3) = {3, 4};
//+
Line(4) = {4, 1};
//+
Circle(5) = {6, 5, 9};
//+
Circle(6) = {9, 5, 10};
//+
Circle(7) = {10, 5, 7};
//+
Circle(8) = {7, 5, 6};
//+
Curve Loop(1) = {3, 4, 1, 2};
//+
Curve Loop(2) = {8, 5, 6, 7};
//+
Plane Surface(1) = {1, 2};
//+
Physical Curve("inlet", 9) = {4};
//+
Physical Curve("wall", 10) = {3, 1, 7, 8, 5, 6};
//+
Physical Curve("outlet", 11) = {2};
//+
Extrude {0, 0, 0.1} {
  Surface{1}; Layers {1}; Recombine;
}
```
{% endfold %}

# References