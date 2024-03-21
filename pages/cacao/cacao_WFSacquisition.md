---
title: WFS acquisition
keywords: 
last_updated: Feb 21, 2024
tags: [getting_started]
summary: "WFS acquisition: processing raw WFS input to computCACAO_FPSPROC_ACQUWFSe the WFS signal"
sidebar: userguide_sidebar
permalink: cacao_WFSacquisition.html
complex_map: true
map_name: runcacaoloopmap
box_number: 1
folder: cacao
---


## 1. ACQWFS compute unit

The raw WFS image is processed to compute a WFS signal. This is performed by compute unit `ACQWSF`. This step is optional if no pre-processing of the WFS input is required.

The `ACQWFS` CU performs the following steps.

### 1.1. Dark subtraction and multiplication -> imWFS0

Subtract stream `aolX_wfsdark` to input, amd multiply by `aolX_wfsmult`. Result is written to stream `aolX_imWFS0`.

This step is enabled by (set to OFF to disable):
~~~bash
cacao-fpsctrl setval milt comp.darksub ON
~~~

{% include note.html content="
If stream `aolX_wfsdark` does not exist, it will be created and set to zero. If stream `aolX_wfsmult` does not exist, it will be created and set to 1.0.
" %}


{% include note.html content="
If skipping this step (comp.darksub set to OFF), the WFS input will be copied to `aolX_imWFS0` without any change, other than type conversion to ensure `aolX_imWFS0` is in float32.
" %}



### 1.2. Flux Normalization -> imWFS1

Multiply stream `aolX_imWFS0` by `aolX_wfsmask`, normalize the result and write it to `aolX_imWFS1`.

This step is enabled by (set to OFF to disable) :
~~~bash
cacao-fpsctrl setval milt comp.WFSnormalize ON
~~~

{% include note.html content="
If stream `aolX_wfsmask` does not exist, it will be created and set to 1.0.
" %}


{% include warning.html content="
Flux normalization is WFS-specific. For example, if the WFS input is a centroid vector (SH type WFS), or a curvature signal, disable this step.
" %}


{% include note.html content="
If skipping this step (comp.WFSnormalize set to OFF), `aolX_imWFS0` will be copied to `aolX_imWFS1`.
" %}




### 1.3. Reference subtraction -> imWFS2

Subatract stream `aolX_wfsrefc` to `aolX_imWFS1`, write result to `aolX_imWFS2`.

This step is enabled by (set to OFF to disable) :
~~~bash
cacao-fpsctrl setval milt comp.WFSrefsub ON
~~~

{% include note.html content="
If stream `aolX_wfsrefc` does not exist, it will be created and set to 0.0.
" %}


{% include note.html content="
If skipping this step (comp.WFSnormalize set to OFF), `aolX_imWFS1` will be copied to `aolX_imWFS2`.
" %}


### 1.4. Time averaging -> imWFS3

{% include important.html content="
By default, stream `aolX_imWFS2` is the input to the wavefront reconstruction matrix-vector-multiply, so this time averaging is not applied to the real-time AO loop input, and will not slow down the loop.
" %}

The WFS signal imWFS2 may be time-averaged. This feature is enabled by :
~~~bash
cacao-fpsctrl setval milt comp.WFSsigav ON
~~~

The time aveaged output is useful for monitoring, and used by optional advanced AO loop contorl techniques, such as ensuring that the time-averaged AO correction is unbiased (zero mean).




## 2. Scripts

### 2.1. Taking the WFS dark

If the WFS input has a bias and/or dark, acquire the "dark", which is the WFS input when no signal is present:

```bash
# -n option: number of frames averaged
cacao-aorun-005-takedark -n 2000
```

Make sure to turn off or block light source before running the `cacao-aorun-005-takedark` command, and remember to turn on / unblock after done.


### 2.2. Starting WFS acquisition

```bash
cacao-aorun-025-acqWFS -w start
```

### 2.3. Acquiring the WFS reference

This is the WFS response to a flat (unaberrated) input wavefront, and defines the convergence point for the AO loop. This step can be skipped is the WFS input is already referenced to zero for a flat wavefront.

```bash
cacao-aorun-026-takeref -n 2000
```


{% include links.html %}
