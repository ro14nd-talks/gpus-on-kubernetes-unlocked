---
layout: default
---

# Resource-Based Scheduling: Simple and Direct

```yaml {lines:false}
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
  - name: cuda-app
    image: nvidia/cuda:12.2-base
    resources:
      limits:  # <-- GPU resource request
        nvidia.com/gpu: 1
```

<div class="mt-4 grid grid-cols-2 gap-8">

<div>

## How It Works

1. Device plugin advertises `nvidia.com/gpu: 4`
2. Pod requests `nvidia.com/gpu: 1`
3. Scheduler finds node with available GPU
4. Kubelet allocates exclusive access

</div>

<div class="p-4 bg-blue-200 dark:bg-blue-900 rounded" v-click>

## Simple But Limited

Treats all GPUs equally - A100 and T4 both count as "1 GPU"

</div>

</div>

<!--
Simplest scheduling approach: request GPUs like CPU or memory.

Device plugin advertises count, pod requests count, scheduler matches.

Works great for homogeneous clusters with one GPU type.

Problem: scheduler sees all GPUs as identical. Model needs 40GB but gets scheduled on 16GB T4 → fails at runtime.

For heterogeneous clusters, we need label-based precision.

Timing: 90 seconds
-->
