---
layout: post
title: MPAS -- install Metis
categories: [MPAS]
tags: [Metis, Installation]
author: wpsze
---

# METIS - Serial Graph Partitioning and Fill-reducing Matrix Ordering

[METIS](http://glaros.dtc.umn.edu/gkhome/metis/metis/overview) is a set of serial programs for partitioning graphs, partitioning finite element meshes, and producing fill reducing orderings for sparse matrices. The algorithms implemented in METIS are based on the multilevel recursive-bisection, multilevel k-way, and multi-constraint partitioning schemes developed in our lab.


# Issue & Solution

```
############################## metis ############################
if [[ ${do_metis} == True ]]; then
	echo "=== metis ===="
	cd $download_DIR
	tar xvf metis-5.1.0.tar.gz 1>/dev/null 2>&1
	cd metis-5.1.0/
	#./configure --prefix=$libs_DIR
	
	make config prefix=$libs_DIR
	make
	#make check
	make install
	cd ..
	rm -rf metis-5.1.0
	echo "=== metis (end) ===="
fi
```

Output files are,

```
 $ ls Library/bin/*meti*
Library/bin/gpmetis  Library/bin/m2gmetis  Library/bin/mpmetis  Library/bin/ndmetis
 $ ls Library/lib/*meti*
Library/lib/libmetis.a
 $ ls Library/include/*meti*
Library/include/metis.h
```

