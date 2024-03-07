---
title: Start DM and WFS
keywords: 
last_updated: Feb 21, 2024
tags: [getting_started]
summary: "Starting DM and WFS"
sidebar: userguide_sidebar
permalink: cacao_start_DM_WFS.html
complex_map: true
map_name: runcacaoloopmap
box_number: 1
folder: cacao
---


## 1. Hardware mode: start DM process (if needed)

If in hardware mode, run hardware DM:
```bash
cacao-aorun-000-dm start
```
{% include warning.html content="
Do not run this command if the DM process is already running on the system. If in doubt, run milk-fpsCTRL and look for the entry DMch2disp-XX, where XX is the loop DM index. If it is present, you do not need to run the command.
" %}




## 2. Simulator mode

If in simulator mode, run simulation DM and WFS:

```bash
cacao-aorun-001-dmsim start
cacao-aorun-002-simwfs -w start
```



{% include links.html %}
