---
title: cacao processes
keywords: processes
last_updated: Mar 6, 2024
tags: [processes]
summary: "A cacao loop is a collection of interconnected processes, each performing a step of the AO loop"
sidebar: home_sidebar
datatable: true
permalink: cacao_processes.html
folder: cacao
---

<div class="datatable-begin"></div>

Name           | Description                                 | cacaovar                | Category
-------------- | ------------------------------------------- | ----------------------- | ------ 
DMch2disp-     | DM control, manage and coadd channels       | DMCH2DISP       | system
acquWFS-       | Pre-process WFS input to WFS signal         | ACQUWFS         | system
wfs2cmodeval-  | Compute modal coefficients from WFS signal  | MVMGPU_WFS2CMODEVAL | control
mfilt-         | Modal filtering: compute modal control      | MODALFILTERING | control
mvalC2dm-      | Compute DM correction from modal coeffs     | MVMGPU_CMODEVAL2DM | control
mlat-          | Measure hardware latency                    | MLAT            | calibration
measlinresp-   | Measure WFS linear response to DM commands  | MEASURELINRESP  |calibration
compstrCM-     | Compute simple control matrix               | COMPSTRCM       | calibration
simmvmgpu-     | Simulate WFS response                       | SIMMVMGPU       | simulation
               | Add simulated turbulence to DM              | DMATMTURB       | simulation
               | Add delay to DM commands                    | DMSIMDELAY      | simulation
               | Simulate WFS camera                         | WFSCAMSIM       | simulation

<div class="datatable-end"></div>




{% include links.html %}
