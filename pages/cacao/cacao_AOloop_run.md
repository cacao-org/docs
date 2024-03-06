---
title: Running the AO loop
keywords:
last_updated: Feb 21, 2024
tags: [CPU, GPU]
summary: "Running the AO loop: processes."
sidebar: home_sidebar
permalink: cacao_AOloop_run.html
complex_map: true
map_name: runcacaoloopmap
box_number: 3
folder: cacao
---

Select GPUs for the modal decomposition (WFS->modes) and expansion (modes->DM) MVMs

```bash
# MVMs on CPU (GPUindex = 99)
# if on GPU, set to GPUindex=0 or device index
#
cacao-fpsctrl setval wfs2cmodeval GPUindex 99
cacao-fpsctrl setval mvalC2dm GPUindex 99
```


## Start WFS -> mode coefficient values

```bash
cacao-aorun-050-wfs2cmval start
```


## Start modal filtering

```bash
cacao-aorun-060-mfilt start
```


## Start mode coeff values -> DM

```bash
cacao-aorun-070-cmval2dm start
```


## Closing the loop and setting loop parameters with mfilt

```bash
# Set loop gain
cacao-fpsctrl setval mfilt loopgain 0.1
# Set loop mult
cacao-fpsctrl setval mfilt loopmult 0.98
# Close loop
cacao-fpsctrl setval mfilt loopON ON
```


{% include links.html %}
