---
title: Zonal Filtering
keywords: optimization
last_updated: May 25, 2024
tags: [optimization]
summary: "Zonal Filtering (ZF) applies gain, leak and limit for each actuator. This is the zonal conterpart to modal filtering."
sidebar: userguide_sidebar
permalink: cacao_zonalfiltering.html
folder: cacao
---



## 1. Configuration

### 1.1. Compute Units to be registered (file `cacaovars.bash`)

To enable zonal filtering, the following compute units must be enabled:
- `CACAO_FPSPROC_ZONALFILTERING`: Perform zonal filtering
- `CACAO_FPSPROC_MVMGPU_DM2MVAL`: zonal to modal space translation of filtered output

The `cacaovars.bash` entries for enabling predictive control are:
~~~bash
export CACAO_FPSPROC_ZONALFILTERING="ON"
export CACAO_FPSPROC_MVMGPU_DM2MVAL="ON"
~~~


### 1.2. Turning zonal filtering on/off

The zonal filtering (ZF) compute unit inserts itself between the `mvalC2dm` and `DMch2disp` compute units. Without ZF, `mvalC2dm` writes to stream `aol_dmC`. With ZF, `mvalC2dm` is reconfigured to write to stream `aol_zvalDM`, and compute unit `zfilt` takes `aol_zvalDM` as input and writes to `aol_dmC`.

To switch ZF on/off:
```bash
cacao-aorun-zonalfiltering on
cacao-aorun-zonalfiltering off
```

## 2. Modal Feedback

Modal feedback is essential to AO loop stability when performing zonal filtering.

Without modal feedback, zonal filtering creates a discrepancy between the actual state of the DM correction and its modal representation `aol_modevalDM`. For example, when performing trunction of the command in zonal space, the modal control loop is unaware of the trunction, and can keep "pushing" the command beyond the trunction, creating a growing discrepancy between the modal representation and the actual DM correction. It is difficult for the control loop to recover from this discrancy.

The figure below and corresponding video show such instability when implementing zonal trunction of the output command. Notice how the loop becomes unstable once trunction occurs.

{% include image.html file="cacao_mfilt_zfilt_nomodalfeedback.png" caption="
Zonal clipping without modal feedback.
Left: Turbulence injected in AO loop input.
Center: DM correction.
Right: WF residual.
Click on image for video (external link, youtube)" url="https://youtu.be/Hs2Rw5ErcLM"
%}

With modal feedback implemented, the `mval2dm` converts the truncated zonal command back into modal space (stream `aol_modevalDMf`) at each loop interation. The `mfilt` compute unit detects that `aol_modevalDMf` has been updated, and uses it to uptdate its own internal DM correction state in modal space. The control loop is then aware of the truncation, avoiding loop instability. Compare the figure and video below (with modal feedback) from the one above.


{% include image.html file="cacao_mfilt_zfilt_modalfeedback.png" caption="
Zonal clipping with modal feedback.
Left: Turbulence injected in AO loop input.
Center: DM correction.
Right: WF residual.
Click on image for video (external link, youtube)" url="https://youtu.be/qUikshaJdzg"
%}






{% include links.html %}
