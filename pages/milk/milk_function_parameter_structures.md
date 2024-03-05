---
title: Function Parameter Structures (FPS)
keywords: 
last_updated: Mar 4, 2024
tags: [getting_started]
summary: "Function Parameter Structures (FPS) hold variables for processes"
sidebar: home_sidebar
permalink: milk_function_parameter_structures.html
folder: milk
datatable: true
---


# Rationale

Each real-time process has its parameters (input and output) stored in a shared memory structure, the FPS, allowing low-latency communication with the process without impacting its performance. The FPS is shared with the realtime process, allowing for a non-realtime process to change function parameters (such as the AO loop gain) with no additional load on the realtime process.

# FPS control

Users can interact with FPSs with the fpsCTRL tool:

```bash
milk-fpsCTRL
```

{% include note.html content="
run with -h option for more information
" %}


Users can interract with FPSs using the milk-fpsCTRL tool in two ways:
- TUI control navigation (type h within GUI for instructions)
- fpsCTRL's input fifo


# Using the fpsCTRL input fifo

By default, each instance of milk-fpsCTRL sets up an input fifo to which commands can be sent. The fifo name appears at the top of the terminal in the verbose mode (typing "V" in the GUI to toggle verbose mode). 


{% include image.html file="fpsCTRL-maincontrol.png" 
caption="milk-fpsCTRL main control screen, in verbose mode. The input fifo name is on the 5th line." %}


As shown in the screenshot above, the default input fifo name follows the terminal name within which it is running, in order to avoid name collision. In this example, it is `/milk/shm/milkCLIfifo.fpsCTRL.devpts83`, where `devpts83` is the terminal name.

To set the input fifo name to a specfic name, use the -f option:

~~~bash
milk-fpsCTRL -f /tmp/mycustomfifo
~~~

Then, to issue commands to the fpsCTRL instance:

~~~bash
echo "my command" >> /tmp/mycustomfifo
~~~

The fpsCTRL tool will process commands one line at a time. The history of commands received and their processing status can be seen by typing F3 to enter the sequencer screen:



{% include image.html file="fpsCTRL_sequencer.png" 
caption="milk-fpsCTRL sequencer screen, accessible by typing F3" %}



# cacao's fpsCTRL process


Each cacao loop has its own fpsCTRL process. It is hosted within a tmux session, with the name loopname_fpsCTRL. See example output of tmux ls command below for a loop named maps:

~~~bash
tmux ls
~~~

<pre>
DMch2disp-00: 3 windows (created Thu Sep 21 15:52:34 2023)
DMch2disp-10: 3 windows (created Thu Sep 21 15:52:35 2023)
DMstreamDelay-10: 3 windows (created Thu Sep 21 15:52:35 2023)
acquWFS-2: 3 windows (created Thu Sep 21 15:52:33 2023)
compstrCM-2: 3 windows (created Thu Sep 21 15:52:34 2023)
maps_fpsCTRL: 1 windows (created Thu Sep 21 15:52:36 2023)
measlinresp-2: 3 windows (created Thu Sep 21 15:52:35 2023)
mfilt-2: 3 windows (created Thu Sep 21 15:52:36 2023)
mlat-2: 3 windows (created Thu Sep 21 15:52:33 2023)
mvalC2dm-2: 3 windows (created Thu Sep 21 15:52:36 2023)
simmvmgpu-2: 3 windows (created Thu Sep 21 15:52:33 2023)
wfs2cmodeval-2: 3 windows (created Thu Sep 21 15:52:34 2023)
​</pre>


The input fifo name for cacao's fpsCTRL process is $MILK_SHM/loopname_fpsCTRL.fifo. For the example above, it is /milk/shm/maps_fpsCTRL.fifo.
To send a command to the loop, scripts and users would issue commands such as:

~~~bash
echo "runstart mfilt-2.loopON ON" >> /milk/shm/maps_fpsCTRL.fifo
~~~


From a loop's root directory, the cacao-fpsctrl can send commands to the loop without specifying the loop number or input fifo. The above command can then be issued with:

~~~bash
cacao-fpsctrl setval mfilt loopON ON
~~~

This is quite handy to write generic scripts that are not tied to a specific loop index, and is used by most cacao high level scripts. The command reads the LOOPNUMBER and LOOPNAME local files to build the full command string and fifo name.



# Input fifo commands


## Rationale
The preferred way for high-level interaction with processes is to use milk-fpsCTRL's input fifo. Commands are sent to the fifo for execution.


## Sequencer & Execution Queues

{% include image.html file="FPSfifocommands.svg" 
caption="milk-fpsCTRL sequencer screen, accessible by typing F3" %}


Input fifo commands are processed by the milk-fpsCTRL sequencer. The sequencer status is accessible by typing F3 in the milk-fpsCTRL GUI.


## Controlling fps TUI


<pre>

Command      Description                   Argument(s)


</pre>

Command      Description                   Argument(s)

rescan       Rescan fps tree
​exit         Exit fpsCTRL TUI
​
</pre>



## Controlling processes

<div class="datatable-begin"></div>

Command      | Description                  | Argument(s)
------------ | ---------------------------- | -------- 
confstart    | Start conf process           | keyword
confstop     | Stop conf process            | keyword
confupdate   | Update conf process          | keyword
confwupdate  | Update conf process, wait for completion before processing next command | keyword
runstart     | Start run process            |   keyword
runstop      | Stop run process             |   keyword
setval       | Set value                    |   keyword, value
tmuxstart    | Start tmux session           |   keyword
tmuxstop     | Stop tmux session            |   keyword
fpsrm        | Remove fps                   |   keyword

<div class="datatable-end"></div>


Only the setval command requires an exact full keyword (for example mfilt-2.loopON). Other commands will only use the first part of the keyword (mfilt-2) and disregard the rest of the keyword, which is then optional.




## Getting status

<div class="datatable-begin"></div>

Command      | Description                  | Argument(s)
------------ | ---------------------------- | -------- 
fpswfile     | Write fps content to txt file in datadir | keyword
getval       | Get value, write to output log | keyword
fwrval       | Get value, write to file or fifo | keyword, fname
cntinc       | Counter test to check input fifo connection |

<div class="datatable-end"></div>



## Execution Queues and Sequencing



<div class="datatable-begin"></div>

Command      | Description                  | Argument(s)
------------ | ---------------------------- | -------- 
setqindex    | Set queue index              | queue index
setqprio     | Set current queue priority   | priority
queueprio    | Set any queue priority       | queue, priority
waitonrunON  | Toggle wait-on-run ON        | 
​waitonrunOFF | Toggle wait-on-run OFF       | 
​waitonconfON | Toggle wait-on-conf ON       | 
waitonconfOFF| Toggle wait-on-conf OFF      | 

<div class="datatable-end"></div>






# milk-fpsCTRL's execution queues

Using execution queues for advanced sequencing


{% include note.html content="
Using execution queues is optional (but very powerful). You can skip this section if you don't need to sequence operations, or if you have another way to do it. Unless you change queues and priorities, all tasks will be in the queue #0, with priority=1, and will simply be executed in the order they are received (FIFO).
" %}



Tasks processed by milk-fpsCTRL's sequencer are arranged in execution queues. Each task belongs to a single queue, defined by an index (integer between 0 and 99). Each queue also has a priority index (index from 0 to 99).


## Rules

The sequencer decides which task to run according the following rules:
- Tasks with the higher priority value are executed first
- Priorities are associated to queues, not individual tasks. Changing a queue priority affects all tasks in the queue
- If queue priority = 0, no task is executed in the queue: it is paused
- Task order within a queue must be respected. Execution order is submission order (FIFO)
- Tasks can overlap if they belong to separate queues and have the same priority
- A running task waiting to be completed cannot block tasks in other queues
- If two tasks are ready with the same priority, the one in the lower queue index will be launched

## Conventions
- queue #0 is the main queue
- Keep queue 0 priority at 10
- Return to queue 0 when done working in other queues


{% include links.html %}
