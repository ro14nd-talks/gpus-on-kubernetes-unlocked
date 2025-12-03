---
layout: default
---

# Pods Claim Resources, Scheduler Allocates Devices

<div class="px-4 pt-3 pb-3 bg-purple-100 dark:bg-purple-900 rounded">

### 🔄 Template-Based Flow

<ol>
<li>Create ResourceClaimTemplate defining GPU requirements</li>
<li>Reference template in Pod spec</li>
</ol>

</div>

<div class="mt-6 grid grid-cols-2 gap-8">

<div>

```yaml {lines:false}
apiVersion: resource.k8s.io/v1beta1
kind: ResourceClaimTemplate
metadata:
  name: high-memory-gpu
spec:
  devices:
    requests:
      - deviceClassName: gpu.nvidia.com/a100
        minMemory: "40Gi"
        allocationMode: "exclusive"
```

</div>

<div>

```yaml {lines:false}
kind: Pod
spec:
  resourceClaims:  # <-- Reference template
  - name: gpu-claim
    resourceClaimTemplateName: high-memory-gpu
  containers:
  - name: inference
    resources:
      claims:  # <-- Use gpu-claim defined above
      - name: gpu-claim
```

</div>

</div>

<div v-click class="mt-4 px-4 pt-3 pb-3 bg-blue-100 dark:bg-blue-900 rounded">

- Scheduler allocates matching device automatically
- Pattern mirrors PVC/PV binding

</div>

<!--
DRA uses template-based pattern for resource allocation.

Left: ResourceClaimTemplate defines requirements (device class, memory, etc.)
Right: Pod references template via resourceClaims, uses it in containers

Flow:
1. Admin creates ResourceClaimTemplate (reusable)
2. Pod references template by name
3. Scheduler creates ResourceClaim from template
4. Allocates actual GPU matching requirements
5. Pod container claims the allocated resource

Similar to PVC/PV pattern: define requirements, bind to actual resource.

Timing: 90 seconds
-->
