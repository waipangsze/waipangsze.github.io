---
layout: post
title: "CFD | Wall Function"
categories: [CFD]
tags: [OpenFOAM, HPC]
author: wpsze
date: 2024-12-18 22:19:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/xDxSACl.png
banner_img: https://i.imgur.com/xDxSACl.png
---

# Why using wall functions approach?

Nowadays turbulence models are heavily used when doing CFD simulations. However, **some mature turbulence models such as $ k-\epsilon $ are only valid in the area of turbulence fully developed**, and *don’t perform well in the area close to the wall*. In order to deal with the near wall region, two ways are usually proposed. 

- **One way is to integrate the turbulence to the wall**. Turbulence models are modified to enable the viscosity-affected region to be resolved with all the mesh down to the wall, including the viscous sublayer. This approach leads to requirement of abundant mesh number, which means a substantial computational resource is needed.
- **Another way is to use so-called wall functions**, which can model the near wall region. Wall functions are empirical equations used to satisfy the physics of the flow in the near wall region and the first cell center needs to be placed in the log-law region to ensure the accuracy of the result. Wall functions are used to bridge the inner region between the wall and the turbulence fully developed region, in order to provide near-wall boundary conditions for the momentum and turbulence transport equations, rather than to specify those conditions at the wall itself

{% note primary %}
Wall functions are an essential tool in CFD for modeling turbulent flows near solid boundaries. By using empirical relationships to approximate the near-wall region, they provide a balance between accuracy and computational efficiency.
{% endnote %}

![](https://i.imgur.com/vEtRCp6.png){width=600}

# Wall functions

If you are a CFD engineer or scientist, wall functions are your best choice most of the time for your turbulent flow simulations since they can provide reliably accurate results within much less time than fully solving the turbulent boundary layer. 

![](https://i.imgur.com/xDxSACl.png){width=600}

## Wall functions in OpenFOAM

**Wall functions are boundary conditions** and in OpenFOAM all of them inherit from the abstract class `FvPatchField`.

**Wall functions directory** in OpenFOAM is located in the path

`$FOAM_SRC/TurbulenceModels/turbulenceModels/derivedFvPatchFields/wallFunctions`

In wallFunctions directory there are six types of wall function sub-directories

- epsilonWallFunctions
- kqRWallFunctions
- omegaWallFunctions
- fWallFunctions
- nutWallFunctions
- v2WallFunctions

# Near-wall treatment and wall functions

- Wall functions is the approach to use if you are more interested in the mixing in the outer region, rather than the forces on the wall.
- If accurate prediction of forces or heat transfer on the walls are key to your simulation (aerodynamic drag, turbomachinery blade performance, heat transfer) you might not want to use wall functions.
- If you are conducting 2D steady simulations, do not be afraid of using a wall resolving approach.
- On the other hand, if you are conducting 3D steady simulations, everything depends on your computational resources, but it you can afford it, use a wall resolving approach.
- In all the other situations (unsteady cases) and if you do not have any restrictions in the nearwall treatment, use wall functions.
- But again, it depends on your computational resources and time available.
- **Wall functions can be used in RANS, DES and LES**.
- **If you are doing LES, it is highly recommended to use wall functions**. Otherwise, your meshing requirements will be very similar to DNS

![](https://i.imgur.com/sSDoDhW.png){width=600}
![](https://i.imgur.com/lluX5kd.png){width=600}
![](https://i.imgur.com/cOet8K5.png){width=600}
![](https://i.imgur.com/rsr0mMj.png){width=600}
![](https://i.imgur.com/GiL1ZLN.png){width=600}

# References

1. [turbulence_2021_OF8 (**recommend**)](https://www.wolfdynamics.com/training/turbulence/OF2021/turbulence_2021_OF8.pdf)
2. [A brief introduction to wall functions in CFD](https://cfdmonkey.com/a-brief-introduction-to-wall-functions-in-cfd/)
3. [7.5 Wall functions | The Architects of OpenFOAM](https://doc.cfd.direct/notes/cfd-general-principles/wall-functions)
4. [A Thorough Description Of How Wall Functions Are Implemented In OpenFOAM | Developed for OpenFOAM-4.0.x (**recommend**)](https://www.tfd.chalmers.se/~hani/kurser/OS_CFD_2016/FangqingLiu/openfoamFinal.pdf)