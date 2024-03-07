---
title: Hardware Latency
last_updated: Feb 21, 2024
tags: [performance]
summary: "Measuring hardware latency, the time delay between DM actuation commands and WFS response measured"
sidebar: home_sidebar
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

Check the result in fpsCTRL TUI, and by plotting the latency measurement curve. Inside the "loopname-rootdir", find a directory named "loopname-rundir", and execute the commands below:

```bash
cd loopname-rundir/fps.mlat-LOOPNUMBER.datadir
gnuplot
plot "hardwlatency.dat" u 2:3
exit
```


{% include links.html %}
