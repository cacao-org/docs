---
title: cacao processes
keywords: processes
last_updated: Mar 6, 2024
tags: [processes]
summary: "A cacao loop is a collection of interconnected processes, each performing a step of the AO loop"
sidebar: userguide_sidebar
datatable: true
permalink: cacao_processes.html
folder: cacao
---



## 1. Overview


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
