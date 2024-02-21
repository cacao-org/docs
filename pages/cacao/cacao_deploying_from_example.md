---
title: Deploying from example
keywords: 
last_updated: Feb 21, 2024
tags: [getting_started]
summary: "Deploying from example"
sidebar: home_sidebar
permalink: cacao_deploying_from_example.html
folder: cacao
---


## Download & modify existing example

Download from source to current directory one of the cacao example loops. Run command cacao-loop-deploy with -h option to see list of examples.

```bash
cacao-loop-deploy -c <examplename>
```

The loop number. loop name, DM index and DM simulation index can be changed from their default values by setting the corresponding environment variables. For example:

```bash
CACAO_LOOPNUMBER=7 cacao-loop-deploy -c <examplename>
CACAO_LOOPNUMBER=7 CACAO_DMINDEX="03" cacao-loop-deploy -c <examplename>
```

OPTIONAL: Edit file <examplename>-conf/cacaovars.bash as needed


## Deploy processes and tmux sessions

Run deployment (starts the conf processes):

```bash
cacao-loop-deploy -r <examplename>
```

The copy and run steps can be done at once with:

```bash
cacao-loop-deploy <examplename>
```

Go to rootdir, from which user controls the loop:

```bash
cd <loopname>-rootdir
```

From this point on, all commands should be run from the root directory. To check we are in the correct directory, run command cacao-check-cacaovars


## Start logging (optional)
Message logging is optional.

## Select simulation or hardware mode

```bash
# Simulation mode
./scripts/aorun-setmode-sim
# Hardare mode, will connect to hardware
./scripts/aorun-setmode-hardw
```


{% include links.html %}
