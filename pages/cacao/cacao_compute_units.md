---
title: Compute Units
keywords: CU
last_updated: Mar 13, 2024
tags: [CU]
summary: "A cacao loop is a collection of interconnected processes, each performing a step of the AO loop"
sidebar: userguide_sidebar
datatable: true
permalink: cacao_compute_units.html
folder: cacao
---



## 1. Overview

### 1.1. Compute Unit (CU)

cacao runs a collection of compute units to perform all required operations. A compute unit performs a specific step of the AO loop, such as extracting modal coefficients from the WFS signal stream.

Each <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.compute_unit}}">CU</a>  consists of a function parameter structure (FPS) holding the parameters for the computation, and a tmux sesssion within which code is executed. Two processes can run within a CU: the configuration process that manages function parameters, and the run process that executes the computation (often a loop in a real-time environment). The configuration and run processes are executed in tmux windows 1 and 2, with window 0 reserved for housekeeping.


### 1.2. Selecting CUs

The user must first select which CUs will be deployed.

<a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.compute_unit}}">CUs</a> are registered by adding entries in the `cacaovars.bash` file. For example, the following entry will register the mlat (measure latency) CU:
~~~bash
export CACAO_FPSPROC_MLAT="ON"
~~~

During deployment, the `cacao-fpslistadd` script is called to add registered CUs to the `fpslist.txt` ASCII file. The script calls all instances of `cacao-fpslistadd-` scripts, each one handling a single CU registration. For example, the mlat process is registered by `cacao-fpslistadd-MLAT`, which will test if the `CACAO_FPSPROC_MLAT` environment variable is set to ON, and register the CU if so.

`cacao-fpslistadd` is typically not called directly by the user, but is part of the `cacao-setup` (which reads the `cacaovars.bash` file prior to calling `cacao-fpslistadd`). The full list of registered CUs available, and their status (registered ?) can be inspected by running :
~~~bash
source cacaovars.LOOPNAME.bash; cacao-fpslistadd -l
~~~
Registered CUs will be labeled as ON, while others are labeled as OFF.


