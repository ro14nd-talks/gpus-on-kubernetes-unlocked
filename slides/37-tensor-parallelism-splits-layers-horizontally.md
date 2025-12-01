---
layout: default
---

# Tensor Parallelism Splits Layers Horizontally

<div class="grid grid-cols-20 gap-8">

<div class="col-span-11">

<div class="grid grid-cols-1 gap-6">

<div class="px-4 pt-3 pb-3 bg-blue-100 dark:bg-blue-900 rounded">

<div class="mb-2">

### How it works

</div>

- Single server, all GPUs cooperate
- NVLink: 600 GB/s (50× faster than network)
- All GPUs process same layer in parallel

</div>

<div class="px-4 pt-3 pb-3 bg-blue-100 dark:bg-blue-900 rounded">

<div class="mb-2">

### Trade-offs

</div>

**Advantages:**
- Lowest latency - parallel processing
- Simplest setup - single Kubernetes pod

**Limitations:**
- Limited scale - max 8 GPUs per server

</div>

</div>

</div>

<div class="col-span-9">

<div class="grid grid-cols-1 gap-6">

<div class="flex items-center justify-center">
  <img src="/images/tensor-parallelism-single-node.svg" alt="Tensor Parallelism Architecture" class="max-w-full max-h-56 object-contain" />
</div>

<div class="flex items-center justify-center">
  <img src="/images/tensor-parallelism.svg" alt="Tensor Parallelism Concept" class="max-w-full max-h-48 object-contain" />
</div>

</div>

</div>

</div>

<!--
Tensor parallelism: Multiple GPUs in one server work together on each request.

How it works:
- All GPUs in a single server cooperate on the SAME layer simultaneously
- Each GPU handles a different portion of the layer (horizontal split)
- After processing each layer, GPUs exchange results via NVLink
- This repeats for every layer in the model
- Think: "Many hands make light work" - all GPUs chip in on each step

Architecture:
- Single server with NVLink/NVSwitch interconnect
- 600 GB/s bandwidth between GPUs (50× faster than network)
- All-to-all connectivity: any GPU can talk to any other GPU
- Intra-GPU memory bandwidth: 900 GB/s (even faster within a single GPU)

Why it's fast:
- High communication frequency (after every layer) requires super-fast interconnect
- NVLink provides the speed needed for constant GPU-to-GPU data exchange
- Parallel processing: all GPUs work simultaneously, reducing latency

Kubernetes deployment:
- Single pod request: nvidia.com/gpu: 4 (or 8)
- Scheduler finds node with enough free GPUs
- CUDA_VISIBLE_DEVICES automatically configured by runtime
- Framework (vLLM, TGI, PyTorch) initializes tensor parallelism
- No special networking needed—everything stays local within the server

Real-world example:
- Llama2-70B model: ~140GB parameters
- Single A100 GPU: 40GB memory → model won't fit!
- Solution: Split across 4 A100 GPUs (4 × 40GB = 160GB)
- Each GPU holds 1/4 of each layer
- Low latency critical for real-time inference

Design principle:
- Prefer single-node when possible (fastest, simplest)
- Ideal for models fitting 1-8 GPUs (most models up to ~70B params)

Limitations:
- Typically 2-8 GPUs per server (NVLink topology limit on most hardware)
- Can't exceed GPU count of largest node in your cluster
- If you need 16 GPUs and nodes have 8 max, must use pipeline parallelism instead

Timing: 120 seconds
-->
