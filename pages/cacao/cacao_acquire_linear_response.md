---
title: Acquiring the system's linear response
keywords:
last_updated: Feb 21, 2024
tags: [getting_started]
summary: "The WFS linear response to DM perturbation is acquired by actuation of the DM while recording WFS telemetry."
sidebar: userguide_sidebar
permalink: cacao_acquire_linear_response.html
complex_map: true
map_name: runcacaoloopmap
box_number: 2
folder: cacao
---


## Acquiring Linear Response Calibration


The system linear calibration consists of the following files:

File
Description
./conf/RMmodesDM/RMmodesDM.fits
Calibration DM modes
./conf/RMmodesWFS/RMmodesWFS.fits
WFS linear response to DM modes
./conf/dmmask.fits
DM mask (active actuators = 1)
./conf/wfsmask.fits
WFS mask (active pixels = 1)

These are the input to the ​computation of control modes


### Preparing DM Poke Modes


The cacao-mkDMpokemodes command computes a few different sets of poke modes, from which we can choose the DM poke modes used for acquiring the WFS linear response.

```bash
# -z is for number of Zernike modes
# -c is for max spatial frequency [CPA]
cacao-mkDMpokemodes -z 5 -c 25
```

The following files are written to ./conf/RMmodesDM/:

File
Description
FpokesC.<CPA>.fits
Fourier modes up to spatial frequency CPA (integer)
ZpokesC.<NUM>.fits
First NUM Zernike modes.
SmodesC.fits
Simple (single actuator) pokes
HpokeC.fits
Hadamard modes
Hmat.fits
Hadamard matrix
Hpixindex.fits
Hadamard pixel index

DM poke modes can also be prepared independently of the `cacao-mkDMpokemodes` command, following the file name convention ./conf/RMmodesDM/<name>.fits.

### Acquiring WFS Linear Response to DM poke modes

The cacao-aorun-030-acqlinResp command acquires the WFS response corresponding to a set of . For example, to acquire the calibration with Hadamard poke modes: 

```bash
# -n : number of cycles (increase for higher SNR)
# HpokeC is the DM poke modes
cacao-aorun-030-acqlinResp -n 4 HpokeC

```

The output is a FITS cube, written to ./conf/RMmodesWFS/<name>.WFSresp.fits. For example, the response to Hadamard pokes will be ./conf/RMmodesWFS/HpokeC.WFSresp.fits.


{{site.data.alerts.note}}
<p>The WFS linear response to multiple sets of DM poke modes can be acquired. For example with.</p>
<pre>
# Creates files:
# ./conf/HpokeC.WFSresp.fits
# ./conf/ZpokeC.5.WFSresp.fits
# ./conf/TipTiltFoc.WFSresp.fits
cacao-aorun-030-acqlinResp -n 4 HpokeC
cacao-aorun-030-acqlinResp -n 10 ZpokesC.5
cacao-aorun-030-acqlinResp -n 30 TipTiltFocCACAO_FPSPROC_ACQUWFS
</pre>
{{site.data.alerts.end}}




The `cacao-aorun-030-acqlinResp` command also creates/updates the sym link ./conf/RMmodesWFS/RMmodesWFS.fits to point to the new <name>.WFSresp.fits file, so that the  could be run.

{{site.data.alerts.note}}
Use the -s option to save all intermediate files for custom assembly/averaging of the response matrix. Files will appear in directory ./LOOPNAME_rundir/measlinrespm/DATE. By default (no -s option), only timing info and poke sequence info will be written. With the -s option, FITS files corresponding to each RM (time step and iteration) will be written.
{{site.data.alerts.end}}




Example measlinrespmdirectory content (with -s option)



{{site.data.alerts.warning}}
Watch out disk usage, especially when saving all intermediate files and taking many calibrations.
{{site.data.alerts.end}}



{{site.data.alerts.note}}
Stopping the RM acquisition.  The RM acquisition process can be stopped at any time, before the total number of iterations is reached. The process saves results at the end of each iteration, writing both intermediate files and the overall (averaged) response matrix. There are 2 ways to stop the RM acquisition process:
INT or TERM signals: Type CTRL+SHIFT+r in milk-fpsCTRL, or send INT signal to run process (this can be done by typing SHIFT+i in milk-procCTRL). This will complete the current and next iteration and then gently stop the process.
KILL signal: Send KILL signal to run process (this can be done by typing SHIFT+k in milk-procCTRL). This will stop the process immediately.
Excluding most recent RM iteration(s). In the measlinrespm directory, the RM (averaged) is written at the end of each iteration, as file mode_linresp.ave.iterXXXX.fits. If some disruptive event occurred where the last iterations are known to be corrupted, use the appropriate such file, and copy it to the desired output destination.
{{site.data.alerts.end}}



### Representing WFS response in zonal space (optional)

{{site.data.alerts.note}}
Zonal representation is useful for visualization, showing the WFS response to individual DM actuators. Zonal representation also makes DM mask computation relatively straightforward.
{{site.data.alerts.end}}


If a Hadamard response matrix was acquired, the zonal space WFS response is decoded with:

```bash
cacao-aorun-031-RMHdecode
```

Any WFS response can also be projected to the zonal basis with the following script:

```bash
#!/usr/bin/env bash
​
cacao
# load custom RM files
loadfits "customRMmodesDM.fits" RMmodesDM
loadfits "customRMmodesWFS.fits" RMmodesWFS
# convert to zonal representation
# last argument is SVD limit - may require tuning
cacaocc.RM2zonal RMmodesDM RMmodesWFS RMmodesDMz RMmodesWFSz 0.01
# save results
saveFITS RMmodesDMz "conf/RMmodesDM/RMmodesDMz.fits"
saveFITS RMmodesDMz "conf/RMmodesWFS/RMmodesWFSz.fits"
exitCLI
```


### DM and WFS masks (optional)

If a zonal response matrix exists, then DM and WFS maps and masks can be computed:

```bash
cacao-aorun-032-RMmkmask
```

Check results:

<pre>
conf/dmmask.fits
conf/wfsmask.fits
</pre>

If needed, rerun command with non-default parameters (see -h for options). Note: we are not going to apply the masks in this example, so OK if not net properly. The masks are informative here, allowing us to view which DM actuators and WFS pixels have the best response.


{% include links.html %}
