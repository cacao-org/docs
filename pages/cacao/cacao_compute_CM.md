---
title: Compute Control Matrix
keywords:
last_updated: Feb 21, 2024
tags: [control_matrix]
summary: "Control matrix"
sidebar: userguide_sidebar
permalink: cacao_compute_CM.html
complex_map: true
map_name: runcacaoloopmap
box_number: 2
folder: cacao
---




## Computing Control Modes

Before proceeding to this step, make sure the required input files are ready:

<pre>
conf/RMmodesDM/RMmodesDM.fits (response matrix, DM space)
conf/RMmodesWFS/RMmodesWFS.fits (response matrix, WFS space)
conf/dmmask.fits (DM pixel mask, indicating which actuators are linearly coupled to WFS)
conf/wfsmask.fits (WFS pixel mask, indicating which WFS pixels are linearly coupled to DM)
</pre>


## Computing CMs by SVD
Control modes are computed and their representation in both WFS and DM spaces. First, set the SVD limit:

```bash
cacao-fpsctrl setval compstrCM svdlim 0.001
```

Then run the `cacao-aorun-039-compstrCM` script to compute CM and load it to shared memory:

```bash
cacao-aorun-039-compstrCM
```

Inspect result:

<pre>
conf/CMmodesDM/CMmodesDM.fits
conf/CMmodesWFS/CMmodesWFS.fits
Check especially the number of modes controlled.
</pre>





{% include callout.html content="**Conventions**:
<br/><br/>

Control modes are orthogonal in WFS space (dot product between two modes = 0) over the WFS pixel mask (wfsmask)
<br/>
Control modes are of unity norm in DM space over the DM pixel mask (dmmask)
<br/><br/>

WFS-space orthogonality ensures that the modal coefficients can be obtained by matrix-vector multiplication (MVM) between CMmodesWFS and the input WFS signal.
DM-space normalization ensures that the modal coefficients obtained by the above MVM operation have a physical meaning (unit = micron RMS over dmmask).
"
type="primary" %}





### Ordering CMs by Spatial Frequency
The SVD-based process orders modes by singular value, such that DM patterns creating the strongest linear WFS response are first.
Ordering CMs by spatial frequency (low-order modes first) is often preferable when implementing and tuning modal control.

```bash
# Input:
# ./conf/RMmodesWFS/zrespM-H.fits
# - conf/dmmask.fits
# - conf/wfsmask.fits
#
# Output:
# - conf/CMmodesDM/CMmodesDM_sf.fits
# - conf/CMmodesWFS/CMmodesWFS_sf.fits
#
cacao-aorun-040-compfCM -g 0 -c 25
```

The `cacao-aorun-040-compfCM` script allows for much customization. Check options with -h. For a more detailed discussion about how to optimize control modes, check the ​[link]().




{% include links.html %}
