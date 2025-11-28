---
layout: section
---

# GPU Sharing

## Sub-GPU Allocation

<!--
So far: exclusive GPU access - one pod gets one whole GPU.

But what if workload only needs 20% of GPU? Multiple small models on one card?

GPUs are expensive. Sharing them efficiently is critical.

NVIDIA provides two mechanisms: time-slicing and MIG.

Timing: 15 seconds
-->
