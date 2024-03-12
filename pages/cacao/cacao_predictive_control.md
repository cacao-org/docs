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

Predictive control operates in modal space on pseudo-open loop (pOL) telemetry. pOL telemetry is reconstructed by adding DM corrections and WFS measurements with the appropriate time latency. 

The `mfilt.comp.OLmodes` entry must be activated within the `mfilt` compute unit:

~~~bash
cacao-fpsctrl setval mfilt auxDMmval.enable ON
cacao-fpsctrl setval mfilt auxDMmval.mixfact 1.0
cacao-fpsctrl setval mfilt auxDMmval.modulate OFF
~~~

Accurate pOL telemetry is imperative for predictive control. The main sources of error in reconstructing pOL are WFS optical gain and DM-WFS latency. These are set as follows:
~~~bash
cacao-fpsctrl setval mfilt comp.WFSfact 0.8
cacao-fpsctrl setval mfilt comp.latencysoftwfr 1.5
~~~

`WFSfact` is the multiplicative factor applied to WFS signals (compared to the response matrix calibration). For example, here, the modal reconstruction is 0.8 times the actual optical disturbance. This is also refered to as the sensor optical gain.




~~~bash
cacao-fpsctrl setval mfilt loopgain 0.03
cacao-fpsctrl setval mfilt loopmult 0.999

cacao-aorun-080-testOL -w 1.0

# repeat multiple times to converge to correct parameters
cacao-aorun-080-testOL -w 0.1
~~~






{% include links.html %}
