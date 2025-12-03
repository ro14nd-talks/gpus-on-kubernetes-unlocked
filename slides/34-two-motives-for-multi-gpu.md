---
layout: default
---

# Two Dimensions Define Three GPU Strategies

<div class="flex justify-center mt-12 w-full">
  <img src="/images/gpu-parallelism.svg" class="w-full max-w-3xl" alt="Multi-GPU Parallelism Taxonomy" />
</div>

<!--
This slide introduces the taxonomy of multi-GPU parallelism strategies. There are two key dimensions:

1. Strategy: Do we replicate the entire model (Data Parallelism) or split it (Model Parallelism)?
   - Data Parallelism: Multiple complete copies for throughput scaling
   - Model Parallelism: Split a single model when it's too large for one GPU

2. Deployment: Single-node vs multi-node determines the communication patterns
   - Tensor Parallelism: Single-node, layer-level horizontal splits, NVLink
   - Pipeline Parallelism: Multi-node capable, layer-level vertical splits, Ethernet

This is an intro slide. Next slides cover:
- Slide 35: Data Parallelism throughput scaling characteristics
- Slides 36-37: Model Parallelism strategies (Tensor and Pipeline)
- Slide 38: Comparison and best practices

Timing: 90 seconds
-->
