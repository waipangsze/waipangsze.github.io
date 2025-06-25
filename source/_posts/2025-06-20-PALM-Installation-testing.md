---
layout: post
title: PALM | Installation & testing
categories: [PALM]
tags: [NWP, MPAS, WRF, PALM, LES, Urban, Installation, PALM]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-06-20 15:25:00
index_img: https://i.imgur.com/Unyeq0D.png
banner_img: https://i.imgur.com/Unyeq0D.png
---

# PALM 

- <https://palm.muk.uni-hannover.de/trac/wiki/aboutus>

**PALM（Parallelized Large-eddy simulation Model）** 是由德國漢諾萊布尼茲大學開發的一種用於研究大氣邊界層區域內的湍流過程的大渦模擬模型，已應用於各種大氣邊界層內的流場模擬

The model **PALM** is based on the **non-hydrostatic, filtered, incompressible Navier-Stokes equations in Boussinesq-approximated form** (an anelastic approximation is available as an option for simulating deep convection). By default, PALM has at least six prognostic quantities: the velocity components u, v, w on a Cartesian grid, the potential temperature θ, water vapor mixing ratio qv and possibly a passive scalar s. Furthermore, an additional equation is solved for either the subgrid-scale turbulent kinetic energy (SGS-TKE) e (LES mode, default) or the total turbulent kinetic energy (RANS mode).

**In the LES mode, the filtering process yields four subgrid-scale (SGS) covariance terms**. These SGS terms are parametrized using a 1.5-order closure after Deardorff (1980). PALM uses the modified version of Moeng and Wyngaard (1988) and Saiki et al. (2000). The closure is based on the assumption that the energy transport by SGS eddies is proportional to the local gradients of the mean quantities.

## Installation

- <https://gitlab.palm-model.org/releases/palm_model_system/-/blob/master/README.md#installation>

1. `sudo apt-get install gfortran g++ make cmake coreutils libopenmpi-dev openmpi-bin libnetcdff-dev netcdf-bin libfftw3-dev python3-pip python3-pyqt5 flex bison ncl-ncarg`
2. `python3 -m pip install -r requirements.txt`

### My conda env

{% fold info @conda env %}
```yaml
name: PALM
channels:
  - defaults
  - conda-forge
dependencies:
  - Jinja2=3.0.0
  - make
  - cmake=3.25.1
  - mike
  - mkdocs
  - mkdocs-macros-plugin
  - pymdown-extensions
  - verspec
  - netCDF4=1.6.4
  - PyQt
  - argcomplete
  - PyYAML
  - termcolor
  - f90nml
  - tqdm
  - parse
  - scipy
  - xarray
  - bison
  - fftw
```
{% endfold %}

- with gcc, gxx, gfortran, mpich, ..., which are WRF's requried library.

#### Error

##### Could NOT find MPI (missing: MPI_Fortran_FOUND) (found version "4.0")



##### NOT find MPI_Fortran

- `Could NOT find MPI_Fortran (missing: MPI_Fortran_WORKS) Configuring incomplete, errors occurred!`

1. `fortran_compiler = /home/wpsze/micromamba/envs/PALM/bin/x86_64-conda-linux-gnu-gfortran `
   1. It is **wrong**, fortran_compiler should be `gfortran`, `mpifort`, `mpif90`
   2. This comes from `fortran_compiler="$(nf-config --fc)"`
      1. `$ nf-config --fc` and search all config by `$ nf-config`
   3. It is not a Fortran compiler, **but a wrapper** around one provided. CMake generally prefers to run compilers directly (because wrappers can do…funny things).

### Install PALM

{% fold info @install.sh %}
```sh
#!/bin/bash

source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate PALM

export script_path="/home/wpsze/PALM"
echo ${script_path}

source ${script_path}/PALM_env_GNU.sh

export palm_version="v25.04" # v23.04, v25.04

function palm_install(){
    cd ${palm_version}

    cp palm_model_system/packages/palm/model/share/config/.palm.config.default .palm.config.default

    cd palm_model_system

    export install_prefix="${script_path}/${palm_version}"

    #bash install -p ${install_prefix} -c GNU -x
    bash install -p ${install_prefix} -x

    export PATH=${install_prefix}/bin:${PATH}
}

# --------------------------------- Main Control --------------------------------- #
start_time="$(date -u +%s)"
rm log.install.${palm_version}
exec > >(tee -a log.install.${palm_version}) 2>&1      # 
echo " ------------------------------  PALM ------------------------------- "
sleep 1s && echo

palm_install

echo "====================="
end_time="$(date -u +%s)"
echo "Finished datetime: $(date)"
elapsed="$(($end_time-$start_time))"
echo "Total of $elapsed seconds elapsed for process"
time_mins="$(($elapsed /60))"
echo "Total of $time_mins minutes elapsed for process"
time_hours="$(($elapsed /60/60))"
echo "Total of $time_hours hours elapsed for process" 
echo "====================="
echo " -------------------------- Completed ------------------------- "
```
{% endfold %}

