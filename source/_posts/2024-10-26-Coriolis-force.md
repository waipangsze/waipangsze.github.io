---
layout: post
title: Coriolis force | 科氏力
categories: [Meteorology]
tags: []
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2024-10-26 15:38:00
index_img: https://upload.wikimedia.org/wikipedia/commons/thumb/3/32/Northern_vs_Southern_hemisphere_tropical_cyclones.jpg/2880px-Northern_vs_Southern_hemisphere_tropical_cyclones.jpg
banner_img: https://upload.wikimedia.org/wikipedia/commons/thumb/3/32/Northern_vs_Southern_hemisphere_tropical_cyclones.jpg/2880px-Northern_vs_Southern_hemisphere_tropical_cyclones.jpg
---

In physics, the **Coriolis force** is an inertial (or fictitious) force that acts on objects in motion within a frame of reference that rotates with respect to an inertial frame. In a reference frame with clockwise rotation, the force acts to the left of the motion of the object. In one with anticlockwise (or counterclockwise) rotation, the force acts to the right. Deflection of an object due to the Coriolis force is called the Coriolis effect. Though recognized previously by others, the mathematical expression for the Coriolis force appeared in an 1835 paper by French scientist Gaspard-Gustave de Coriolis, in connection with the theory of water wheels. Early in the 20th century, the term Coriolis force began to be used in connection with meteorology.

