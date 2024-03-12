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

The `mfilt.comp.OLmodes` entry must be activated within the `mfilt` compute unit.



Accurate pOL telemetry is imperative for predictive control. The main sources of error in reconstructing pOL are WFS optical gain and DM-WFS latency. These are set as follows:
~~~bash
cacao-fpsctrl setval mfilt comp.WFSfact 0.8
cacao-fpsctrl setval mfilt comp.latencysoftwfr 1.5
~~~

`WFSfact` is the multiplicative factor applied to WFS signals (compared to the response matrix calibration). For example, here, the modal reconstruction is 0.8 times the actual optical disturbance. This is also refered to as the sensor optical gain.

`latencysoftwfr` is the software latency, which is summed with the hardware latency to yield the total latency. The hardware latency (parameter `latencyhardwfr`) is measured by the `mlat` compute unit - do not change it.

Change these two parameters until getting a good pOL match. To do so, close the loop and run the `cacao-aorun-080-testOL` script. Change the values of `WFSfact` and `latencysoftwfr` to get a good match for a range of `loopgain` and `loopmult` values representing both WFS-dominated (low gain, loopmult close to 0.0) and DM-dominated (high gain, loopmult close to 1.0) conditions.

~~~bash
cacao-fpsctrl setval mfilt loopgain 0.03
cacao-fpsctrl setval mfilt loopmult 0.999

# testOL sends probes to auxDMval, so it needs to be enabled
cacao-fpsctrl setval mfilt auxDMmval.enable ON
cacao-fpsctrl setval mfilt auxDMmval.mixfact 1.0
cacao-fpsctrl setval mfilt auxDMmval.modulate OFF


cacao-aorun-080-testOL -w 1.0

# repeat multiple times to converge to correct parameters
cacao-aorun-080-testOL -w 0.1
~~~

To inspect how well the pOL telemetry is reconstructed (probe and pOL should match):
~~~bash
gnuplot
plot [0:] "vispyr2-rundir/testOL.log" u 1:2 w l title "probe", "vispyr2-rundir/testOL.log" u ($1):5 title "psOL", "vispyr2-rundir/testOL.log" u ($1):3 title "DM"
quit
~~~

Once tuned, the pOL telemetry is accurately capturing input disturbances on stream `aolX_modevalOL`.


### 1.3. Running predictive filter computation (training)

Start process mctrlstats to split telemetry into blocks.

~~~bash
cacao-aorun-120-mstat start
~~~

The process writes pOL telemetry buffers named `aolX_modevalOLbuff_blkYY`, where X is the loop number and YY is the block index.

Start mkPFXX-Y processes.
~~~bash
cacao-aorun-130-mkPF 0 start
cacao-aorun-130-mkPF 1 start
~~~




### 1.3. Running predictive filter computation (inference)

Compute modal solution using predictive filters:
~~~bash
cacao-aorun-140-applyPF 0 start
cacao-aorun-140-applyPF 1 start
~~~

The predicted wavefront coefficients are written in stream `aolX_modevalPF`. To apply them to the AO correction, enable 

~~~bash
cacao-fpsctrl setval mfilt PF.enable ON

# should match inference number of blocks
cacao-fpsctrl setval mfilt PF.NBblock 2

# mixing coeff between PF and non-PF solutions
cacao-fpsctrl setval mfilt PF.mixcoeff 0.3
~~~




{% include links.html %}
