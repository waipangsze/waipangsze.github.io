---
layout: post
title: MPAS | HPC file system failure or MPI failure
categories: [MPAS]
tags: [WRF,NWP,HPC]
author: wpsze
date: 2024-10-29 17:00:00
mathjax: true
mathjax_autoNumber: true
index_img: https://wiki.lustre.org/images/a/a3/Lustre_File_System_Overview_%28DNE%29_lowres_v1.png
banner_img: https://wiki.lustre.org/images/a/a3/Lustre_File_System_Overview_%28DNE%29_lowres_v1.png
---


When having simulation on new cluster, we have issue about the failure of multiple nodes.

1) node       up   infinite      2   idle node-[1,2]
2) submit by 1 node, it is successful.
3) submit by 2 nodes, it hangs.
4) node-[1,3] fails as well.

stops here, no respond and still occupy nodes,
```sh
Using io_type Parallel-NetCDF (CDF-5, large variable support) for mesh stream
  ** Attempting to bootstrap MPAS framework using stream: input
```

Or,

```sh
Segmentation fault (signal 11)

insert_addr_table_roots_only() /build/impi/_buildspace/release/../../src/mpid/ch4/netmod/ofi/ofi_init.c
```

Or,

```sh
This requires fcntl(2) to be implemented. As of 8/25/2011 it is not. Generic MPICH Message: File locking failed in ADIOI_GEN_SetLock(fd 1B,cmd F_SETLKW64/7,type F_RDLCK/0,whence 0) with return value FFFFFFFF and errno 26.
- If the file system is NFS, you need to use NFS version 3, ensure that the lockd daemon is running on all the machines, and mount the directory with the 'noac' option (no attribute caching).
- If the file system is LUSTRE, ensure that the directory is mounted with the 'flock' option.
```

Solution:

```sh
#!/bin/bash

ulimit -s unlimited
export OMP_STACKSIZE=1G
export I_MPI_FABRICS="shm:ofi"

# for multiple nodes,
# Enable parallel filesystem support
export I_MPI_EXTRA_FILESYSTEM=enable
# Enable cross node communication in partition
export FI_PROVIDER=tcp
...
```
to allow extra filesystem and use `tcp` that be slower a bit.