---
title: WFS reference
keywords: reference
last_updated: Mar 23, 2024
tags: [reference]
summary: "The WFS reference defines where the AO loop is converging to. The WFS reference can be static or dynamic."
sidebar: userguide_sidebar
permalink: cacao_wfsref.html
folder: cacao
---

## 1. Overview





## 2. Zero Point Offsetting (zpo)

Zero-Point Offsetting (zpo) allows for some DM channels to NOT be corrected by the AO loop. When writing a DM command as a zero-point offset, the command will be applied to the convergence point of the loop instead of being corrected. This is done by changing the WFS reference.

Zero point offsetting is handled by 3 processes:
- DMch2disp to select and sum DM channels to the dmzpo DM-space offset. The zpo-selected channels will be added onto stream `aolX_dmzpo`.
- zpo to convert DM-space offset `aolX_dmzpo` to WFS-space offset `aolX_wfszpo`.
- acquWFS to apply `aolX_wfszpo` to the reference


### 2.1. Selecting DM channels for offsetting

Select DM channels to be included in zpo within process .
```bash
cacao-fpsctrl setval DMch2disp zpoffset.ch08 ON
cacao-fpsctrl setval DMch2disp zpoffset.ch09 ON
```

### 2.2. Converting DM-space offset to WFS-space offset

The ZPO process takes as input stream aol_dmzpo and computes the correspoding WFS-space reference offset aol_wfszpo by multiplication by the zonal response matrix. To start the process:
```bash
cacao-aorun-071-zpo start
```

### 2.3. Applying WFS-space offset to WFS reference

ZPO is applied by subtracting aol_wfszpo from aol_wfsref, with the result written in aol_wfsrefc.

```bash
cacao-fpsctrl setval acquWFS WFSrefcmult 1.0
cacao-fpsctrl setval acquWFS WFSrefcgain 0.0
cacao-fpsctrl setval acquWFS comp.WFSrefc ON
```



## 3. Unbiased Correction (zero-mean correction)

Focring the average correction to be zero is useful to remove artefacts such as strong correction at the edges of the beam due to slight misalignment between calibration and operation, or to adapt to straylight (for example moonlight) when observing a faint target.

This is done from the acquWFS process, as follows:

```bash
cacao-fpsctrl setval acquWFS WFStaveragegain 0.01
cacao-fpsctrl setval acquWFS WFStaveragemult 0.999
cacao-fpsctrl setval acquWFS WFSrefcmult 0.0
cacao-fpsctrl setval acquWFS WFSrefcgain 0.01
cacao-fpsctrl setval acquWFS comp.WFSrefc ON
```

These settings will time-average imWFS2 to imWFS3, and drive wfsrefc to imWFS3.

Note that this mode and the zero-point offsetting described in the following section are mutually exclusive.

To start/stop this reference update, run :

```bash
cacao-fpsctrl setval acquWFS comp.WFSrefc ON
cacao-fpsctrl setval acquWFS comp.WFSrefc OFF
```

To revert to the wfsref reference:

```bash
cacao-fpsctrl setval acquWFS WFSrefcmult 1.0
cacao-fpsctrl setval acquWFS WFSrefcgain 0.0
cacao-fpsctrl setval acquWFS comp.WFSrefc ON
```



{% include links.html %}
