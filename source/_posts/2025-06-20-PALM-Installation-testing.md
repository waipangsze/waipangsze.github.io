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

## Some Software requirements 

- <https://palm.muk.uni-hannover.de/trac/wiki/doc/install#Softwarerequirements>
- `palmplot` 
  - The graphic-package `NCL` from NCAR (needed by the **data visualization tool palmplot**)
- `kpp4palm`
  - The `FLEX` library `BISON` parser generator (needed by the **chemistry tool kpp4palm**)

## PALM preprocessor options / define strings

- <https://palm.muk.uni-hannover.de/trac/wiki/doc/app/cpp_options>

![](https://i.imgur.com/V29VF2S.png)

### The PALM configuration file

- <https://palm.muk.uni-hannover.de/trac/wiki/doc/app/palm_config>

Running `PALM` with the `palmrun` script or compiling `PALM` with pal`m`build requires a **configuration file** in the working directory from where the scripts are called.

### how to set `-D__netcdf4_parallel`?

TODO.

- not parallel-netCDF

```log
942:netcdf_c_prefix  =                                                                                                                                                  
943:netcdf_fortran_prefix  =                                                                                                                                            

964:-- Found netCDF 4.7.4 compiled with mpicc                                                                                                                           

965:-- Found netCDF-Fortran 4.5.3 compiled with mpifort                                                                                                                 

966:-- Found NetCDF: /home/wpsze/MPAS-A/modules_library/Library/lib
libnetcdff.so                                                                                         

967:-- Fortran compiler matches netCDF Fortran compiler.
```

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

- with gcc, gxx, gfortran, mpich, ..., which are MPAS's requried library (like parallel-netCDF PnetCDF).

#### For ./palmplot

- use another conda env that contains `ncl`

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

### Possible issues

- `make[1]: *** [Makefile:60: kpp] Error 1`

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

{% fold info @run_palm.py %}
```python
#!/bin/bash

#------------------------------------------------#
#Author:         wpsze
#Email：         waipangsze@gmail.com
#date:           2023-08-31 10:39:15
#Version:        0.0 
#Description:    The purpose of the script
#Copyright (C)： 2023 All rights reserved
#------------------------------------------------#

# final environment variable setting
ulimit -s unlimited
export OMP_NUM_THREADS=1

source /home/wpsze/PALM/PALM_env_GNU.sh

source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate PALM

# set fftw lib path
export LD_LIBRARY_PATH=/home/wpsze/micromamba/envs/PALM/lib:$LD_LIBRARY_PATH

# set PALM bin path
export PATH=/home/wpsze/PALM/v25.04/bin:${PATH}

export palm_version="v25.04"

export install_prefix=/home/wpsze/PALM/${palm_version}
export PATH=${install_prefix}/bin:${PATH}

function palm_my_case(){
    echo "======== palm_my_case  ============"
    # my_test_case_cyclic or 
    case_name=my_test_case_cyclic 
    mkdir -p "${install_prefix}/JOBS/${case_name}/INPUT"
    cd ${install_prefix}
    palmrun -r ${case_name} -a "d3#" -X 4 -v

    # For noncyclic (1. pcr restart 2. d3# )
    # palmrun -r my_test_case_cyclic -a "pcr restart" -X 4 -v
    # palmrun -r ${case_name} -a "d3#  cyclic" -X 4 -v
}

function palm_flow_around_buildings(){
    echo "======== palm_flow_around_buildings case ============"
    # flow_around_cube_cyclic or flow_around_cube_noncyclic
    case_name=flow_around_cube_noncyclic 
    mkdir -p "${install_prefix}/JOBS/${case_name}/INPUT"
    cd ${install_prefix}
    # palmrun -r ${case_name} -a "d3#" -X 4 -v

    # For noncyclic (1. pcr restart 2. d3# )
    palmrun -r ${case_name} -a "pcr restart" -X 4 -v
    palmrun -r ${case_name} -a "d3#  cyclic" -X 4 -v
}

function palm_test(){
    echo "======== PALM test case ============"

    # https://palm.muk.uni-hannover.de/trac/attachment/wiki/doc/tut/palm/E-0_Installation.pdf

    cd ${install_prefix}
    mkdir -p "JOBS/example_cbl/INPUT"

    cp palm_model_system/packages/palm/model/tests/cases/example_cbl/INPUT/example_cbl_p3d JOBS/example_cbl/INPUT/
    palmrun -r example_cbl -c default -a "d3#" -X 4 -v -z
}

function palm_test_v0(){
    echo "======== PALM test case ============"
    cd ${install_prefix}

    palmtest --cases urban_environment_restart --cores 4

}

# --------------------------------- Main Control --------------------------------- #
start_time="$(date -u +%s)"
rm log.run_palm
exec > >(tee -a log.run_palm) 2>&1      # 
echo " ------------------------------  PALM ------------------------------- "
sleep 1s && echo

#palm_check
palm_test
#palm_test_v0

#palm_flow_around_buildings
# palm_my_case

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
echo " -------------------------- Completed PALM ------------------------- "

# --------------------------------- palmrun ? --------------------------------- #
#   *** Description of available palmrun options:

#       Option  Description                              Default-Value
#         -a    activation string list                   "" 
#         -A    project account number                   ---
#         -b    batch-job on local machine               ---
#         -B    do not delete temporary directory at end ---
#         -c    configuration identifier                 "default" 
#         -F    create batch job file only               ---                     
#         -k    keep data from previous run                                                   
#         -m    memory demand in MB (batch-jobs)         0 MB
#         -M    Makefile name                            Makefile
#         -O    threads per openMP task                  ---
#         -q    queue                                    "none" 
#         -r    run identifier                           test
#         -s    filenames of routines to be compiled     "" 
#               must end with .f, .f90, .F, or .c !
#               use ".." for more than one file and wildcards
#               -s LM compiles all locally modified files
#         -t    allowed cpu-time in seconds (batch)      0
#         -T    tasks per node                           ---
#         -u    username on remote machine               "" 
#         -v    no prompt for confirmation               ---
#         -V    check if SOURCES_FOR_RUN_... exists      ---
#         -w    maximum parallel io streams              as given by -X
#         -W    name of job to wait for                  ---
#         -x    tracing of palmrun for debug purposes    ---
#         -X    # of processors (on parallel machines)   1
#         -y    add appendix "_O" to all local output
#               files (ocean precursor runs followed by
#               coupled atmosphere-ocean runs)           ---
#         -Y    run coupled model, "#1 #2" with
#               #1 atmosphere and #2 ocean processors    "#/2 #/2" depending on -X
#         -Z    skip combine_plot_fields at the end of      
#               the simulation                           ---
  
#       Possible values of positional parameter <modus>:
#         "?"       -  this outline 
```
{% endfold %}

## palm_test()

- **palm_model_system/packages/palm/model/tests/cases/example_cbl/INPUT/example_cbl_p3d**

{% fold info @log.palm_test %}
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

### palmrun -r ${case_name} -a "pcr restart" -X 4 -v

{% fold info @log.flow_around_cube_noncyclic %}
```log
......
*** execution starts in directory
   "/home/wpsze/PALM/v25.04/tmp/flow_around_cube_noncyclic.26633"
----------------------------------------------------------------------------

*** execute command:
   "mpirun -n 4 ./palm" 
......
      [XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX]   0.0 left
   17:50:10   -finished-   time-stepping
   17:50:10   -start----   writing restart data
   17:50:10   -finished-   writing restart data
   17:50:10   -start----   calculating cpu statistics
   17:50:10   -finished-   calculating cpu statistics

  ----------------------------------------------------------------------------
  *** execution finished 
......
  *** saving OUTPUT-files:     local time: 17:50:11
  ----------------------------------------------------------------------------
  >>> OUTPUT: BINOUT  to
              /home/wpsze/PALM/v25.04/tmp/flow_around_cube_noncyclic/RESTART/flow_around_cube_noncyclic_d3d.002
      file will be linked

  >>> OUTPUT: PARIN  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/MONITORING/flow_around_cube_noncyclic_parin.003

  >>> OUTPUT: STDOUT  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/MONITORING/flow_around_cube_noncyclic_stdout.003

  >>> OUTPUT: RUN_CONTROL  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/MONITORING/flow_around_cube_noncyclic_rc.003

  >>> OUTPUT: HEADER  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/MONITORING/flow_around_cube_noncyclic_header.003

  >>> OUTPUT: CPU_MEASURES  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/MONITORING/flow_around_cube_noncyclic_cpu.003

  >>> OUTPUT: DATA_1D_PR_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/OUTPUT/flow_around_cube_noncyclic_pr.003.nc

  >>> OUTPUT: DATA_1D_TS_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/OUTPUT/flow_around_cube_noncyclic_ts.003.nc

  >>> OUTPUT: DATA_3D_AV_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/OUTPUT/flow_around_cube_noncyclic_av_3d.003.nc

  >>> OUTPUT: DATA_3D_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/OUTPUT/flow_around_cube_noncyclic_3d.003.nc

  ----------------------------------------------------------------------------
  *** all OUTPUT-files saved       local time: 17:50:12 

 --> palmrun finished
```
{% endfold %}

### palmrun -r ${case_name} -a "d3#  cyclic" -X 4 -v

{% fold info @log.flow_around_cube_noncyclic %}
```log
  *** providing INPUT-files:
  ----------------------------------------------------------------------------
  >>> INPUT: /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/INPUT/flow_around_cube_noncyclic_p3d  to  PARIN
  >>> INPUT: /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/INPUT/flow_around_cube_noncyclic_topo  to  TOPOGRAPHY_DATA
  >>> INPUT: /home/wpsze/PALM/v25.04/tmp/flow_around_cube_noncyclic/RESTART/flow_around_cube_noncyclic_d3d.002  to  BININ
      file will be linked
  *** INFORMATIVE: some optional INPUT-files are not present
  ----------------------------------------------------------------------------
  *** all INPUT-files provided 

   +++ warning message ---
      ID: PAC0192  generated by routine "do_topo_and_surface_setup":
      
      Topography and surface-setup output requires parallel netCDF. No output file will be created.
      
      Further information can be found at
      https://docs.palm-model.com/25.04/Reference/LES_Model/Logging/#PAC0192
......
 *** saving OUTPUT-files:     local time: 17:57:39
  ----------------------------------------------------------------------------
  >>> OUTPUT: PARIN  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/MONITORING/flow_around_cube_noncyclic_parin.004

  >>> OUTPUT: STDOUT  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/MONITORING/flow_around_cube_noncyclic_stdout.004

  >>> OUTPUT: RUN_CONTROL  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/MONITORING/flow_around_cube_noncyclic_rc.004

  >>> OUTPUT: HEADER  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/MONITORING/flow_around_cube_noncyclic_header.004

  >>> OUTPUT: CPU_MEASURES  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/MONITORING/flow_around_cube_noncyclic_cpu.004

  >>> OUTPUT: DATA_1D_PR_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/OUTPUT/flow_around_cube_noncyclic_pr.004.nc

  >>> OUTPUT: DATA_1D_TS_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/OUTPUT/flow_around_cube_noncyclic_ts.004.nc

  >>> OUTPUT: DATA_3D_AV_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/OUTPUT/flow_around_cube_noncyclic_av_3d.004.nc

  >>> OUTPUT: DATA_3D_NETCDF  to
              /home/wpsze/PALM/v25.04/JOBS/flow_around_cube_noncyclic/OUTPUT/flow_around_cube_noncyclic_3d.004.nc

  ----------------------------------------------------------------------------
  *** all OUTPUT-files saved       local time: 17:57:40 
```
{% endfold %}

![](https://i.imgur.com/Cbvmt4m.png)

### palmplot

```
#!/bin/bash

set -e # stop the shell on first error
#set -u # fail when using an undefined variable
set -x # echo script lines as they are executed
set -o pipefail

source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate venv
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

export PATH=/home/wpsze/PALM/v25.04/bin:${PATH}

# need NCL only

# Time series:
palmplot ts file_1=flow_around_cube_noncyclic_ts.000.nc format_out=pdf \
file_out=flow_around_cube_cyclic_plot_ts \
var='E E* umax vmax wmax' no_rows=5

# Profiles:
palmplot pr file_1=flow_around_cube_noncyclic_pr.000.nc format_out=pdf \
file_out=flow_around_cube_cyclic_plot_pr

# xy-cross sections:
palmplot xy file_1=flow_around_cube_noncyclic_av_3d.000.nc format_out=pdf \
file_out=flow_around_cube_cyclic_plot_xy var='u v w' \
no_rows=3 no_columns=2 sort=time mode='Both' zs=10 ze=10

# xz-cross sections:
palmplot xz file_1=flow_around_cube_noncyclic_av_3d.000.nc format_out=pdf \
file_out=flow_around_cube_cyclic_plot_xz var='u v w' \
no_rows=3 no_columns=2 sort=time mode='Both' ys=40 ye=40
```

# References

1. [热环境分析软件丨PALM城市微气候和空气质量大涡模拟](https://mp.weixin.qq.com/s/bgdvOYEkxwFUP-PYfMvUXg)
2. [大涡模拟（LES）在城市大气环境研究中的应用与挑战](https://mp.weixin.qq.com/s/wJ9k5VDCXURtQdPWI16jXw)
3. [热点论文丨基于大涡模拟与中尺度数值天气模式的精细化风场模拟](https://mp.weixin.qq.com/s/GvsJOrYhOefiIKBXuJaINA)