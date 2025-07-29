---
layout: post
title: WRF | Computation, Decomposition, Order of operations
categories: [WRF]
tags: [WRF, HPC, NWP, MPI, domain, patch, tile]
author: wpsze
date: 2025-07-28 07:56:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/XPg6bGj.png
banner_img: https://i.imgur.com/XPg6bGj.png
---

[**WRF | MPAS | MPI | halo/ghost cell**](https://waipangsze.github.io/2024/12/07/wrf-mpas-MPI-halocell-ghostcell/)

---

# Nesting in WRF, Kelly Werner, NCAR/MMMJanuary 2021

- <https://www2.mmm.ucar.edu/wrf/users/tutorial/presentation_pdfs/202101/werner_nesting.pdf>

{% gi 9 3-3-3 %}
![](https://i.imgur.com/X2j8OxF.png)
![](https://i.imgur.com/QdUwbiD.png)
![](https://i.imgur.com/ThrmGx9.png)
![](https://i.imgur.com/gkATfLh.png)
![](https://i.imgur.com/jpZIqrR.png)
![](https://i.imgur.com/XpLTjqz.png)
![](https://i.imgur.com/XPg6bGj.png)
![](https://i.imgur.com/qt0IAg7.png)
![](https://i.imgur.com/IVCWn3A.png)
{% endgi %}

# Difference between ids, ims, ips,its

- [Difference between ids, ims, ips,its | Sep 15, 2019](https://forum.mmm.ucar.edu/threads/difference-between-ids-ims-ips-its.8435/)
  - "ids,, ide, jds, jde" etc. are "domain" sized indexes along the i, j, k directions.
  - "ims, ime, jms, jme" are "memory" dimensions for arrays.
  - "ips, ipe, jps, jpe" are computational extent for loops inside most of the WRF physics.

A more detailed description can be found in the website:

- <https://www2.mmm.ucar.edu/wrf/users/tutorial/>

# Tile Strategy is not specified.

- [Tile Strategy is not specified. Jun 20, 2019](https://forum.mmm.ucar.edu/threads/tile-strategy-is-not-specified.5523/)

```log
So for your domain set-up, you have

e_we = 637
e_sn = 534

WRF domains are 1-based, which means that your domain is actually 533 x 636. 
Since you have 12 processors, this divides your domain into a 4x3 grid with 
tiles of size 158x177 (some rounding is done, and adjustments are made when 
the number is not perfectly divisible). So this particular print-out looks 
like it's for the 2nd tile over from the start of i (SW corner), and the 
first tile in the j-direction. Therefore this line:

Code:
WRF TILE 1 IS 160 IE 318 JS 1 JE 178

actually means:
for this particular tile,
i-start = 160
i-end = 318
j-start = 1
j-end = 178

Your decomposition should be:
i-direction
1-159
160-318
319-477
478-636

j-direction
1-178
179-356
357-534
```

- You are correct. If this were an `OpenMP` build, the `"WRF Tile"` information would refer to a sub-tiling of the `MPI` rank's patch.
- It would be better if the following line in the model output did not appear unless we were running `OpenMP`:
  - Code:
    - `Tile Strategy is not specified. Assuming 1D-Y`
- Again, this printout should be removed from the output, but the line is currently there. **Without OpenMP, it signifies nothing more than a reminder that the computational area on each MPI rank is the work that is handled by a single MPI task.**
- The next line(s) are useful for MPI and for OpenMP. If this is an OpenMP build, there will be as many lines with "WRF Tile" as there are OpenMP threads. When this is an MPI-only build, then the single line with the tile size (the information on the "WRF Tile" line) is identical to the patch size (the information a few lines up that, that is on the "ips,ipe,jps,jpe" line).


# How are nested domains divided in distributed memory execution?

- [How are nested domains divided in distributed memory execution? Aug 7, 2020](https://forum.mmm.ucar.edu/threads/how-are-nested-domains-divided-in-distributed-memory-execution.9433/)

{% gi 2 2 %}
![](https://i.imgur.com/dh4gKBo.png){width=300}
![](https://i.imgur.com/6ivOG3w.png){width=300}
{% endgi %}

The first is a depiction of a WRF simulation with five total domains. The yellow area is domain 1, the blue box is domain 2, and the cyan boxes are domains 3, 4, 5. These are set up just to show us the order of operations.

The second attachment shows the sequential procedure within the WRF model.

1. Domain 1 runs a single time step for 3 minutes (red arrow).
2. Domain 2 runs a single time step for 1 minute (yellow arrow). Before domain 2 can do the second time step, all of domain 2's children need to catch up.
3. Domain 3 runs multiple time steps to catch up to domain 2 (20 second time step).
4. Domain 4 runs multiple time steps to catch up to domain 2.
5. Domains 5 run multiple time steps to catch up to domain 2.
6. Domains 3, 4, and 5 feed back to domain 2.
7. The steps 2 - 6 are repeated until domain 2 has caught up with domain 1.
8. Domain 2 feeds back to domain 1.
9. Steps 1 - 8 are repeated until all domains are completed with the entire simulation.

For computational performance, generally the amount of time that the WRF model spends on a single grid column is nearly constant. If we assume that all domains are running the same physics, then the amount of computation is about the same for a single grid column, whether it be from domain 1 or domain 2. However, a nest causes the model to be expensive for two reasons.

1. The fine grid time step is smaller (typically, the time ratio is the same as the grid distance ratio). While the amount of time is similar to do a single grid column, the fine grid has to do that computation more times to keep pace with the larger time step from the parent domain.
2. A nested simulation has additional overhead: computing lateral boundaries at each parent time step, feedback at the end of the child time steps. And for a distributed memory machine, these also entail a good amount of communication.

Let's use the 5-domain example that I depicted. We have a 3:1 ratio for the grid distance ratios and the time step ratios. Let us assume that the number of grid cells is identical in each of the domains (for example, all are 100x100 or all of 500x500, it does not really matter).

Let us define the cost of running domain 1 as ONE unit of computation.

Without overhead (boundary update, feedback, extra nesting communications), the cost of domain 2 would then THREE units of computation (the same number grid points, just doing the computations three times more due to the smaller time step).

With the same arm waving, each of domains 3, 4, and 5 would cost NINE units of computation (due entirely to their smaller time step).

Without any nesting overhead, one would expect this five domain run to be 31x more expensive than the single domain case (1 + 3 + 9 + 9 + 9).

With nesting overhead, the costs are a bit unpredictable. We tend to see an additional factor of anywhere between 10-25%, depending on how expensive the communications interconnect is (hardware on the computer). The process of nesting is communication intensive. The more domains and levels of nests, the more the overhead.

For your particular case, there are about 8% more grid cells on the fine domains than d01.

- d01 costs 1 unit of time
- d02 costs 1.08 * 5 units of time
- d03 costs 1.08 * 25 units of time
- d04 costs 1.08 * 125 units of time
- d05 costs 1.08 * 625 units of time

Compared to the d01 only (which is our unit measure of time), the entire simulation without overhead relative to the coarse domain is ( 1 + 1.08*(5 + 25 + 125 + 625 ) ) = 843.4x the cost of d01.

From this, you can see why the advice we give is to make the outer domains LARGE. Domains 1 and 2 together are not even 1% of the simulation's cost.

For your timing estimates, on your current machine, just run the first two domains for a few time steps. Throw away the initial time and make sure that a radiation step is included in your timing. You should be able to get a good scaling from domains 1 and 2 compared to your entire simulation.

Next, run the small d01 and d02 case on the new machine. Use the same scale factor to estimate what the five domain cost would be on the new machine.

The absolute amount of computations per grid cell and the speed of the chip are not a direct way to handle the timing performance, due to the relatively low percent of peak that WRF is able to obtain, the wild variations in compiler performance, the huge impact that I/O can have on a full simulation, etc.

Lastly, the default decomposition for each domain is based only on the number of MPI process and the number of grid cells in a domain. If the MPI processes are set up to decompose the domain into 8 chunks in the south-north direction, and 4 chunks in the west-east direction - that same decomposition is used for every domain.

# Choosing an Appropriate Number of Processors

- <https://forum.mmm.ucar.edu/threads/choosing-an-appropriate-number-of-processors.5082/>