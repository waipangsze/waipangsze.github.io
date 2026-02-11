---
layout: post
title: MPAS | Simple MPAS I/O Layer (SMIOL)
categories: [MPAS]
tags: [WRF, MPAS, NWP, SMIOL]
author: wpsze
date: 2026-02-11 07:01:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/xBoWNBP.png
banner_img: https://i.imgur.com/xBoWNBP.png
---

# Simple MPAS I/O Layer (SMIOL)


The **Simple MPAS I/O Layer (SMIOL)** is a lightweight, parallel I/O library introduced in MPAS v8.0+ as an alternative to the Parallel I/O (PIO) library. It provides necessary parallel I/O functionality for reading/writing distributed arrays with minimal dependencies, relying solely on MPI and PNetCDF. SMIOL is enabled at compile time by not setting the $PIO environment variable.

- [https://github.com/MPAS-Dev/MPAS-Model/releases/tag/v8.0.0](https://github.com/MPAS-Dev/MPAS-Model/releases/tag/v8.0.0)
  - Introduce a new I/O layer, the `Simple MPAS I/O Layer`. `SMIOL` provides a subset of the parallel I/O functionality of PIO, but with a simplified design that minimizes external dependencies to just the `Parallel-NetCDF library`. When the `$PIO` environment variable is not set at compile time, `SMIOL` will be used in MPAS.

# Compiling

- [https://www2.mmm.ucar.edu/projects/mpas/tutorial/Howard2024/lectures/downloading_and_compiling.pdf](https://www2.mmm.ucar.edu/projects/mpas/tutorial/Howard2024/lectures/downloading_and_compiling.pdf)

## Setup

- When the `$PIO` environment variable is not set at compile time, `SMIOL` will be used in MPAS.
- As you noted, the toggle for `SMIOL` happens at the compile stage. If the build system doesn't detect a `$PIO` path, it defaults to this "Simple" layer.

```sh
export JASPERLIB=$libs_DIR
export PNETCDF_PATH=$libs_DIR
export PNETCDF=$libs_DIR
#export NETCDF_PATH=$libs_DIR
#export NETCDF=$libs_DIR
#export PIO_PATH=$libs_DIR
#export PIO=$libs_DIR
```

If success,

- `Using the SMIOL library.` at the end of your log file.

{% fold info @log %}
```log
*******************************************************************************
MPAS was built with default single-precision reals.
Debugging is off.
Parallel version is on.
Using the mpi_f08 module.
Papi libraries are off.
TAU Hooks are off.
MPAS was built without OpenMP support.
MPAS was built without OpenMP-offload GPU support.
MPAS was built without OpenACC accelerator support.
Position-dependent code was generated.
MPAS was built with .F files.
The native timer interface is being used
Using the SMIOL library.
*******************************************************************************
make[1]: Leaving directory '/home/wpsze/MPAS-A/intel-oneapi/mpasv831_SMIOL/build-mpas'
=== atmosphere_model (end) ====
  
 Constructing tables for Thompson cloud microphysics scheme.
 This may take as much as 15-20 minutes with an optimized build...
 
 --- building MP_THOMPSON_QRacrQG_DATA.DBL
 
 --- building MP_THOMPSON_QRacrQS_DATA.DBL
 
 --- building MP_THOMPSON_freezeH2O_DATA.DBL
 
 --- building MP_THOMPSON_QIautQS_DATA.DBL
 
 Finished building all tables.
 
 *******************************************************************************
 To preserve these tables when running 'make clean', please copy the following
 files to the src/core_atmosphere/physics/physics_wrf/files/ directory:
 
   MP_THOMPSON_QRacrQG_DATA.DBL
   MP_THOMPSON_QRacrQS_DATA.DBL
   MP_THOMPSON_freezeH2O_DATA.DBL
   MP_THOMPSON_QIautQS_DATA.DBL
 
 Tables in the src/core_atmosphere/physics/physics_wrf/files/ directory 
 will be automatically linked to the top-level MPAS directory when compiling
 the 'atmosphere' core.
 *******************************************************************************
```
{% endfold %}

# Why Use SMIOL? 

The primary "sell" for SMIOL is its minimalist footprint. While PIO is powerful, it often requires a complex stack of libraries (like NetCDF-C, PnetCDF, and sometimes HDF5). SMIOL strips this back to the essentials.

## Key Features

- **Dependency** Light: It only requires MPI and PNetCDF.
- **Built-in Logic**: It handles the remapping between the distributed memory layout (how MPI tasks hold data) and the file layout (how data sits on the disk) internally.
- **Lower Overhead**: Because it doesn't have the abstraction layers of PIO, it can be easier to debug and faster to link during the build process.

| Feature      | PIO (Parallel I/O Library)       | SMIOL (Simple MPAS I/O Layer)                 |
|--------------|----------------------------------|-----------------------------------------------|
| Ease of Setup      | Challenging (Multiple dependencies)    | Easy (Minimal dependencies)        |
| Required Libraries | MPI, PnetCDF, NetCDF-4, HDF5, PIO      | MPI, PnetCDF                       |
| Integration  | External library                 | Integrated within MPAS v8.0+                  |
| Use Case     | Large-scale production runs      | High-performance runs with fewer dependencies |
| File Format        | Supports NetCDF-4 (HDF5) & Classic     | Primarily PnetCDF (Classic/64-bit) |
| Maintenance        | High (Updates to any lib can break it) | Low (Integrated in MPAS core)      |

# A Quick Reality Check

While `SMIOL` is excellent for performance, remember that it specifically targets `PNetCDF` **(Parallel NetCDF)**. This means your output files will be in `classic NetCDF` formats. If your workflow strictly requires **NetCDF-4/HDF5 features (like internal compression)**, you might still find yourself reaching for the full `PIO` library.

{% note primary %}
**Note**: The only trade-off to keep in mind is that `SMIOL` focuses on the `PnetCDF` format. If your specific post-processing workflow requires `NetCDF-4` features like transparent compression or `HDF5`-based files, you would still need the traditional `PIO` library.
{% endnote %}

# Classic NetCDF vs NetCDF-4/HDF5

When choosing between **Classic NetCDF** (used by `SMIOL` via `PnetCDF`) and **NetCDF-4/HDF5** (supported by `PIO`), you are essentially trading off raw I/O speed for storage efficiency and data structure complexity.

## File Structure and Performance

The "Classic" formats are flat and predictable, while NetCDF-4 is hierarchical and flexible.

- **Classic (CDF-1, CDF-2, CDF-5)**: Uses a **linear layout**. Because the data's location on the disk is mathematically predictable, PnetCDF can perform high-speed, non-blocking parallel writes. This is why SMIOL is so efficient for large MPAS meshes.
- **NetCDF-4 (HDF5-based)**: Uses a complex **"B-tree" structure**. While this allows for features like "Groups" (folders within a file), it adds metadata overhead that can slow down massive parallel write operations compared to the classic format.

## Key Feature Comparison

If you use `SMIOL`, you are opting for the `Classic/PnetCDF` side of this table:

| Feature         | Classic / 64-bit Offset (SMIOL)   | NetCDF-4 / HDF5 (PIO)                 |
|-----------------|-----------------------------------|---------------------------------------|
| Compression     | No (Files are larger)             | Yes (Zlib/Deflate compression)        |
| Parallelism     | High-speed via PnetCDF            | Variable via HDF5 layer               |
| Data Types      | Basic (Int, Float, Double)        | Complex (Strings, User-defined types) |
| File Size Limit | Virtually unlimited (with 64-bit) | Virtually unlimited                   |
| Chunking        | No                                | Yes (Improves subsetting speed)       |

## Which one should you use?

- Choose `Classic` (`SMIOL` / `PnetCDF`) if:
  - **Performance is King**: You are running very large simulations where I/O bottlenecks are a concern.
  - **Simplicity**: You want a build process with fewer dependencies and less chance of library version conflicts.
  - **HPC Environments**: Your supercomputer has a high-performance Lustre or GPFS parallel file system that PnetCDF is optimized for.
- Choose `NetCDF-4` (`PIO` / `HDF5`) if:
  - **Disk Space is Limited**: You need internal compression to keep output file sizes manageable.
  - **Downstream Tools**: Your specific post-processing or visualization software requires NetCDF-4 features (like groups or specific metadata standards).
  - **Complex Metadata**: You need to store many different types of related data in a single, organized file structure.