![If the air began to move in response to that force, however, the Coriolis force would deflect it, to the right of the motion in the northern hemisphere or to the left in the southern hemisphere.](https://i.imgur.com/knTtXP0.jpeg){width=400}

{% gi 2 2 %}
![](https://i.imgur.com/lTrDNpz.jpeg){width=400}
![](https://i.imgur.com/6p5PJ2P.jpeg)
{% endgi %}

# Derivation

$$
\begin{align*}
\mathbf{r} = x\hat{\mathbf{i}} + y\hat{\mathbf{j}} + z\hat{\mathbf{k}} 
\end{align*}
$$

Specify subscript $r$ to represent **a body-fixed system (The Earth)** as basis $\hat{\mathbf{i}}, \hat{\mathbf{j}}, \hat{\mathbf{k}}$,

$$
\begin{align*} 
\left( \dfrac{d \mathbf{r}}{dt} \right)_r &= \dfrac{dx}{dt}\hat{\mathbf{i}} + \dfrac{dy}{dt}\hat{\mathbf{j}} + \dfrac{dz}{dt}\hat{\mathbf{k}} 
\end{align*} 
$$

When a **space-fixed system** is considered,

$$
\begin{align*}
\left( \dfrac{d \mathbf{r}}{dt} \right)_s &= \dfrac{dx}{dt}\hat{\mathbf{i}} + \dfrac{dy}{dt}\hat{\mathbf{j}} + \dfrac{dz}{dt}\hat{\mathbf{k}} + x\dfrac{d \hat{\mathbf{i}}}{dt} + y\dfrac{d \hat{\mathbf{j}}}{dt} + z\dfrac{d \hat{\mathbf{z}}}{dt} \\
& = \left( \dfrac{d \mathbf{r}}{dt} \right)_r + x\dfrac{d \hat{\mathbf{i}}}{dt} + y\dfrac{d \hat{\mathbf{j}}}{dt} + z\dfrac{d \hat{\mathbf{z}}}{dt} 
\end{align*} 
$$

What are last three terms?

$$
\begin{align*}
\dfrac{d \hat{\mathbf{i}}}{dt} &= \mathbf{\omega} \times \hat{\mathbf{i}} \\
\dfrac{d \hat{\mathbf{j}}}{dt} &= \mathbf{\omega} \times \hat{\mathbf{j}} \\
\dfrac{d \hat{\mathbf{z}}}{dt} &= \mathbf{\omega} \times \hat{\mathbf{z}} \\
\end{align*} 
$$

then,

$$
\begin{align*}
\left( \dfrac{d \mathbf{r}}{dt} \right)_s & = \left( \dfrac{d \mathbf{r}}{dt} \right)_r + \mathbf{\omega} \times \mathbf{r} \\
\end{align*} 
$$

it is generalized to any vector $\mathbf{A}$,

$$
\begin{align*}
\left( \dfrac{d \mathbf{A}}{dt} \right)_s & = \left( \dfrac{d \mathbf{A}}{dt} \right)_r + \mathbf{\omega} \times \mathbf{A} \\
\end{align*} 
$$

consider velocity $\mathbf{v_s} = \left( \dfrac{d \mathbf{r}}{dt} \right)_s = \mathbf{v_r} + \mathbf{\omega} \times \mathbf{r}$,

$$
\begin{align*}
\mathbf{a_s} = \left( \dfrac{d \mathbf{v_s}}{dt} \right)_s & = \left( \dfrac{d \mathbf{v_s}}{dt} \right)_r + \mathbf{\omega} \times \mathbf{v_s} \\
& = \left( \dfrac{d (\mathbf{\mathbf{v_r} + \mathbf{\omega} \times \mathbf{r}})}{dt} \right)_r + \mathbf{\omega} \times (\mathbf{v_r} + \mathbf{\omega} \times \mathbf{r}) \\
& = \mathbf{a_r} + \dfrac{d \mathbf{\omega}}{dt} \times \mathbf{r} +\mathbf{\omega} \times \mathbf{v_r} + \mathbf{\omega} \times \mathbf{v_r} + \mathbf{\omega} \times (\mathbf{\omega} \times \mathbf{r})
\end{align*} 
$$

let $\omega$ is indepenent of time,

$$
\begin{align*}
\mathbf{a_s} &= \mathbf{a_r} + 2\mathbf{\omega} \times \mathbf{v_r} + \mathbf{\omega} \times (\mathbf{\omega} \times \mathbf{r})
\end{align*} 
$$

The equation of motion on space-fixed system is,

$$
\begin{align*}
\mathbf{F} = m \mathbf{a_s}
\end{align*} 
$$

and converted to body-fixed system (**The Earth**) is,

$$
\begin{align*}
\mathbf{F} - 2 m \mathbf{\omega} \times \mathbf{v_r} - m \mathbf{\omega} \times (\mathbf{\omega} \times \mathbf{r}) = m \mathbf{a_r} 
\end{align*} 
$$

For the observer in the body-fixed or rotating system (**The Earth**), he/she will be suffered a Force/effective Force,

{% note primary %}
$$
\begin{align*}
\mathbf{F_{eff}} = m \mathbf{a_r} = \mathbf{F} - 2 m \mathbf{\omega} \times \mathbf{v_r} - m \mathbf{\omega} \times (\mathbf{\omega} \times \mathbf{r}) 
\end{align*} 
$$
{% endnote %}

The second term $- 2 m \mathbf{\omega} \times \mathbf{v_r}$ called the **Coriolis force**, is present when a particle is moving in the rotating co-ordinate system. This is also not a real force, but a fictitious one. Another feature of this force is that it does no work, since it acts in a direction perpendicular to velocity.

The third term $- m \mathbf{\omega} \times (\mathbf{\omega} \times \mathbf{r})$ is **centrifugal force** that $| m \mathbf{\omega} \times (\mathbf{\omega} \times \mathbf{r})| = mr \omega^2 \sin \theta$. he negative sign indicates
that the centrifugal force is directed away from the centre of rotation. It is not a real force, but a fictitious one. It is present only if we refer to moving coordinates in space.

# Coriolis parameter & Rossby number
The time, space, and velocity scales are important in determining the importance of the Coriolis force. Whether rotation is important in a system can be determined by its Rossby number, which is the ratio of the **velocity**, $U$, of a system to the product of the **Coriolis parameter**,

{% note primary %}
$$
f=2\omega \sin \varphi
$$ 
{% endnote %}

,and the $\omega = 7.2921 × 10^{−5}$ rad/s can be calculated as $2\pi / T$ radians per second, the **length scale**, $L$,  **latitude** $\varphi$ of the motion:

$$
Ro = \frac{U}{fL}
$$

In the midlatitudes, the typical value for $f$ is about $10^{−4}$ rad/s.

Hence, it is the ratio of inertial to Coriolis forces; **a small Rossby number indicates a system is strongly affected by Coriolis forces**, and **a large Rossby number indicates a system in which inertial forces dominate**. For example, in tornadoes, the Rossby number is large, so in them the Coriolis force is negligible, and balance is between pressure and centrifugal forces. In low-pressure systems the Rossby number is low, as the centrifugal force is negligible; there, the balance is between Coriolis and pressure forces. In oceanic systems the Rossby number is often around 1, with all three forces comparable.

- **An atmospheric system moving at U = 10 m/s (22 mph) occupying a spatial distance of L = 1,000 km (621 mi), has a Rossby number of approximately 0.1**
- A baseball pitcher may throw the ball at U = 45 m/s (100 mph) for a distance of L = 18.3 m (60 ft). The Rossby number in this case would be 32,000 (at latitude 31°47'46.382").

# Rossby parameter

In stability calculations, the rate of change of $f$ along the meridional direction becomes significant. This is called the Rossby parameter and is usually denoted

$$
\beta = \frac{\partial f}{\partial y}
$$

where $y$ is the in the local direction of increasing meridian. This parameter becomes important, for example, in calculations involving Rossby waves.

# f-plane approximation

In geophysical fluid dynamics, the **f-plane approximation** is an approximation where the **Coriolis parameter**, denoted $f$, is set to a constant value.

$$
\begin{align*}
f &= f_0 = 2\Omega\sin(\phi_0)
\end{align*}
$$

- This approximation is frequently used for the analysis of highly idealized tropical cyclones. 
- f-plane approximation is not appropriate when considering flows which span large changes in latitude.
- The f-plane approximation is also poor near the equator.

# beta-plane approximation

In geophysical fluid dynamics, an approximation whereby the **Coriolis parameter**, $f$, is set to **vary linearly** in space is called a **beta plane approximation**.

$$
\begin{align*}
f &= f_0 + \beta y \\
\beta &= (\mathrm{d}f/\mathrm{d}y)|_{\phi_0} = 2\Omega\cos(\phi_0)/R
\end{align*}
$$

where $f_{0}$ is the Coriolis parameter at $\phi _{0}$.

The beta plane approximation is useful for the theoretical analysis of many phenomena in geophysical fluid dynamics since **it makes the equations much more tractable**, yet retains the important information that the Coriolis parameter varies in space. In particular, Rossby waves, the most important type of waves if one considers large-scale atmospheric and oceanic dynamics, depend on the variation of f as a restoring force; they do not occur if the Coriolis parameter is approximated only as a constant.

- Ripa, P. (1997). “Inertial” Oscillations and the β-Plane Approximation(s). Journal of Physical Oceanography, 27(5), 633-647. https://doi.org/10.1175/1520-0485(1997)027<0633:IOATPA>2.0.CO;2

# References

1. [Coriolis-Force.pdf](https://magadhmahilacollege.org/wp-content/uploads/2020/09/Coriolis-Force.pdf)
2. [wiki](https://en.wikipedia.org/wiki/Rotating_reference_frame#Relating_rotating_frames_to_stationary_frames)