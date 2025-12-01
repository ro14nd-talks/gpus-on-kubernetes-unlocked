---
layout: default
---

<h1 style="font-size: 2.1rem;">Resource Requests Trade Simplicity for GPU Precision</h1>

<div class="mt-8 grid grid-cols-2 gap-8">

<div v-click="1" class="px-4 pt-3 pb-3 bg-green-100 dark:bg-green-900 rounded">

<div class="mb-2">

### 🌟 Strengths

</div>

- Simple syntax
- Native K8s integration
- No extra labels needed
- Works with familiar resource model
- Good for homogeneous fleets

</div>

<div v-click="2" class="px-4 pt-3 pb-3 bg-red-100 dark:bg-red-900 rounded">

<div class="mb-2">

### ⛔ Limitations

</div>

- All GPUs look identical
- Can't request "A100" specifically
- Can't specify memory size
- Can't request compute capability
- GPU-blind scheduling

</div>

</div>

<div v-click="3" class="mt-4 py-2 px-3 bg-blue-100 dark:bg-blue-900 rounded text-center font-semibold">

**Best For:** Dev/test environments with single GPU type

</div>

<!--
Resource-based scheduling shines in simplicity.

If all your nodes have identical GPUs (all T4s or all A100s), this is perfect.

But in production heterogeneous clusters with mix of GPU types, the limitations become painful.

Scheduler can't distinguish between a $5K T4 and a $30K H100.

For precise placement, combine with labels.

Timing: 90 seconds
-->
