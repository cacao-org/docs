---
title: Logging data streams
keywords: streams
last_updated: May 20, 2024
tags: [streams]
summary: "Logging data streams to disk"
sidebar: userguide_sidebar
permalink: milk_streamlogging.html
folder: milk
---


Use command `milk-streamFITSlog` to log streams to disk (run with `-h` option for instructions).

`milk-stremFITSlog` is a wrapper function for controlling logging processes through a `milk-fpsCTRL` instance containing only the FITS logging entries is running in the `milkFITSlogger` tmux session.

To access the TUI:

```bash
tmux a -t milkFITSlogger
# make sure to disconnect from the tmux session with CTRL-B + D, do not exit the milk-fpsCTRL process runnning in the session
```

Users can perform logging operations through the `milk-streamFITSlog`  wrapper script, or directly in the `milk-fpsCTRL` instance.





{% include links.html %}
