---
layout: default
---

# GFD Delivers Model-Specific GPU Information

<div class="mt-8">

- **GPU Feature Discovery (GFD)** - NVIDIA graphics cards only
- AMD alternative: AMD GPU Node Labeler

</div>

<div class="grid grid-cols-2 gap-4 mt-4">

<div>

**Features:**

- GPU model name
- Memory capacity (MiB)
- Architecture family
- CUDA driver version
- MIG and time-slicing support

</div>

<div>

```yaml {fontSize:'10px'}
nvidia.com/gpu.count: "4"
nvidia.com/gpu.product: "A100-SXM4-40GB"
nvidia.com/gpu.memory: "40537"
nvidia.com/gpu.family: "ampere"
nvidia.com/gpu.compute.major: "8"
nvidia.com/gpu.compute.minor: "0"
nvidia.com/cuda.driver-version.full: "525.105.17"
nvidia.com/cuda.driver.major: "525"
nvidia.com/cuda.runtime-version.major: "12"
nvidia.com/cuda.runtime-version.minor: "0"
nvidia.com/mig.capable: "true"
```

</div>

</div>

<div v-click class="mt-4 p-4 bg-blue-100 dark:bg-blue-900 rounded text-center font-semibold">

GFD is part of the **NVIDIA GPU Operator**

</div>

<!--
GPU Feature Discovery adds the detailed GPU information NFD lacks.

Uses nvidia-smi to gather comprehensive data: exact model, memory size, architecture, CUDA capabilities, MIG support.

These labels enable fine-grained scheduling - target "A100 with 40GB+" or "Hopper architecture only".

GFD is part of the GPU Operator we'll cover later.

Timing: 90 seconds
-->
