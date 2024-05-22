---
title: Optimizing Control Modes
keywords: controlmodes
last_updated: May 21, 2024
tags: [advanced]
summary: "Optimizing control modes."
sidebar: userguide_sidebar
permalink: cacao_optimizing_control_modes.html
folder: cacao
---






{% include note.html content="
This page refers to tools described in the Building Control Modes page. We consider here the optimization of a system with ~1000 control modes with a spatial DM. Control modes are computed by blocks to group modes by spatial frequencies.
" %}





### 1. How to set SVD limit?

Once the blocks have been defined, the SVD limit setting must be tuned to remove poorly sensed modes.
A high value of `svdlim` will reject more modes, and achieve better control loop stability over the modes that are controlled. The drawback of a high `svdlim` value is that some input WF errors may not be corrected.

It is good practice to check how many modes are kept after SVD, and compare it to the expected number of modes that need to be corrected.

```bash
# Compute mode block 03 control modes with svdlim=0.02
cacao-fpsctrl setval compstrCM svdlim 0.02
cacao-aorun-039-compstrCM -mb 03 -mr 11:50
# Check number of modes
fitsheader conf/CMmodesWFS/CMmodesWFS.03.fits | grep NAXIS3
# 40 control modes
```
{% include image.html file="cacao_AOmodes_optimization_example40modes.png"
caption="AO control modes for svdlim=0.02 setting. Left: DM-space cross product. Right: WFS-space cross product." %}


With this `svdlim=0.02` settings, no mode is rejected (input dimension = output dimension = 40). The figure above shows the DM-space and WFS-space cross products, written as file `conf/CMmodesDM/CMmodesDM.03.fits` and `conf/CMmodesWFS/CMmodesWFS.03.fits`. The SVD is performed in WFS-space, so the WFS-space cross product is a pure diagonal. Since the cacao convention is that modes are normalized to unity RMS amplitude in DM space, the DM-space cross-product matrix has constant value on the diagonal, equal to the number of active actuators in `conf/dmmask.fits` (value = 1382 in this example).

At higher mode numbers (top right part of the matrices), we can see two issues indicative of poor/unstable AO loop performance:

<span class="label label-danger">DM modes cross-talk</span>
Off-diagonal terms in the DM-space cross-product become large, with absolute values reaching 0.7x the diagonal value. Large off-diagonal values mean that correcting a mode will excite other modes, rendering the loop unstable at high gain.

<span class="label label-danger">Weak WFS modes</span>
Diagonal values in the WFS-space cross-product become small, down to 4% of the peak value (first element of the diagonal). A small diagonal value here corresponds to a mode that is weakly sensed, so it can be easily excited by WFS noise, WFS nonlinearities of WFS crosstalk.

{% include tip.html content="
To ensure a stable AO loop, off-diagonal elements in the DM-space cross-product should be below ~50% of the diagonal value, and the weakest diagonal elements in the WFS-space cross-product should be no less than ~10% of the peak value.
" %}


Let's try a larger `svdlim` value to remove the problematic modes.

```bash
# Trying svdlim=0.2
cacao-fpsctrl setval compstrCM svdlim 0.2
cacao-aorun-039-compstrCM -mb 03 -mr 11:50
# Check number of modes
fitsheader conf/CMmodesWFS/CMmodesWFS.03.fits | grep NAXIS3
# 25 control modes
```
{% include image.html file="cacao_AOmodes_optimization_example25modes.png"
caption="AO control modes for svdlim=0.2 setting. Left: DM-space cross product. Right: WFS-space cross product." %}

We now have a smaller number of control modes (25 instead of 40), but with cleaner cross-product matrices: largest off-diagonal DM-space value is 45% of peak, and smallest WFS-space value is 14% of peak.



{% include note.html content="
Modal control can be very helpful to postpone the sdvlim value tradeoff, by keeping a large number of modes (small svdlim) and zeroing modal gains on the last modes to exclude them during AO loop operation. The main drawback of this option is the additional compute power required to run the loop.
" %}


## 2. Optimizing blocks

Once we have defined the control mode blocks (typically a range of spatial frequencies) and optimized `svdlim` for each block, we must combine the blocks.


### 2.1. Simple merge
The simplest approach is to join all control modes, pasting together all blocks. For example:
```bash
cacao-aorun-039-compstrCM -mbm 00:01:02:03:04
```
This is almost always a **bad approach**, as modes are generally not orthogonal in WFS space between blocks, so we will encounter the <span class="label label-danger">DM modes cross-talk</span> issue between modes belonging to different blocks.


### 2.2. Marginalizing in WFS space

To address the <span class="label label-danger">DM modes cross-talk</span> issue, we can enforce that each mode block is marginalized against previous blocks in WFS space. In this example, we also enforce that the first two modes are pure tip and tilt.


```bash
# Compute CM for tip-tilt (first 2 modes of modesZF, so use range = 1:2)
# Write output to mode block 00
cacao-aorun-039-compstrCM -mb 00 -mr 1:1
cacao-aorun-039-compstrCM -mb 01 -mr 2:2

# block 01: LO modes, marginalized against TT
cacao-aorun-039-compstrCM -mb 02 -mr 3:10 -marg 00:01

# block 02: MO modes, marginalized against TT and LO
cacao-aorun-039-compstrCM -mb 03 -mr 11:50 -marg 00:01:02

# block 03: HO modes, marginalized against TT, LO, M)
cacao-aorun-039-compstrCM -mb 04 -mr 51:1249 -marg 00:01:02:03

# Merge
cacao-aorun-039-compstrCM -mbm 00:01:02:03:04
```


### 2.3. DM-space Marginalizing

Modes may need to be orthogonalized in DM space as well. For example, we may want to ensure that the first two modes are tip-tilt and all other modes be orthrogonal to tip-tilt in DM space.

```bash
# Compute CM for tip-tilt (first 2 modes of modesZF, so use range = 1:2)
# Write output to mode block 00
cacao-aorun-039-compstrCM -mb 00 -mr 1:1
cacao-aorun-039-compstrCM -mb 01 -mr 2:2

# block 01: LO modes, marginalized against TT
cacao-aorun-039-compstrCM -mb 02 -mr 3:10 -marg 00:01 -margDM 00:01

# block 02: MO modes, marginalized against TT and LO
cacao-aorun-039-compstrCM -mb 03 -mr 11:50 -marg 00:01:02 -margDM 00:01

# block 03: HO modes, marginalized against TT, LO, M)
cacao-aorun-039-compstrCM -mb 04 -mr 51:1249 -marg 00:01:02:03 -margDM 00:01

# Merge
cacao-aorun-039-compstrCM -mbm 00:01:02:03:04
```

Block-marginalization can be done in WFS space, DM space, or both. If selecting both WFS and DM space marginalization, the DM-space marginalization will be performed first with the `cacao-aorun-039-compstrCM` script.


### 2.4. Example and Discussion

{% include image.html file="cacao_AOmodes_marg_options.png"
caption="AO control modes block-orthogonalization options. See text for details." %}

The figure above shows the result of SVD-based WFS-space mode selection with `svdlim=0.2` for each block, and different block marginalization options. Marginalizations are performed on the input modes prior to SVD computation.

From left to right:
- DM-space cross product
- WFS-space cross product
- selfRM, obtained after closint the loop with gain=0

From top to bottom:
1. No marginalization
2. WFS-space marginalization of each block against the preceeding ones
3. DM-space marginalization of each block against the preceeding ones
4. WFS-space marginalization followed by DM-space marginalization
5. DM-space marginalization followed by WFs-space marginalization

The loop was closed in each of the five cases, with a 1% leak (`mult=0.99`) on all modes, and all modes at the same gain value.

1. Without marginalization, 585 modes are controlled. The loop becomes unstable on tip-tilt at gain=0.28. This is consistent with the strong off-diagonal DM-space values on tip-tilt, where tip-tilt DM modes are excited by modes in block02.
2. With WFS-space marginalization, 487 modes are controlled. The loop becomes unstable at g=0.60 on petal modes.
3. With DM-space marginalization, 498 modes are controlled. The loop becomes unstable at g=0.28 on tip-tilt.
4. With WFS+DM space marginalization, 260 modes are controlled. The loop becomes unstable at g=0.33 on tip-tilt.
5. With DM+WFS space marginalization, 487 modes are controlled. The loop becomes unstable at g=0.60 on tip-tilt.


## 3. Conclusions

{% include important.html content="
Within a block of modes, the SVD limit should be chosen to ensure control stability (improved by higher `svdlim` value) and maximize the number of modes controlled (improved by lower `svdlim` value).
" %}

{% include important.html content="
WFS-space marginalization is critical to AO loop performance. DM-space marginalization is not helpful to loop stability, but can be used to force control modes, such as ensuring the first two control modes are tip and tilt.
" %}


{% include links.html %}
