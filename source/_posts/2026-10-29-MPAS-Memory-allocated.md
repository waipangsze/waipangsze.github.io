---
layout: post
title: MPAS | Memory allocated
categories: [MPAS]
tags: [WRF, MPAS, NWP]
author: wpsze
date: 2026-10-29 07:01:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

- `src/framework/mpas_block_creator.F`
- <https://github.com/MPAS-Dev/MPAS-Model/blob/v8.4.0/src/framework/mpas_block_creator.F>
- Commit f324b9b mgduda committed on Feb 10, 2022
  - <https://github.com/MPAS-Dev/MPAS-Model/commit/f324b9b72544efcf9b46ab2af5c0c15b0d1ae7f0>


```log
Report in log file the total memory allocated for fields by the block creator
This commit adds code to the mpas_block_creator module to track and
report the total number of bytes allocated for fields by the
mpas_block_creator_allocate_pool_fields routine. Allocated bytes are
reported for both the MPI task itself as well as the sum across all MPI
tasks. The report of the number of allocated bytes is written to the log
file, for example:

 Allocating fields ...
  284 MB allocated for fields on this task
  3408 MB total allocated for fields across all tasks
  ----- done allocating fields -----
```

```fortran
call mpas_log_write('Allocating fields ...')
allocated_bytes = 0_I8KIND

call mpas_block_creator_allocate_pool_fields(block_ptr % structs, block_ptr % dimensions, allocated_bytes, ierr)
if (ierr /= 0) then
    call mpas_log_write('Allocation of fields failed for core ' // trim(block_ptr % domain % core % coreName), MPAS_LOG_CRIT)
end if

call mpas_log_write(' $i MB allocated for fields on this task', intArgs=[int(allocated_bytes / 1000000_I8KIND)])
if (domain % dminfo % nprocs > 1) then
    call mpas_dmpar_sum_int(domain % dminfo, int(allocated_bytes / 1000000_I8KIND), total_allocated_mb)
    call mpas_log_write(' $i MB total allocated for fields across all tasks', intArgs=[total_allocated_mb])
end if
call mpas_log_write(' ----- done allocating fields -----')
call mpas_log_write('')
```

