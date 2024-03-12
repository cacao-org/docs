---
title: Predictive Control
keywords: optimization
last_updated: Mar 11, 2024
tags: [optimization]
summary: "Predictive Control"
sidebar: userguide_sidebar
permalink: cacao_predictive_control.html
folder: cacao
---


To enable predictive control, the following compute units must be enabled:
- `CACAO_FPSPROC_MKPF`: Build predictive control filter(s)
- `CACAO_FPSPROC_APPLYPF`: Apply predictive control filter(s)

Each instance of the MKPF and APPLYPF compute units handles a set number of control modes. For high-order AO systems, there may be multiple instances of MKPF/APPLYPF pairs, each handling a subset of the control modes. The number of such pairs is set by variable `CACAO_PF_NBBLOCK`.





{% include links.html %}
