---
title: Deploying from example
keywords: 
last_updated: Mar 6, 2024
tags: [getting_started]
summary: "Deploying from example"
sidebar: userguide_sidebar
permalink: cacao_deploying_from_example.html
complex_map: true
map_name: runcacaoloopmap
box_number: 1
folder: cacao
---


## 1. Download & modify existing example

Download from source to current directory one of the cacao example loops:
```bash
cacao-loop-deploy -c <examplename>
```
<span class="label label-info">Tip</span> Run command cacao-loop-deploy with -h option to see list of examples.


The loop number. loop name, DM index and DM simulation index can be changed from their default values by setting the corresponding environment variables. For example:

```bash
CACAO_LOOPNUMBER=7 cacao-loop-deploy -c <examplename>
CACAO_LOOPNUMBER=7 CACAO_DMINDEX="03" cacao-loop-deploy -c <examplename>
```

OPTIONAL: Edit file <examplename>-conf/cacaovars.bash as needed


## 2. Deploy processes and tmux sessions

### 2.1. Run deployment (starts the conf processes):

```bash
cacao-loop-deploy -r <examplename>
```

<span class="label label-warning">Warning</span> Running this commands successive times won't re-run the deployment steps. To force re-running the steps, remove the taskmanager log files prior to the `cacao-loop-deploy` command: `rm .LOOPNAME.cacaotaskmanager-log/*`

<span class="label label-info">Info</span> The copy and run steps can be done at once with: `cacao-loop-deploy <examplename>`

{% include tip.html content="
To cleanup the deployment (undo all previous steps), use the cacao-task-manager command:<br>
<code>
cacao-task-manager -C 0 LOOPNAME
</code>
<br>
Check options with -h. The cacao-task-manager provides fine grained control over each step of the deployment.
" %}


### 2.2. Getting ready ...

{% include note.html content="
From this step onward, almost all commands need to be run from the LOOPNAME-rootdir directory
" %}

Go to rootdir, from which user controls the loop:
```bash
cd LOOPNAME-rootdir
```

From this point on, all commands should be run from the root directory. To check we are in the correct directory, run `cacao-check-cacaovars`.


## 3. Start logging (optional)

[Message logging](cacao_message_logging.html) is optional. 


## 4. Select simulation or hardware mode

```bash
# Simulation mode
./scripts/aorun-setmode-sim
# Hardare mode, will connect to hardware
./scripts/aorun-setmode-hardw
```


{% include links.html %}