```log
#------------------------------------------------------------------------#
# Installing PALM model system packages finished.
# 
# Use the following command to make the executables available
# inside your environment:
# 
#    export PATH=/home/wpsze/PALM/v25.04/bin:${PATH}
# 
#------------------------------------------------------------------------#
#------------------------------------------------------------------------#
| PALM model system installer finished.                                  |
#------------------------------------------------------------------------#
```

![](https://i.imgur.com/IUIU6xI.png)

```console
drwxrwxr-x 6 wpsze wpsze  33K Jun 20 13:37 ./
drwxr-xr-x 4 wpsze wpsze  33K Jun 20 16:03 ../
drwxrwxr-x 2 wpsze wpsze  33K Jun 20 13:37 bin/
drwxrwxr-x 2 wpsze wpsze  89K Jun 20 13:37 MAKE_DEPOSITORY_default/
-rw-rw-r-- 1 wpsze wpsze 5.1K Jun 20 13:34 .palm.config.default
drwxrwxr-x 4 wpsze wpsze  33K Jun 20 13:37 palm_model_system/
drwxrwxr-x 6 wpsze wpsze  33K Jun 20 13:34 rrtmg/
```

### Maybe issues

```log
/usr/bin/ld: cannot find -lfl
collect2: error: ld returned 1 exit status
make[1]: *** [Makefile:60: kpp] Error 1

-- PALM is not using the rrtmg library.
CMake Error at /home/wpsze/PALM/v25.04/palm_model_system/packages/palm/model/share/cmake/FindTenStream.cmake:23 (list):
  list does not recognize sub-command PREPEND
Call Stack (most recent call first):
  CMakeLists.txt:69 (find_package)


-- Found PkgConfig: /usr/bin/pkg-config (found version "1.4.2") 
-- Checking for one of the modules 'TenStream'
-- PALM is not using the TenStream library.
-- Configuring incomplete, errors occurred!
```

# Run script

{% fold info @run_test.py %}
```python
function palm_test(){
    echo "======== PALM test case ============"

    # https://palm.muk.uni-hannover.de/trac/attachment/wiki/doc/tut/palm/E-0_Installation.pdf

    cd ${install_prefix}
    mkdir -p "JOBS/example_cbl/INPUT"

    cp palm_model_system/packages/palm/model/tests/cases/example_cbl/INPUT/example_cbl_p3d JOBS/example_cbl/INPUT/
    palmrun -r example_cbl -c default -a "d3#" -X 4 -v -z
}
```
{% endfold %}

## Test

- **palm_model_system/packages/palm/model/tests/cases/example_cbl/INPUT/example_cbl_p3d**

{% fold info @log.test %}
```log

  *** execution starts in directory
      "/home/wpsze/PALM/v25.04/tmp/example_cbl.30429"
  ----------------------------------------------------------------------------

  *** execute command:
      "mpirun -n 4 ./palm" 

   16:47:42   -start----   reading environment parameters from ENVPAR
   16:47:42   -finished-   reading environment parameters from ENVPAR
   16:47:42   -start----   reading NAMELIST parameters from PARIN
   16:47:43   -finished-   reading NAMELIST parameters from PARIN
   16:47:43   -start----   creating virtual PE grids + MPI derived data types
   16:47:43   -finished-   creating virtual PE grids + MPI derived data types
   16:47:43   -start----   Setup topography
   16:47:43   -finished-   Setup topography
   16:47:43   -start----   checking parameters
   16:47:43   -finished-   checking parameters
   16:47:43   -start----   model initialization
   16:47:43   -start----   initializing surface layer
   16:47:43   -finished-   initializing surface layer
   16:47:43   -finished-   model initialization
   16:47:43   -start----   time-stepping
   16:47:48   -finished-   time-stepping
   16:47:48   -start----   calculating cpu statistics
   16:47:48   -finished-   calculating cpu statistics

  ----------------------------------------------------------------------------
  *** execution finished 



 *** post-processing: now executing "mpirun -n 1 ./combine_plot_fields.x" ... 
 
 *** combine_plot_fields ***
     uncoupled run
 
 
     NetCDF output enabled
     no XY-section data available
 
     NetCDF output enabled
     no XZ-section data available
 
     no YZ-section data available
  
     no 3D-data file available


  *** execution of OUTPUT-commands:
  ----------------------------------------------------------------------------
  >>> [[ -f LIST_PROFIL_1D ]] && cat LIST_PROFIL_1D >> LIST_PROFILE
  >>> [[ -f LIST_PROFIL ]] && cat LIST_PROFIL >> LIST_PROFILE
  >>> [[ -f PARTICLE_INFOS/_0000 ]] && cat PARTICLE_INFOS/* >> PARTICLE_INFO
  ----------------------------------------------------------------------------


  *** saving OUTPUT-files:     local time: 16:47:49
  ----------------------------------------------------------------------------
  >>> OUTPUT: PARIN  to
              /home/wpsze/PALM/v25.04/JOBS/example_cbl/MONITORING/example_cbl_parin.000

  >>> OUTPUT: STDOUT  to
              /home/wpsze/PALM/v25.04/JOBS/example_cbl/MONITORING/example_cbl_stdout.000

  >>> OUTPUT: RUN_CONTROL  to
              /home/wpsze/PALM/v25.04/JOBS/example_cbl/MONITORING/example_cbl_rc.000

  >>> OUTPUT: HEADER  to
              /home/wpsze/PALM/v25.04/JOBS/example_cbl/MONITORING/example_cbl_header.000

  >>> OUTPUT: CPU_MEASURES  to
              /home/wpsze/PALM/v25.04/JOBS/example_cbl/MONITORING/example_cbl_cpu.000

  >>> OUTPUT: DATA_1D_PR_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/example_cbl/OUTPUT/example_cbl_pr.000.nc

  >>> OUTPUT: DATA_1D_TS_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/example_cbl/OUTPUT/example_cbl_ts.000.nc

  >>> OUTPUT: DATA_2D_XY_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/example_cbl/OUTPUT/example_cbl_xy.000.nc

  >>> OUTPUT: DATA_2D_XZ_AV_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/example_cbl/OUTPUT/example_cbl_av_xz.000.nc

  >>> OUTPUT: DATA_2D_XZ_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/example_cbl/OUTPUT/example_cbl_xz.000.nc

  ----------------------------------------------------------------------------
  *** all OUTPUT-files saved       local time: 16:47:51 

 --> palmrun finished
 ```
{% endfold %}

{% gi 4 2-2 %}
![](https://i.imgur.com/qOx9DMG.png)
![](https://i.imgur.com/SRmiXoS.png)
![](https://i.imgur.com/TwoM1aQ.png)
![](https://i.imgur.com/7c5b6Dd.png)
{% endgi %}

# Exercise presentations

- <https://palm.muk.uni-hannover.de/trac/wiki/doc/tut/palm#Exercisepresentations>

![](https://i.imgur.com/vli06Jo.png)


## E3: Flow around a cubical building

1. psolver [link](https://palm.muk.uni-hannover.de/trac/wiki/doc/app/initialization_parameters#psolver)
   1. 'poisfft'
2. end_time
   1. 9000.0
3. Message: psolver = "poisfft" requires that boundary conditions along x and y are both either cyclic or non-cyclic
   1. 'https://docs.palm-model.com/25.04/Reference/LES_Model/Logging/#PAC0077'
   2. Description: Switch to `psolver = 'multigrid'` or set `bc_lr = bc_ns`.


# References

1. [热环境分析软件丨PALM城市微气候和空气质量大涡模拟](https://mp.weixin.qq.com/s/bgdvOYEkxwFUP-PYfMvUXg)
2. [大涡模拟（LES）在城市大气环境研究中的应用与挑战](https://mp.weixin.qq.com/s/wJ9k5VDCXURtQdPWI16jXw)
3. [热点论文丨基于大涡模拟与中尺度数值天气模式的精细化风场模拟](https://mp.weixin.qq.com/s/GvsJOrYhOefiIKBXuJaINA)