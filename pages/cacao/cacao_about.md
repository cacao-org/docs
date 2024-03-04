---
title: cacao
keywords: documentation theme, jekyll, technical writers, help authoring tools, hat replacements
last_updated: Feb 20, 2024
tags: [getting_started]
summary: "cacao"
sidebar: home_sidebar
permalink: cacao_about.html
folder: cacao
---

cacao (Compute and Control for Adaptive Optics) deploys and manages processes for real-time control of adaptive optics systems, and provides user interfaces to interact with them.

cacao is a plugin of milk. Commands inherited from milk start with "milk-" while cacao-specific commands start with "cacao-".


cacao is built around 3 types of data structures, provided by milk, and hosted on the system's shared memory :
- Streams contain numerical data (images, matrices and vectors) for real-time use
- Function Parameter Structures (FPS) provide interface to variables and parameters
- Process Info (procinfo) provide control and status of real-time processes

To view and interact with stream, FPS and procinfo structures, run:

{% include links.html %}
