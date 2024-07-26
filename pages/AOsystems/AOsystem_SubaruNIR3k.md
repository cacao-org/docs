---
title: Subaru NIR 3k system (AO3kNIR)
keywords: AOsystem
last_updated: Jun 24, 2024
tags: [AOsystem]
summary: "Subaru Telescope AO3k system, near-IR pyramid WFS"
sidebar: examples_sidebar
permalink: AOsystem_SubaruNIR3k.html
folder: 
---

## 1. System Overview

AO3kNIR uses a near-IR pyramid WFS to drive a 64x64 magnetic DM (ALPAO DM3228).



## 2. System Calibration


System calibration is done in three main steps:
- Acquire respone matrix using Hadamard pokes, decode to zonal space
- Construct modal response matrix for tip, tilt, focus, astigmatism and Fourier modes
- Construct the control matrix by blocks of spatial frequency

This process may need to be repeated to iterately flatten the wavefront and acquire a high quality calibration around the flat wavefront point.


{% include warning.html content="
Take a dark before starting calibration, as the detector dark changes
" %}


### 2.1. Setup

Set modulation to 500 Hz to improve system linearity.

The WFS acquisition process should be set to subtract dark and normalize signal. Initially, turn off reference subtraction:

```bash
cacao-fpsctrl setval acquWFS comp.darksub ON
cacao-fpsctrl setval acquWFS comp.WFSnormalize ON
cacao-fpsctrl setval acquWFS comp.compWFSmask OFF
cacao-fpsctrl setval acquWFS comp.WFSrefsub OFF
```

Align the WFS so that the flux is equally distributed between the 4 pupil images. Example commands:

```bash
# phi is +X direction
irwfs_steering phi push -0.01
# theta is +Y direction
irwfs_steering theta push 0.005
```



### 2.2. Acquiring response matrix



Use amplitude 0.005 to ensure WFS linearity, and set NBexl to 5 to give sufficent time for DM to settle after pokes. Response matrix acquisition takes about 15mn.

```bash
cacao-fpsctrl setval measlinresp ampl 0.005
cacao-fpsctrl setval measlinresp timing.NBave 5
cacao-fpsctrl setval measlinresp timing.NBexcl 5
cacao-aorun-030-acqlinResp -n 8 HpokeC
```

The Hadamard response matrix is then decoded to zonal space:

```bash
cacao-aorun-031-RMHdecode
```






```bash
cacao-aorun-028-mkZFmodes -c0 0 -c1 32 -c 50 -ea 2.0 -t 1.0 -a 0.3
```



```bash
#!/usr/bin/env bash

SVDl100=${1:-"06"}
MARGMODE=${2:-1}

# 1: always marginalize against TT in DM space



SVDlim="0.${SVDl100}"

echo "SVDlim = ${SVDlim}"

# 0.08  1745
# 0.10	1535
# 0.12  1390


WHICH_MARG="-marg"
MARGTT="-margDM 0:1"
margstring="margDM01-margWFS"




if [[ ${MARGMODE} -eq 1 ]]; then
echo "MARGMODE = 1"
WHICH_MARG="-marg"
MARGTT="-margDM 0:1"
margstring="margDM01-margWFS"
fi


if [[ ${MARGMODE} -eq 2 ]]; then
echo "MARGMODE = 2"
WHICH_MARG="-marg"
margstring="margWFS"
fi

if [[ ${MARGMODE} -eq 3 ]]; then
echo "MARGMODE = 2"
WHICH_MARG="-margDM"
margstring="margDM"
fi



SAVEDIR="calib/svd${SVDl100}-${margstring}/"
echo "SAVEDIR : ${SAVEDIR}"



if [ ! -d ${SAVEDIR} ]; then

cacao-fpsctrl setval compstrCM svdlim ${SVDlim}

# Make T CM
cacao-aorun-039-compstrCM -mb 0 -mr 1:1

# Make T CM
cacao-fpsctrl setval compstrCM svdlim ${SVDlim}
cacao-aorun-039-compstrCM -mb 1 -mr 2:2

# make LO CM
cacao-fpsctrl setval compstrCM svdlim ${SVDlim}

if [[ ${MARGMODE} -eq 1 ]]; then
cacao-aorun-039-compstrCM -mb 2 -mr 3:12 ${MARGTT}
else
cacao-aorun-039-compstrCM -mb 2 -mr 3:12 ${WHICH_MARG} 0:1
fi



# make LO CM
cacao-fpsctrl setval compstrCM svdlim ${SVDlim}

if [[ ${MARGMODE} -eq 1 ]]; then
cacao-aorun-039-compstrCM -mb 3 -mr 13:50 ${WHICH_MARG} 2 ${MARGTT}
else
cacao-aorun-039-compstrCM -mb 3 -mr 13:50 ${WHICH_MARG} 0:1:2
fi



# make MO CM
cacao-fpsctrl setval compstrCM svdlim ${SVDlim}

if [[ ${MARGMODE} -eq 1 ]]; then
cacao-aorun-039-compstrCM -mb 4 -mr 51:200 ${WHICH_MARG} 2:3 ${MARGTT}
else
cacao-aorun-039-compstrCM -mb 4 -mr 51:200 ${WHICH_MARG} 0:1:2:3
fi



# make HO CM
cacao-fpsctrl setval compstrCM svdlim ${SVDlim}

if [[ ${MARGMODE} -eq 1 ]]; then
cacao-aorun-039-compstrCM -mb 5 -mr 201:1000 ${WHICH_MARG} 2:3:4 ${MARGTT}
else
cacao-aorun-039-compstrCM -mb 5 -mr 201:1000 ${WHICH_MARG} 0:1:2:3:4
fi



# make HHO CM
cacao-fpsctrl setval compstrCM svdlim ${SVDlim}

if [[ ${MARGMODE} -eq 1 ]]; then
cacao-aorun-039-compstrCM -mb 6 -mr 1001:5029 ${WHICH_MARG} 2:3:4:5 ${MARGTT}
else
cacao-aorun-039-compstrCM -mb 6 -mr 1001:5029 ${WHICH_MARG} 0:1:2:3:4:5
fi


# merge CMs
cacao-aorun-039-compstrCM -mbm 0:1:2:3:4:5:6





mkdir -p ${SAVEDIR}
cp conf/CMmodesDM.fits ${SAVEDIR}
cp conf/CMmodesWFS.fits ${SAVEDIR}
cp conf/CMmodesDM.xp.fits ${SAVEDIR}
cp conf/CMmodesWFS.xp.fits ${SAVEDIR}

else

echo "DIRECTORY ${SAVEDIR} exists"

cp ${SAVEDIR}/CMmodesDM.fits ./conf/
cp ${SAVEDIR}/CMmodesWFS.fits ./conf/
cp ${SAVEDIR}/CMmodesDM.xp.fits ./conf/
cp ${SAVEDIR}/CMmodesWFS.xp.fits ./conf/

fi


milk-FITS2shm ./conf/CMmodesDM.fits aol7_DMmodes
milk-FITS2shm ./conf/CMmodesWFS.fits aol7_modesWFS

cacao-fpsctrl runstop wfs2cmodeval 0 0
cacao-fpsctrl runstop mfilt 0 0
cacao-fpsctrl runstop mvalC2dm 0 0

cacao-fpsctrl confstop wfs2cmodeval 0 0
cacao-fpsctrl confstop mfilt 0 0
cacao-fpsctrl confstop mvalC2dm 0 0

sleep 2

# START

cacao-fpsctrl confstart wfs2cmodeval 0 0
sleep 1
cacao-fpsctrl runstart wfs2cmodeval 0 0

sleep 2

cacao-fpsctrl confstart mfilt 0 0
sleep 1
cacao-fpsctrl runstart mfilt 0 0
#cacao-fpsctrl setval mfilt loopON ON
#cacao-fpsctrl setval mfilt loopgain 0.0


sleep 2

cacao-fpsctrl confstart mvalC2dm 0 0
sleep 1
cacao-fpsctrl runstart mvalC2dm 0 0

echo "Done"


```




{% include links.html %}
