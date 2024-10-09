---
title: cacao - Directories and Files
last_updated: Aug 16, 2024
tags: [getting_started, setup, framework]
summary: "Conventions for directories and files"
sidebar: userguide_sidebar
permalink: cacao_directories_files.html
folder: cacao
---


# 1. Operation


All cacao commands and scrpts are run from the loop root directory, and an error message will be returned if they are not.


# 2. Calibrations

## 2.1. Calibration Files

Calibration files are stored in `conf/` subdirectory of the loop root directory.

- The curent working files (input and output of scripts) are stored as FITS files, such as `dmmask.fits`.
- These are the key input and output of most cacao scripts.
- Configuration files, when uploaded to shared memory, are also copied to `shmim.sname.fits` file, which `sname` the name of the shared memory (without the `aolX_` prefix).

{% include note.html content="
Uploading a calibration file to shared memory should be done with the command `cacao-calib-streamload`, whith will perform the copy to `conf/shmim.sname.fits` and log the operation.
" %}





## 2.2. Calibration scripts

By default cacao scripts will upload results in shared memory and archieved to the `logdir` directory. This allows for quick operation, not having to run separate commands for compute and upload, and performs automatic archival.

{% include warning.html content="
the uploading to shared memory can disrupt a running AO loop. Use the `-nl` (no loading to memory) to disable it.
" %}

## 2.3. Managing multiple calibrations

With the environment variable `CACAO_CONFWDIR` set, the configuration working directory (default `conf`) will be changed to `CACAO_CONFWDIR`.




In this example, a custom set of control modes is computed and stored in directory `conftest1`. At the end of this example, the resulting control modes are loaded to shared memory:
```bash
# All commands executed from LOOPROOTDIR
# Create new configuration directory
mkdir -p conftest1

# Copy zonal RM from main configuration
cp ./conf/zrespM.fits ./conftest1/


# Have all cacao scripts below work in conftest1
export CACAO_CONFWDIR="conftest1"

# Create Zernike-Fourier modes
cacao-aorun-028-mkZFmodes -c0 0 -c1 32 -c 50 -ea 2.0 -t 1.0 -a 0.3
# output:
# conftest1/RMmodesDM/modesZF0.fits (not apodized)
# conftest1/RMmodesDM/modesZF.fits (apodized)

# Create the corresponding response
cacao-aorun-034-RMzonal2modal modesZF
# output:
# conftest1/RMmodesDM.fits (sym link to input modesZF.fits)
# conftest1/RMmodesWFS.fits

# Compute the corresponding control matrix using the by-block algorithm
cacao-aorun-045-compCM-byblocks 0.1 1
# output:
# conftest1/CMmodesDM.fits
# conftest1/CMmodesDM.fits

# Load the CM to shared memory, copy to main conf and log
cacao-aorun-042-loadCM

```



## 2.4. User scripts

User calibration scripts should be stored in `conf/scripts/`


## 2.2. Archived Files

Most cacao scripts save their output in archive in directory `logdir` which may be a symlink to outside the LOOPROOTDIR.

Files are archieved with the `cacao-calib-logFITSfile` function (part of `cacaofuncs-log`), which will take as argument the `NAME`, and copy file `conf/NAME.fits` to `logdir/aol_LOOPNAME/NAME/NAME.DATESTRING.fits`.




