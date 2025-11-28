---
layout: two-cols
---

# Time-Slicing Oversubscribes GPUs

**Concept:**
- 1 physical GPU → N virtual GPUs
- Like CPU time-sharing
- Processes interleave on GPU
- No memory isolation

<div class="mt-4">

**Configuration:**

```yaml
sharing:
  timeslicing:
    renameByDefault: true
    resources:
    - name: nvidia.com/gpu
      replicas: 8
```

</div>

::right::

<div class="mt-8">

```mermaid
graph TB
    A[Physical GPU]
    A -->|Time Slice 1| B[Pod A]
    A -->|Time Slice 2| C[Pod B]
    A -->|Time Slice 3| D[Pod C]
    A -->|Time Slice 4| E[Pod D]

    style A fill:#2196F3
    style B fill:#4CAF50
    style C fill:#4CAF50
    style D fill:#4CAF50
    style E fill:#4CAF50
```

<div class="mt-4 text-sm">

Node shows: `nvidia.com/gpu: 8` (instead of 1)

</div>

</div>

<!--
Time-slicing is oversubscription.

1 physical GPU advertised as 8 schedulable resources. Kubernetes thinks 8 GPUs exist.

Pods time-share actual GPU - similar to CPU scheduler interleaving processes.

ConfigMap sets replica count. replicas: 8 means 1 physical becomes 8 virtual.

Can rename resource to nvidia.com/gpu.shared to distinguish from exclusive.

Key: Compute time-sharing, NOT memory isolation. All pods share same VRAM.

Timing: 120 seconds
-->
