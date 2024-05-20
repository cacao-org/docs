---
title: Hardware Latency
last_updated: Feb 21, 2024
tags: [performance]
summary: "Measuring hardware latency, the time delay between DM actuation commands and WFS response measured"
sidebar: userguide_sidebar
permalink: cacao_hardware_latency.html
complex_map: true
map_name: runcacaoloopmap
box_number: 2
folder: cacao
---



The time delay between issuing a command to the DM and having the corresponding signal in the WFS stream is the hardware latency. It must be known to measure the system response, as well as for some advanced control features such as pseudo-open loop reconstruction and predictive control.

```bash
# -w option is for "wait until done"
cacao-aorun-020-mlat -w
```

Check the result in fpsCTRL TUI, fields `.out.framerateHz` and `.out.latencyfr`.

The latency value is estimated by locating the maximum value of the latency curve, where the y-coordinate is the sum-squared difference between two consecutive WFS frames and the x-axis coordinate is the time difference between the WFS difference measurement and the time at which the DM command was issued.

The file is written on `fps.mlat-0.datadir/hardwlatency.dat` (for LOOPNUMBER 0), with columns 2 and 3 the x and y coordinate of the curve.

To view the curve:
```bash
# Still from the ROOTDIR
cacao-aorun-021-mlatshow
```


{% include links.html %}
