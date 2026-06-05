---
layout: post
title: Slurm | Check status, CPU load, and memory load
categories: [slurm]
tags: [WRF,NWP,HPC,slurm, sinfo]
author: wpsze
date: 2026-06-05 06:00:00
mathjax: true
mathjax_autoNumber: true
index_img: https://wiki.lustre.org/images/a/a3/Lustre_File_System_Overview_%28DNE%29_lowres_v1.png
banner_img: https://wiki.lustre.org/images/a/a3/Lustre_File_System_Overview_%28DNE%29_lowres_v1.png
---

# sinfo

To check node and CPU usage in Slurm, use the sinfo command combined with the --Format or --Node flags to display allocated vs. idle CPU counts and current CPU loads.

Here are the most effective commands to view this data:

## 1. View Allocated vs. Idle CPU Counts

To see a summary of your CPUs across different states (Allocated, Idle, Other, Total), run:

`sinfo --Format="Partition,NodeList:30,CPUsState:20"`

## 2. View Individual Node CPU Load

To view the exact CPU load per compute node, use:

`sinfo --Node --Format="NodeList:25,State:10,CPUsLoad:10"`

Note: A CPUsLoad value of 1.0 roughly corresponds to one fully utilized CPU core.

## 3. Count of Nodes by Resource Allocation

If you want to view a summary of CPU core counts grouped by how many nodes are in each state (Allocated/Idle/Other/Total), run:

`sinfo -eO "CPUs:8,Memory:9,NodeAIOT:16,NodeList:50"`

## 4. To check the status, CPU load, and memory load

To check the status, CPU load, and memory load for each individual node, use the sinfo command with the --Node and --Format flags.
Run this exact command in your terminal:

`sinfo --Node --Format="NodeList:20,State:12,CPUsLoad:12,FreeMem:12,AllocMem:12"`

### Column Definitions

* NodeList: The specific name of the compute node.
* **State: The current operating status (e.g., alloc, idle, mix, down).**
* **CPUsLoad: The current CPU workload (e.g., 4.00 means 4 cores are fully active).**
* FreeMem: Available RAM in megabytes (MB) remaining on the node.
* AllocMem: Total RAM in megabytes (MB) currently allocated to running jobs.

## ** 5. To display the exact breakdown of CPUs (Allocated/Idle/Other/Total) for each node

`sinfo --Node --Format="NodeList:20,State:12,CPUsState:20,CPUsLoad:12,AllocMem:12,FreeMem:12"`

### Understanding the CPUS(A/I/O/T) Output

The CPUsState column will output values in the exact format A/I/O/T:

* A (Allocated): Number of CPU cores currently assigned to active jobs.
* I (Idle): Number of CPU cores available for new jobs.
* O (Other): Number of CPU cores that are unavailable (due to node maintenance, down state, or reservation).
* T (Total): The total number of CPU cores physically present on that node.

### Quick Tip for Real-Time Monitoring

If you want to watch these CPU states and loads update live every few seconds, pair the command with watch:

`watch -n 2 sinfo --Node --Format="NodeList:20,State:12,CPUsState:20,CPUsLoad:12,AllocMem:12,FreeMem:12"`
