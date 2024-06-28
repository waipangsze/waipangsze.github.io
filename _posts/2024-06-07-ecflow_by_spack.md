---
layout: article
title: ecFlow -- Installation & Usage
categories: [MPAS]
tags: [ecFlow, Spack, Micromamba, Conda, Installation]
author: wpsze
mathjax: true
mathjax_autoNumber: true
---

# Using Spack

Spack is a package management tool designed to support multiple versions and configurations of software on a wide variety of platforms and environments. It was designed for large supercomputing centers, where many users and application teams share common installations of software on clusters with exotic architectures, using libraries that do not have a standard ABI. Spack is non-destructive: installing a new version does not break existing installations, so many configurations can coexist on the same system.
Most importantly, Spack is simple. It offers a simple spec syntax so that users can specify versions and configuration options concisely. Spack is also simple for package authors: package files are written in pure Python, and specs allow package authors to maintain a single file for many different builds of the same package.

<https://spack.readthedocs.io/en/latest/>

## Search packages online

> <https://packages.spack.io/>
> <https://packages.spack.io/package.html?name=ecflow>

# Install 

```sh
$ spack env create ecFlow 
$ spack env activate ecFlow
$ spack info --all ecflow
```

shows,

```sh
 $ spack info --all ecflow
CMakePackage:   ecflow

Description:
    ecFlow is a work flow package that enables users to run a large number
    of programs (with dependencies on each other and on time) in a
    controlled environment. It provides tolerance for hardware and software
    failures, combined with good restart capabilities.

Homepage: https://confluence.ecmwf.int/display/ECFLOW/

Maintainers: @AlexanderRichert-NOAA @climbfuji

Externally Detectable: 
    False

Tags: 
    None

Preferred version:  
    5.11.4    https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-5.11.4-Source.tar.gz

Safe versions:  
    5.11.4    https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-5.11.4-Source.tar.gz
    5.8.4     https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-5.8.4-Source.tar.gz
    5.8.3     https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-5.8.3-Source.tar.gz
    4.13.0    https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-4.13.0-Source.tar.gz
    4.12.0    https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-4.12.0-Source.tar.gz
    4.11.1    https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-4.11.1-Source.tar.gz
```

then, install,

```sh
$ spack install --add ecflow@5.11.4
```

check version,

```sh
$ ecflow_client --version
```

To uninstall a package, type spack uninstall package. This will ask the user for confirmation before completely removing the directory in which the package was installed.

```sh
spack uninstall --dependents ecflow@5.11.4
```

# Error

Not successful. 

