---
title: Image streams
keywords: streams
last_updated: Mar 4, 2024
tags: [streams]
summary: "Numerical data (images, vectors, matrices) are stored in shared memory for low-latency read/write by real-time processes"
sidebar: userguide_sidebar
permalink: milk_image_streams.html
folder: milk
---


# Rationale

Numerical data (images, vectors, matrices) are stored in shared memory for low-latency read/write by real-time processes. They are embedded in a structure containing, in addition to the data itself, the corresponding metadata (for example, image size, data type), as well as semaphores and counters for synchronization. This is implemented with the ImageStreamIO library.


# Data Synchronization

By embedding semaphores and counters within the data stream structure, milk combines modularity with high low-latency data synchronization between processes.

{% include image.html file="milk_streams.svg" 
caption="Synchronization between data (streams) and processes" %}


A processing pipeline is a chain of individual processes connected together by data streams. Each process can have multiple input and output streams, but has a single triggering input (usually the semaphore of an input data stream).

This modular architecture allows for deployment of complex data processing pipelines across multiple CPU cores, and even between multiple computers. Each process can be hosted on an individual CPU core, or span multiple cores.


{% include links.html %}
