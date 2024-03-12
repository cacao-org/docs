---
title: Predictive Control
keywords: optimization
last_updated: Mar 11, 2024
tags: [optimization]
summary: "Predictive Control"
sidebar: userguide_sidebar
permalink: cacao_predictive_control.html
folder: cacao
---


## 1. Configuration

### 1.1. Compute Units to be registered (file `cacaovars.bash`)

To enable predictive control, the following compute units must be enabled:
- `CACAO_FPSPROC_MKPF`: Build predictive control filter(s)
- `CACAO_FPSPROC_APPLYPF`: Apply predictive control filter(s)

Each instance of the MKPF and APPLYPF compute units handles a set number of control modes. For high-order AO systems, there may be multiple instances of MKPF/APPLYPF pairs, each handling a subset of the control modes. The number of such pairs is set by variable `CACAO_PF_NBBLOCK`.

The `cacaovars.bash` entries for enabling predictive control may be:
~~~bash
export CACAO_PF_NBBLOCK=6
export CACAO_FPSPROC_MKPF="ON"
export CACAO_FPSPROC_APPLYPF="ON"
~~~

In this example, there are 6 blocks of modes. The first block may be allocated to Tip+Tilt, and subsequent blocks may handle groups of 100 modes for example.

### 1.2. Pseudo-Open Loop Telemetry

Predictive control operates in modal space on pseudo-open loop telemetry. The `mfilt.comp.OLmodes` entry must be activated within the `mfilt` compute unit.








{% include links.html %}