- [Installation issue: ecflow@5.11.4 with boost@1.85.0 #44116](https://github.com/spack/spack/issues/44116)
- [Bug fix for ecflow: versions up to 5.11.4 require boost 1.84 or earlier #44181](https://github.com/spack/spack/pull/44181)
- [Merge spack develop as of 2024/05/09 into spack-stack-dev #432](https://github.com/JCSDA/spack/pull/432)

## Try with boost@1.84.0

```sh
 $ spack install --add ecflow%gcc ^boost@1.84.0
==> Concretized ecflow%gcc ^boost@1.84.0
......
==> Installing boost-1.84.0-7awv37536onadsx3urg2sfra7hippfov [29/33]
==> No binary for boost-1.84.0-7awv37536onadsx3urg2sfra7hippfov found: installing from source
==> Using cached archive: //wpsze/spack/var/spack/cache/_source-cache/archive/cc/cc4b893acf645c9d4b698e9a0f08ca8846aa5d6c68275c14c3e7949c24109454.tar.bz2
==> Applied patch //wpsze/spack/var/spack/repos/builtin/packages/boost/python_jam-1_77.patch
==> Applied patch //wpsze/spack/var/spack/repos/builtin/packages/boost/bootstrap-compiler.patch
==> Ran patch() for boost
==> boost: Executing phase: 'install'
==> boost: Successfully installed boost-1.84.0-7awv37536onadsx3urg2sfra7hippfov
  Stage: 29.98s.  Install: 2m 23.30s.  Post-install: 1m 8.91s.  Total: 4m 6.00s
[+] //wpsze/spack/opt/spack/linux-rocky8-haswell/gcc-8.5.0/boost-1.84.0-7awv37536onadsx3urg2sfra7hippfov
[+] //wpsze/spack/opt/spack/linux-rocky8-haswell/gcc-8.5.0/py-pip-23.1.2-7d22lvklf5zxutex5ggyhxp3oxpqhzai
[+] //wpsze/spack/opt/spack/linux-rocky8-haswell/gcc-8.5.0/py-setuptools-69.2.0-axw5axor273y224weubpqloritokktqx
[+] //wpsze/spack/opt/spack/linux-rocky8-haswell/gcc-8.5.0/py-numpy-1.26.4-3vq4eb6gkjjmc3i2pzn4y4pjqscg5bys
==> Installing ecflow-5.11.4-4chvjzr5j33mtu5m3effo7lugzwskirx [33/33]
==> No binary for ecflow-5.11.4-4chvjzr5j33mtu5m3effo7lugzwskirx found: installing from source
==> Using cached archive: //wpsze/spack/var/spack/cache/_source-cache/archive/48/4836a876277c9a65a47a3dc87cae116c3009699f8a25bab4e3afabf160bcf212.tar.gz
==> Applied patch //wpsze/spack/var/spack/repos/builtin/packages/ecflow/ctsapi_cassert.patch
==> ecflow: Executing phase: 'cmake'
==> ecflow: Executing phase: 'build'
==> ecflow: Executing phase: 'install'
==> ecflow: Successfully installed ecflow-5.11.4-4chvjzr5j33mtu5m3effo7lugzwskirx
  Stage: 1.25s.  Cmake: 19.16s.  Build: 3m 47.02s.  Install: 4.49s.  Post-install: 1.60s.  Total: 4m 20.25s
[+] //wpsze/spack/opt/spack/linux-rocky8-haswell/gcc-8.5.0/ecflow-5.11.4-4chvjzr5j33mtu5m3effo7lugzwskirx
==> Updating view at //wpsze/spack/var/spack/environments/ecFlow/.spack-env/view
```

**done** and check,

```sh
 $ ecflow_client --version
Ecflow version(5.11.4) boost(1.84.0) compiler(gcc 8.5.0) protocol(JSON cereal 1.3.0) openssl(enabled) Compiled on Jun 10 2024 00:11:14
```

# By micromamba *****

```sh
$ micromamba create -n ecFlow
Empty environment created at prefix: /home/wpsze/micromamba/envs/ecFlow
$ micromamba activate ecFlow
|ecFlow|wpsze:/home/wpsze $ micromamba install ecflow -c conda-forge
|ecFlow|wpsze:/home/wpsze $ ecflow_client --version
Ecflow version(5.13.0) boost(1.84.0) compiler(gcc 12.3.0) protocol(JSON cereal 1.3.0) openssl(enabled) Compiled on Jun 20 2024 08:28:01

 $ ecf*
ecflow_client        ecflow_http          ecflow_logsvr.pl     ecflow_standalone    ecflow_stop.sh       
ecflow_udp_client    ecflow_ui.x
ecflow_fuse.py       ecflow_logserver.sh  ecflow_server        
ecflow_start.sh      ecflow_udp           ecflow_ui
```



and it installs,

```sh
  + libboost-python                  1.84.0  py312h389efb2_3      conda-forge      124kB
  + libboost-python-devel            1.84.0  py312h9cebb41_3      conda-forge       20kB
  + ecflow                           5.13.0  py312hc3ad225_0      conda-forge       17MB
```

where boost is 1.84.0

# ecFlow

Create the following subdirectories in this directory:

**def**: ecf script file that stores ecFlow workflow definition files and tasks

**ecfout**: The directory where the ecFlow service operates. The logs of the ecFlow service will be stored in this directory.

**Programs**: Save mode programs, profiles, and scripts

**workdir**: directory where the mode operates

```sh
mkdir -p def
mkdir -p ecfout
mkdir -p program
mkdir -p workdir
```

## ecFlow Quickstart
[ecFlow Quickstart](https://confluence.ecmwf.int/display/ECFLOW/ecFlow+Quickstart)

```sh
 $ ll ./ecflow_quickstart  
total 160K
drwxrwxr-x 3 wpsze wpsze  33K Jun  7 22:06 ./
drwxrwxr-x 3 wpsze wpsze  33K Jun  7 22:06 ../
-rw-rw-r-- 1 wpsze wpsze 2.4K Jun  7 17:51 ecflow_quickstart.tar.gz
-rw-r--r-- 1 wpsze wpsze 1.8K Nov  2  2022 head.h
-rw-r--r-- 1 wpsze wpsze  760 Nov  2  2022 instructions.txt
-rw-r--r-- 1 wpsze wpsze  209 Nov  1  2022 tail.h
drwxr-xr-x 2 wpsze wpsze  33K Nov  2  2022 test/
-rw-r--r-- 1 wpsze wpsze  222 Nov  2  2022 test.def

 $ ll test
total 165K
drwxr-xr-x 2 wpsze wpsze  33K Nov  2  2022 ./
drwxrwxr-x 3 wpsze wpsze  33K Jun  7 22:06 ../
-rw-r--r-- 1 wpsze wpsze  627 Nov  2  2022 t1.1
-rw-r--r-- 1 wpsze wpsze  877 Nov  2  2022 t1.2
-rw-r--r-- 1 wpsze wpsze  119 Nov  2  2022 t1.ecf
-rwxr-xr-x 1 wpsze wpsze 2.1K Nov  2  2022 t1.job1*
-rwxr-xr-x 1 wpsze wpsze 2.1K Nov  2  2022 t1.job2*
-rw-r--r-- 1 wpsze wpsze  627 Nov  2  2022 t2.1
-rw-r--r-- 1 wpsze wpsze  118 Nov  2  2022 t2.ecf
-rwxr-xr-x 1 wpsze wpsze 2.1K Nov  2  2022 t2.job1*

 $ cat test/t1.ecf 
%include "../head.h"

echo "I, %ECF_NAME%, am part of a suite that lives in %ECF_HOME%"
sleep 2

%include "../tail.h"

 $ cat test/t2.ecf 
%include "../head.h"

echo "I, %ECF_NAME%, am part of a suite that lives in %ECF_HOME%"
sleep 2

%include "../tail.h"
```

### Inspect and update the suite definition
The file **test.def** defines a suite called "test". Open this file in a text editor and note the structure.

```sh
suite test
   edit ECF_HOME "/replace/with/own/home/ecflow/quickstart"
   task t1
   task t2
        trigger t1 eq complete
endsuite
```

We define a suite called "test"; it defines a variable called **ECF_HOME**, then it defines two tasks **("tasks" are things that actually run)** and specifies that **task t2 will be run as soon as task t1 has completed**. The scripts corresponding to the tasks are specified in **".ecf"** files in the test directory. Have a look - they simply print some information about themselves, then sleep for 2 seconds.

The first thing you must do is to complete the path to your working directory in the **ECF_HOME** definition in test.def and save the file.

### Start an ecFlow server
We will start a **new running instance of an ecFlow server using the default port**. It is possible to use a different port by **adding --port=3500 (for example)** to every ecFlow command-line action. Note also that we start it as a background task - it will run until the server is stopped. It can be run in the foreground, but in that case you will need to use a new terminal for any subsequent commands!

To start the ecflow server:

```sh
$ ecflow_server &
or
$ export ECF_PORT=2500
$ ecflow_start.sh -p $ECF_PORT
```

Check that it is running:

```sh
$ ecflow_client --ping
ping server(localhost:3141) succeeded in 00:00:00.034207  ~34 milliseconds
```

or check

```sh
 $ ps -u wpsze -f | grep ecflow | grep -v grep
wpsze    3248171 3700943  0 14:26 pts/6    00:00:00 ecflow_server
```

Please note **“Host”** and **“Port Number”** here. Also note that each user must use a unique port number (we recommend using a random number between 2500 and 9999)

To view the ecflow GUI:

```sh
ecflow_ui &
```

When opening the ecflow GUI flow for the first time you will need to add your server to the GUI. In the GUI click on **“Servers” and then “Manage servers”**. A new window will appear. Click on **“Add server”. Here you need to add the Name, Host, and Port of your server**. For “Host” and “Port” please refer to the last section of output from the previous step.

To stop the ecflow server:

```sh
ecflow_stop.sh -p $ECF_PORT
```

e.g.

```sh
 $ ecflow_stop.sh -p 3141
Fri Jun 28 08:57:33 UTC 2024

User "10004" attempting to stop ecf server on ip:3141

Checking if the server is running on ip:3141
ping server(ip:3141) succeeded in 00:00:00.011797  ~11 milliseconds

Halting, check pointing and terminating the server
[1]+  Done                    ecflow_server
```

and then,

```sh
$ ps -u wpsze -f | grep ecflow | grep -v grep
``` 
has shown nothing now.

### Load your suite definition into the server

```sh
$ ecflow_client --load=test.def
```

Check that it is loaded by asking the server to give you back the suite definition:

```sh
$ ecflow_client --get
#5.11.4
suite test
  edit ECF_HOME '/EM/wpsze/ecflow/ecflow_quickstart/'
  task t1
  task t2
    trigger t1 eq complete
endsuite
# enddef
```

### Monitor and interact via the GUI
#### Start ecFlowUI:

```sh
$ ecflow_ui &
```

Once ecFlowUI has started, you must tell it how to reach your server. Go to the 

Servers → Manage Servers menu, click "Add server", then enter the details of your server. Name can be anything you want - it's for you to identify the server to your self; something like "localtest" would be fine here. Host should in this case be "localhost", and Port should be XXX. The other fields can be left blank, but keep the "Add server to current view" box ticked.

You should now see your suite loaded into the GUI! To make the server active, right-click on the top-level node representing the server ("localtest in our case) and choose "Restart". Now right-click on the "test" node and choose "Begin" to make the suite active. The default behaviour is to only refresh its view of the suite every 60 seconds, so you will need to click the green refresh button at the top every so often to see the progress of the tasks.
