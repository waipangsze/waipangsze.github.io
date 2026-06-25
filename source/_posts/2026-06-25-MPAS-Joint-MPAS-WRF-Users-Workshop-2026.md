---
layout: post
title: MPAS | Joint MPAS/WRF Users Workshop 2026 | MPASv8.4.1
categories: [MPAS]
tags: [MPAS, NWP, WRF]
author: wpsze
date: 2026-06-25 04:27:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/Qu0lr4K.png
banner_img: https://i.imgur.com/Qu0lr4K.png
---

- [**Joint MPAS/WRF Users Workshop 2026**](https://www.mmm.ucar.edu/events/133496/agenda)
  - [MPAS-Atmosphere: Update](https://drive.google.com/file/d/1IGpToHRpyNnVtQsHN1q3UcAuG9MZZdJL/view)
  - [Data Assimilation with MPAS-JEDI: 2026 Annual update](https://drive.google.com/file/d/17oRt2PcwDVCtSE4UtUJErv606YhDqesp/view)
  - [The Weather Research and Forecasting Model: 2026 Annual Update](https://drive.google.com/file/d/1a17MJO0-Hsw8OzwTDxfgIU8JOBScYWdx/view)
    - `WRF development has slowed so the timing of V4.9 in 2027 may be uncertain`

---

# MPAS Releases Since the 2025 Workshop

- MPAS V8.3.1 – 17 June 2025
- MPAS V8.4.0 – 15 April 2026
- **MPAS V8.4.1 – 9 June 2026**

MPAS V8.4.0 – 15 April 2026

- **Large-Eddy Simulation capability** (PR 1404)
  - The MPAS V8.4.0 release does not work for full-physics configurations if the PBL scheme is disabled and only the surface-layer scheme is used. This bug was fixed in the MPAS V8.4.1 release.
- Mesospheric model top modifications (PRs 1380, 1381)
- Online mesh partitioning (PR 1364)
  - MPAS V8.4.0 now allows for online mesh partitioning using SCOTCH.
- Infrastructure for chemistry (MUSICA, PRs 1309, 1376, 1360)
- **New MPAS-A Mesh Tools: JIGSAW**
  - ![](https://i.imgur.com/DyczTaM.png)

---

# Ongoing Developments

{% gi 2 2 %}
![](https://i.imgur.com/lnhB10N.png)
![](https://i.imgur.com/1q9sJTP.png)
{% endgi %}

