---
layout: post
title: MPAS | Modify Source Code
categories: [MPAS]
tags: [MPAS, NWP, WRF]
author: wpsze
date: 2026-09-09 06:27:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/cvgItH2.png
banner_img: https://i.imgur.com/cvgItH2.png
---

- Comparing changes
  - <https://github.com/MPAS-Dev/MPAS-Model/compare/release-v8.1.0...v8.2.2>

---

# Compilation

```
#!/bin/bash
############################## Directory Listing ############################
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"
source /home/wpsze/MPAS-A/intel-oneapi/mpas_env_intel.sh

which mpiicc && mpiicc --version
which mpiifort && mpiifort --version
which mpiicpc && mpiicpc --version
which icc && icc --version
which ifort && ifort --version

echo $NETCDF
echo $PNETCDF
echo $PIO

##--Intel MPI与Open MPI、MPICH等MPI实现不同：
##--mpiicc、mpiicpc和mpiifort命令：使用Intel编译器
##--mpicc、mpif90和mpifc命令：默认使用GNU编译器

do_init_atmosphere=True # True, False
do_atmosphere=True
do_build_tables=True

mpas_version="MPASv822-verify"

echo "Copy MPAS-Model to build-mpas ... "
rm -rf build-mpas
rm -rf ${mpas_version}
cp -r "MPASv8.2.2-modify" "build-mpas"
cd build-mpas

############################## init_atmosphere ############################ 
if [[ ${do_init_atmosphere} == True ]]; then
	echo "=== init_atmosphere ===="
	
	make clean CORE=init_atmosphere
	make clean CORE=atmosphere
	
	# mpas_dmpar.F:
 	# call MPI_Allreduce(inArray, outArray, nElements, MPI_INTEGERKIND, MPI_MAX, dminfo % comm, mpi_ierr) 
	# Warning: Rank mismatch between actual argument at (1) and actual argument at (2) (scalar and rank-1)
	
	#export FFLAGS="-w -fallow-argument-mismatch -O2"
	#export FCFLAGS="-w -fallow-argument-mismatch -O2"

	#make -j8 gfortran CORE=init_atmosphere PRECISION=single DEBUG=true USE_PIO2=true >& log_build2.txt
	make intel-mpi CORE=init_atmosphere PRECISION=single USE_PIO2=true 2>&1 |tee log_build_init.txt
	
	### Check MPAS model.
	#ls -la init_atmosphere_model
	echo "=== init_atmosphere (end) ===="
fi

############################## atmosphere_model ############################
if [[ ${do_atmosphere} == True ]]; then
	echo "=== atmosphere_model ===="
	#--- To preserve all executables except atmosphere_model and clean the MPAS infrastructure, run: 
	make clean CORE=atmosphere
	
	#export FFLAGS="-w -fallow-argument-mismatch -O2"
	#export FCFLAGS="-w -fallow-argument-mismatch -O2"

	# DEBUG=true
    make -j8 intel-mpi CORE=atmosphere PRECISION=single USE_PIO2=true 2>&1 |tee log_build_atm.txt
	#make -j8 intel-mpi CORE=atmosphere PRECISION=single USE_PIO2=true DEBUG=true 2>&1 |tee log_build_atm.txt
	#make intel-mpi CORE=atmosphere PRECISION=single MPAS_MPI_F08=false USE_PIO2=true 2>&1 |tee log_build_atm.txt
	
	### Check MPAS model.
	#ls -la atmosphere_model
	echo "=== atmosphere_model (end) ===="
wait

fi

if [[ -f atmosphere_model ]]; then
	mkdir -p $SCRIPT_DIR/${mpas_version}
	mv streams.init_atmosphere $SCRIPT_DIR/${mpas_version}
	mv namelist.init_atmosphere $SCRIPT_DIR/${mpas_version}
	mv init_atmosphere_model $SCRIPT_DIR/${mpas_version}

	mv atmosphere_model $SCRIPT_DIR/${mpas_version}
	mv build_tables $SCRIPT_DIR/${mpas_version}
	mv namelist.atmosphere $SCRIPT_DIR/${mpas_version}
	mv streams.atmosphere $SCRIPT_DIR/${mpas_version}
	mv stream_list.atmosphere.diagnostics $SCRIPT_DIR/${mpas_version}
	mv stream_list.atmosphere.output $SCRIPT_DIR/${mpas_version}
	mv stream_list.atmosphere.surface $SCRIPT_DIR/${mpas_version}

	mv ./src/core_atmosphere/physics/physics_wrf/files/* $SCRIPT_DIR/${mpas_version}/

	if [[ ${do_build_tables} == True ]]; then
		cd $SCRIPT_DIR/${mpas_version}
		./build_tables
	fi

fi
```

# Serial/Parallel compilation

- Serial 
- Parallel `make -j8` 

# `mpi_f08` issue

- encounter: ` 75 forrtl: severe (408): fort: (3): Subscript #1 of the array GROUPTOFIELDRECVIDX has value 0 which is less than the lower bound of 1$`
  - `mpas_halo_mp_mpas         728  mpas_halo.F$`
- MPASv8.2.2: call more MPI functions from `mpi_f08`
  - <https://github.com/MPAS-Dev/MPAS-Model/blob/41e9a3fb8ca6b9250a7405209a5c60996318409f/src/framework/mpas_halo.F#L488>
  - if it is the bug, you can disable it
    - set `MPAS_MPI_F08=false`, please check `Makefile` first
    - `make intel-mpi CORE=atmosphere PRECISION=single MPAS_MPI_F08=false USE_PIO2=true 2>&1 |tee log_build_atm.txt`

# Simple MPAS I/O Layer (SMIOL)

- [MPAS | Simple MPAS I/O Layer (SMIOL)](https://waipangsze.github.io/2026/02/11/MPAS-Simple-MPAS-IO-Layer-SMIOL/)