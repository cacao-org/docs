---
title: WFS acquisition
keywords: 
last_updated: Feb 21, 2024
tags: [getting_started]
summary: "WFS acquisition"
sidebar: userguide_sidebar
permalink: cacao_WFSacquisition.html
complex_map: true
map_name: runcacaoloopmap
box_number: 1
folder: cacao
---


## Taking the WFS dark

If the WFS input has a bias and/or dark, acquire the "dark", which is the WFS input when no signal is present:

```bash
# -n option: number of frames averaged
cacao-aorun-005-takedark -n 2000
```

Make sure to turn off or block light source before running the `cacao-aorun-005-takedark` command, and remember to turn on / unblock after done.


## Starting WFS acquisition

```bash
cacao-aorun-025-acqWFS -w start
```

## Acquiring the WFS reference

This is the WFS response to a flat (unaberrated) input wavefront, and defines the convergence point for the AO loop. This step can be skipped is the WFS input is already referenced to zero for a flat wavefront.

```bash
cacao-aorun-026-takeref -n 2000
```


{% include links.html %}
