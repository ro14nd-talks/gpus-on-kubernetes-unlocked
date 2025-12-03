---
layout: default
---

# Combined Approach Is Production Best Practice

<div class="mt-6">

```yaml {lines:false}
kind: Pod
spec:
  containers:
  - name: inference
    resources:
      limits:
        nvidia.com/gpu: 1  # <-- Ensures GPU availability
    ...
  nodeSelector:
    nvidia.com/gpu.family: ampere  # <-- Ensures correct GPU type
```

</div>

<div class="mt-4 grid grid-cols-2 gap-8">

<div class="px-4 pt-3 pb-3 bg-purple-100 dark:bg-purple-900 rounded">

<div class="mb-2">

### 🌟 Double Protection

</div>

- Resource request finds available GPU
- Label selector ensures correct type
- Scheduler validates both constraints
- Prevents wrong GPU placement

</div>

<div class="px-4 pt-3 pb-3 bg-blue-100 dark:bg-blue-900 rounded">

<div class="mb-2">

### 🎯 How It Works

</div>

- Scheduler first filters by available GPUs
- Then applies nodeSelector constraints
- Result: Right GPU type AND available
- Add tolerations if nodes are tainted

</div>

</div>

<!--
Production workloads combine both mechanisms:

Resource request (nvidia.com/gpu: 1) ensures scheduler finds node with FREE GPU

Label selector ensures that GPU is correct type (Ampere architecture)

Double protection: right GPU + available GPU

This pattern is what you'll see in most production deployments.

Timing: 90 seconds
-->
