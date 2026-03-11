---
layout: default
---

# Hardware Labels Enable Intelligent GPU Scheduling

<div class="grid grid-cols-2 gap-8 mt-8">
<div>

**The Challenge:**
- Different GPU models per node
- T4s with 16GB, A100s with 40GB, H100s with 80GB
- Scheduler picks randomly without labels

**What Scheduler Needs:**
- Architecture (Ampere, Hopper)
- Memory and capabilities (MIG, NVLink)

</div>
<div>

```yaml {maxHeight:'400px',fontSize:'11px'}
apiVersion: v1
kind: Node
metadata:
  name: gpu-node-01
  labels:
    # NFD
    feature.node.kubernetes.io/pci-10de.present: "true"

    # GFD
    nvidia.com/gpu.product: A100-SXM4-40GB
    nvidia.com/gpu.memory: "40537"
    nvidia.com/gpu.family: ampere
    nvidia.com/gpu.count: "8"
    nvidia.com/cuda.runtime.major: "12"
    nvidia.com/mig.capable: "true"
...
```

</div>
</div>

<div v-click class="mt-8 p-4 bg-blue-200 dark:bg-blue-900 rounded text-center font-semibold">

**The Solution:** Automatic hardware detection → Node labels

</div>

<!--
Imagine a cluster with mixed GPU types - some nodes have T4s, others A100s, maybe some H100s.

Without labels, scheduler picks randomly. Pod fails because GPU doesn't meet requirements.

Two tools solve this: NFD for general hardware, GFD for GPU-specific details.

Timing: 90 seconds
-->