{% include note.html content="
Command `cacao-fpslistadd` scans for scripts matching `cacao-fpslistadd-*` and `milk-fpslistadd-*` both  in the milk intallation bin directory and the current directory. If deploying a custom CU (named for example CUSTOMCU), users should ensure the corresponding `cacao-fpslistadd-CUSTOMCU` is in the LOOPROOTDIR directory where `cacao-fpslistadd` is to be run.
" %}






### 1.3. CU categories

<div class="row">

<div class="col-md-3 col-sm-6">
<div class="panel panel-default text-center">
<div class="panel-heading">
<span class="fa-stack fa-4x">
<i class="fa fa-circle fa-stack-2x text-primary"></i>
<i class="fa fa-wrench fa-stack-1x fa-inverse"></i>
</span>
    </div>
    <div class="panel-body">
    <h4>System Setup</h4>
    <p>Connecting to devices, pre-processing</p>
    <a href="#" class="btn btn-primary">Learn More</a>
    </div>
</div>
</div>

<div class="col-md-3 col-sm-6">
<div class="panel panel-default text-center">
<div class="panel-heading">
<span class="fa-stack fa-4x">
<i class="fa fa-circle fa-stack-2x text-primary"></i>
<i class="fa fa-microchip fa-stack-1x fa-inverse"></i>
</span>
    </div>
    <div class="panel-body">
    <h4>Simulation</h4>
    <p>Simulating hardware devices</p>
    <a href="#" class="btn btn-primary">Learn More</a>
    </div>
</div>
</div>

<div class="col-md-3 col-sm-6">
<div class="panel panel-default text-center">
<div class="panel-heading">
<span class="fa-stack fa-4x">
<i class="fa fa-circle fa-stack-2x text-primary"></i>
<i class="fa fa-file fa-stack-1x fa-inverse"></i>
</span>
    </div>
    <div class="panel-body">
    <h4>Calibration</h4>
    <p>Measure and store system response</p>
    <a href="#" class="btn btn-primary">Learn More</a>
    </div>
</div>
</div>

<div class="col-md-3 col-sm-6">
<div class="panel panel-default text-center">
<div class="panel-heading">
<span class="fa-stack fa-4x">
<i class="fa fa-circle fa-stack-2x text-primary"></i>
<i class="fa fa-refresh fa-stack-1x fa-inverse"></i>
</span>
    </div>
    <div class="panel-body">
    <h4>Loop control</h4>
    <p>Closing and running the control loop</p>
    <a href="#" class="btn btn-primary">Learn More</a>
    </div>
</div>
</div>



<div class="col-md-3 col-sm-6">
<div class="panel panel-default text-center">
<div class="panel-heading">
<span class="fa-stack fa-4x">
<i class="fa fa-circle fa-stack-2x text-primary"></i>
<i class="fa fa-check fa-stack-1x fa-inverse"></i>
</span>
    </div>
    <div class="panel-body">
    <h4>Optimization</h4>
    <p>Monitoring and optimizing loop performance</p>
    <a href="#" class="btn btn-primary">Learn More</a>
    </div>
</div>
</div>


<div class="col-md-3 col-sm-6">
<div class="panel panel-default text-center">
<div class="panel-heading">
<span class="fa-stack fa-4x">
<i class="fa fa-circle fa-stack-2x text-primary"></i>
<i class="fa fa-database fa-stack-1x fa-inverse"></i>
</span>
    </div>
    <div class="panel-body">
    <h4>Telemetry</h4>
    <p>Recording and storing telemetry</p>
    <a href="#" class="btn btn-primary">Learn More</a>
    </div>
</div>
</div>


<div class="col-md-3 col-sm-6">
<div class="panel panel-default text-center">
<div class="panel-heading">
<span class="fa-stack fa-4x">
<i class="fa fa-circle fa-stack-2x text-primary"></i>
<i class="fa fa-flask fa-stack-1x fa-inverse"></i>
</span>
    </div>
    <div class="panel-body">
    <h4>Advanced</h4>
    <p>Advanced processes for expert users</p>
    <a href="#" class="btn btn-primary">Learn More</a>
    </div>
</div>
</div>


</div>




## 2. List of processes

### 2.1. System Setup

<div class="datatable-begin"></div>

| Name       | Description                           | cacaovar  | Notes    |
| ---------- | ------------------------------------- | --------- | -------- |
| DMch2disp- | DM control, manage and coadd channels | DMCH2DISP | Optional |
| acquWFS-   | Pre-process WFS input to WFS signal   | ACQUWFS   | Optional |

<div class="datatable-end"></div>



### 2.2. Simulation


<div class="datatable-begin"></div>

| Name                           | Description           | cacaovar  | Notes |
| ------------------------------ | --------------------- | --------- | ----- |
| simmvmgpu-                     | Simulate WFS response | SIMMVMGPU |
| Add simulated turbulence to DM | DMATMTURB             |
| Add delay to DM commands       | DMSIMDELAY            |
| Simulate WFS camera            | WFSCAMSIM             |

<div class="datatable-end"></div>





### 2.3. Calibration

<div class="datatable-begin"></div>

| Name         | Description                                | cacaovar       | Notes |
| ------------ | ------------------------------------------ | -------------- | ----- |
| mlat-        | Measure hardware latency                   | MLAT           |
| measlinresp- | Measure WFS linear response to DM commands | MEASURELINRESP |
| compstrCM-   | Compute simple control matrix              | COMPSTRCM      |

<div class="datatable-end"></div>



### 2.4. Loop Control



<div class="datatable-begin"></div>

| Name          | Description                                | cacaovar            | Notes |
| ------------- | ------------------------------------------ | ------------------- | ----- |
| wfs2cmodeval- | Compute modal coefficients from WFS signal | MVMGPU_WFS2CMODEVAL |
| mfilt-        | Modal filtering: compute modal control     | MODALFILTERING      |
| mvalC2dm-     | Compute DM correction from modal coeffs    | MVMGPU_CMODEVAL2DM  |

<div class="datatable-end"></div>



### 2.5. Performance Optimization, Monitoring






{% include links.html %}
