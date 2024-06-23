---
title: selfRM
keywords: monitoring
last_updated: Jun 22, 2024
tags: [optimization]
summary: "SelfRM is a modal response matrix acquisition routine, part of the mfilt compute unit. Each mode is excited and the corresponding modal-space WFS response recorded as a function of time."
sidebar: userguide_sidebar
permalink: cacao_selfRM.html
folder: cacao
---



## 1. Description


### 1.1. Overview

The `selfRM` refers to the measured modal response matrix. This is a 3D file, written as a FITS file. The axes are x: measured WFS mode, y: excited DM mode, and z: time step after excitation command is sent. The `selfRM` is acquired within the `mfilt` compute unit.

{% include tip.html content="
`selfRM` is very useful for diagnostics of an AO loop, both in open loop (gain=0) and close loop. It is recommended to run it before attempting to close the loop to check the DM and WFS modes are self-consistent. A clean diagonal signal in `selfRM` is almost certainly indicating that the loop will close with good performance.
" %}




{% include warning.html content="The modal control loop should be closed for `selfTM` acquisition (`mfilt` should be running with loop ON). To acquire the open loop `selfRM`, set loop gain to zero.
" %}


### 1.2. Expected `selfRM` content

In open loop (gain = 0), the `selfRM` should ideally be zeros for the first few time slices, and then should ideally be a diagonal of ones along the (x=y) line.

The diagonal of ones indicates that poking a single DM mode corresponds to the same mode being excited in the WFS measurement. Off-diagonal elements indicate cross-talk and should be much smaller than 1.

A time delay is expected between DM pokes and the corresponding response appearing in the WFS signal stream, so the first frame(s) of `selfRM` should be close to zero.

In close loop (gain>0), the diagonal should fade with slice (loop iteration after the poke), as the control loop should correct the added poke.



### 1.3. Poke sequence

The number of iterations is set by parameter `mfilt.selfRM.nbiter`. Within each iteration, the mode index is iterated from 0 to `mfilt.selfRM.NBmode`-1 (note: clipped at the total number of control modes, so user can enter very large value to ensure all modes a probed).

A poke sequence is defined as follows: from an intial state of zero (no selfRM poke), a single mode poke is added with amplitude `mfilt.selfRM.pokeampl` and sign `+` or `-`. Once applied, the poke is held for  `mfilt.selfRM.zsize` loop iterations, over which WFS mode values are recorded and added to the measurement. An additional  `mfilt.selfRM.nbsettle` loop iterations are added before the next poke to ensure the DM settles.

The poke sequence is repeated twice before the incrementing the mode index. The poke sign may change between the two consecutive sequences, as detailed below, with the poke signs labeled `+` and `-`, and the modes noted `m0`, `m1`, `m2`...:

```
seflRMiter  m0 m1 m2 m3 m4 ...
    0       +- +- +- +- +- ...
    1       -+ +- -+ +- -+ ...
    2       ++ ++ ++ ++ ++ ...
    3       -- ++ -- ++ -- ...
    4       -- -- -- -- -- ...
    5       ++ -- ++ -- ++ ...
    6       -+ -+ -+ -+ -+ ...
    7       +- -+ +- -+ +- ...
```

As detailed above, on the first iteration, mode 0 is poked with signs `+` (first sequence) and then `-` (second sequence). The next mode (mode 1) is then poked with signs `+` and then `-`, and so on.

The sequence is designed so that, over a set of 8 iterations:
- for each mode, there are four possible pairs of sequences: `++`, `--`, `+-`, and `-+`, with each of these pairs occurring exactly twice
- for each mode, for each pair of sequences (occurs twice in the 8 iterations), the preceeding mode sequences are opposite in the two occurences. For example, in the example above, for mode m2, the `+-` sequence occurs twice, at iterations 0 (for which the preceeding m1 sequence pair is `+-`) and 7 (for which the preceeding m1 sequence pair is `-+`)

These poke patterns are designed to balance temporal bleeding effects between modes, ensuring the measurement is as clean as possible and immune to poke-induced vibrations. Assuming linearity, the temporal signature of pairs mode m1 `+-` and `-+` will be opposite, so that when added they will not contribute to the measurement of mode m2 `+-`.

{% include tip.html content="
To take full advantage of the sign-balancing propeeties of the poke sequence, the number of iteration `mfilt.selfRM.nbiter` should be set to a multiple of 8.
" %}



## 2. Acquiring `selfRM`

As `selfRM` is part of the `mfilt` compute unit, no additional compute unit is required.

To run `selfRM`, run :
```bash
cacao-fpsctrl setval mfilt selfRM.enable ON
```

If `selfRM.zsize` must be changed, the `mfilt` process will first need to be stopped, as `mfilt` allocates `selfRM` buffer when starting. For example, to set `selfRM.zsize` to 40 :


```bash
# stop mfil process
cacao-fpsctrl runstop mfilt 0 0
# set zsize to 40
cacao-fpsctrl setval mfilt selfRM.zsize 40
# poke 2000 modes (may be clipped to lower value to match modal basis size)
cacao-fpsctrl setval mfilt selfRM.NBmode 2000
# restart mfilt process
cacao-fpsctrl runstart mfilt 0 0
# start selfRM acquisition
cacao-fpsctrl setval mfilt selfRM.enable ON
```

When completed, the output file will be in the rundir directory, with file name `selfRM.fits`.





{% include links.html %}
