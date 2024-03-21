---
title: cacao - Getting Situated
last_updated: Feb 20, 2024
tags: [getting_started, setup, framework]
summary: "cacao - Getting Situated"
sidebar: userguide_sidebar
permalink: cacao_getting_situated.html
folder: cacao
---


CACAO is a plugin of the milk package, which provides the building blocks for deploying realtime processes with low-latency interprocess communication, and also provides the user interfaces and controls to configure and operate the AO control loop.


## 1. cacao Loops


CACAO can run multiple AO loops, which may or may not interact. Each loop has a unique number, name, and root directory. To see which loop(s) are currently installed on a system, run:

```bash
cacao-loops
```

An example output is:


<pre>
cacao loops on system :

LOOP  2 using DM 00 ( 336 x 1 )  NAME: maps
rootdir     : /mnt/data0/WORK/AOloop-cacao/maps-rootdir
Last access script : /usr/local/milk/bin/cacao-fpsctrl-logprocess
Last access time   : 2023-09-29 18:17:24.566123595 -1000

SCRIPT /usr/local/milk/bin/cacao-loops  Success
</pre>



## 2. Running Commands


Most operations are done by running cacao scripts within the loop root directory (shown as rootdir in the example above). To get a list of all cacao scripts, run:

```bash
cacao-commands
```

Each command can be safely run with the -h option to print help.
Most cacao commands start by scanning the local directory for required files and environment variables, and will exit if requirements are not satisfied. To run this check alone, run:

```bash
cacao-check-cacaovars
```

The command will fail if not run in a loop's rootdir. Running the command is a good way to check what type of AO loop is configured.

{% include image.html file="cacao-check-cacaovars.png" caption="Checking loop(s) deployed on system" %}



## 3. Deploying an AO loop


### 3.1. Deploying from an existing example
CACAO comes with a few example AO loops, used on actual AO systems and maintained by the corresponding AO groups. To deploy one of the examples, run cacao-loop-deploy. Run with the -h option for help and brief description of the examples:

```bash
cacao-loop-deploy -h
```

### 3.2. Custom deployment


The AO loop is deployed by processing a `cacaovars.LOOPNAME.bash` with the `cacao-setup` scripts.


The cacaovars file defines loop number, name, DM index, DM size, and which processes should be registered. For example, the cacaovars file below will deploy a modal control AO loop that processes input steam `wfscam` controlling a 50x50 actuator DM.


```bash
#!/usr/bin/env bash
# This file will be sourced by cacao-setup and high-level cacao scripts
export CACAO_LOOPNAME="myAOloop"
export CACAO_LOOPNUMBER="3"

# ====== DEFORMABLE MIRROR =========
# Deformable mirror (DM) size
export CACAO_DMINDEX="00"  # Hardware DM - connected to physical DM
export CACAO_DMxsize="50"
export CACAO_DMysize="50"  # If DM is single dimension, enter "1"
export CACAO_DMSPATIAL="1" # 1 if DM actuators are on a coordinate grid

# ====== DIRECTORIES ===============
export CACAO_LOOPROOTDIR="${CACAO_LOOPNAME}-rootdir" # Make sure the rootdir name mataches the directory in which this file is located
export CACAO_LOOPRUNDIR="${CACAO_LOOPNAME}-rundir" # processes run in CACAO_LOOPROOTDIR/CACAO_LOOPRUNDIR
export CACAO_LOOPDATALOGDIR="$(pwd)/datalogdir"

# ======== WFS =====================
export CACAO_WFSSTREAM="wfscam"         # input WFS stream
export CACAO_WFSSTREAM_PROCESSED="OFF"  # Raw image or processed WFS signal ? (ON: no intensity scaling)

# ==== Compute Units to be registered ====
export CACAO_FPSPROC_DMCH2DISP="ON"            # DM combination
export CACAO_FPSPROC_ACQUWFS="ON"              # Acquire WFS stream
export CACAO_FPSPROC_MVMGPU_WFS2CMODEVAL="ON"  # Extract control modes from WFS using MVM
export CACAO_FPSPROC_MODALFILTERING="ON"       # Modal control filtering
export CACAO_FPSPROC_MVMGPU_CMODEVAL2DM="ON"   # Compute DM command from control mode values

```


{% include warning.html content="The `CACAO_LOOPROOTDIR` entry in the cacaovars file must match the directory within which the cacaovars file is located, as shown in this example.
" %}

To deploy this loop within directory `myAOloop-rootdir`, run:
```bash
# Create rootdir
mkdir -p myAOloop-rootdir
cd myAOloop-rootdir
# copy the cacaovars file here
cp ../cacaovars.myAOloop.bash .
# Run deployment script
cacao-setup myAOloop
```

### 3.3. Cleanup and restart

{% include important.html content="Unless otherwise noted, commands are run from the LOOPROOTDIR directory.
" %}

To clean up (exit processes, remove FPSs, exit tmux sessions):
```bash
cacao-setup -C myAOloop
```

At any time, the update command can register missing compute units:
```bash
cacao-setup -u myAOloop
```
Run this command if a cumpute unit is added to cacaovars, or if a CU needs to be re-built.




{% include links.html %}
