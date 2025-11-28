---
layout: default
---

# DRA Requests GPUs By Intent Not Model

<div v-click="1" class="mt-6">

<div class="px-4 pt-3 pb-3 bg-blue-100 dark:bg-blue-900 rounded">

<div class="mb-2">

### 💡 Dynamic Resource Allocation (DRA)

</div>

- Intent-based API: request by capability, not model
- Like PVC: request by class, not device
- DeviceClass resources defined by vendors
- Scheduler resolves requirements to actual GPUs

</div>

</div>

<div v-click="2" class="mt-4">

```yaml {lines:false}
apiVersion: resource.k8s.io/v1beta1
kind: ResourceClaimTemplate
metadata:
  name: high-memory-gpu
spec:
  devices:
    requests:
      - deviceClassName: gpu.nvidia.com/a100  # <-- Device class defines available GPU types
        minMemory: "40Gi"  # <-- Request by capability
        migMode: "disabled"
```

</div>

<!--
Dynamic Resource Allocation (DRA) shifts from static resource requests to intent-based allocation.

Key concept: Request by capability/intent, not hardcoded model names.

Example workload requirement: "GPU with minimum 40GB memory, MIG disabled"
Scheduler resolves this to actual GPU using DeviceClass and parameters.

DeviceClass resources are defined by GPU vendors (NVIDIA, AMD, Intel) as CRDs.
Each vendor provides a DRA driver that understands their hardware capabilities.

Similar to PVC for storage - you request by capacity/class/access mode, not specific disk model.

The beta status, adoption concerns, and future direction are covered in slide 21.

Timing: 90 seconds
-->
