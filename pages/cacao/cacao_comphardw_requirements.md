---
title: Compute Hardware Requirements
keywords:
last_updated: May 17, 2024
tags: [CPU, GPU]
summary: "Hardware Requirements: compute bandwidth needed to close the AO loop."
sidebar: technotes_sidebar
permalink: cacao_comphardw_requirements.html
folder: cacao
---


## 1. Matrix-Vector Multiply (MVM) and GPU/CPU Specs

The most compute-heavy operation in closing the AO loop is often the matrix-vector-multiply (MVM) converting the input WFS pixel values to output wavefront modes. This MVM must be completed in a fraction of the AO loop period, typically well under 1 ms.


The MVM is most often memory-bandwidth limited, so when choosing the compute hardware (for example GPU), the device's memory bandwidth is the most relevant parameter. This is described in [this MVM technical note](https://docs.nvidia.com/deeplearning/performance/dl-performance-matrix-multiplication/index.html), where N=1 (Matrix-vector multiply, special case of matrix-matrix multiply), K is the number of WFS elements, and M is the number of modes reconstructed, or in zonal control, the number of DM actuators.

One must look at the arithmetic intensity, which is a crucial concept used to measure the efficiency of computational algorithms and applications. It is defined as the ratio of the number of floating-point operations (FLOPs) to the amount of data movement (usually measured in bytes).

Taking, for example, a large system with 87k input pixels, 33k output modes :

```
M=33k
K=87k
N=1

Assuming FP16 input, FP32 accumulation

For each MVM:
Compute load : 2.9 GFLOP
memory load : 2.9 GB

Arithmetic intensity ~ 1 (need one FLOP per byte)
```



As of today (year 2024), current GPU have memory bandwidth of approximately 2 TB/s (note this is terabytes, not terabits), and have compute bandwidth of about ~200 TFLOPS. Comparing these specs with the requirements derived above reveals that the MVM will be memory bandwidth limited, not compute bandwidth limited.

In this example, the MVM would take 1.45 ms (700 Hz maxmum AO frame rate).

{% include links.html %}
