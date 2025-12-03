---
layout: default
---

# Taints Protect GPU Nodes From CPU Workloads

<div class="mt-6 grid grid-cols-2 gap-8">

<div>

```yaml {lines:false}
kind: Node
spec:
  taints:  # <-- Reject non-GPU pods
  - key: nvidia.com/gpu
    value: "true"
    effect: NoSchedule
  ...
```

</div>

<div>

```yaml {lines:false}
kind: Pod
spec:
  tolerations:  # <-- Opt-in to GPU nodes
  - key: nvidia.com/gpu
    operator: Exists
    effect: NoSchedule
  ...
```

</div>

</div>

<div class="mt-4 grid grid-cols-2 gap-8">

<div class="px-4 pt-3 pb-3 bg-green-100 dark:bg-green-900 rounded">

<div class="mb-2">

### 🌟 Strengths

</div>

- Protects expensive GPU resources
- Explicit opt-in model
- Complements resource requests
- Prevents accidental placement

</div>

<div class="px-4 pt-3 pb-3 bg-red-100 dark:bg-red-900 rounded">

<div class="mb-2">

### ⛔ Limitations

</div>

- Requires manual toleration setup
- Extra YAML complexity
- Easy to misconfigure
- No warning on missing toleration

</div>

</div>

<div v-click class="mt-4 py-2 px-3 bg-blue-100 dark:bg-blue-900 rounded text-center font-semibold">

**Best For:** Heterogeneous clusters with expensive GPU nodes

</div>

<!--
Taints protect expensive GPU nodes from running cheap workloads.

Left example: Node taint with NoSchedule effect rejects pods without matching toleration.
Right example: Pod toleration opts-in to GPU nodes.

STRENGTHS:
1. Protects expensive GPU resources - Prevents $30K/month GPU nodes from running cheap workloads like nginx
2. Explicit opt-in model - Only pods with tolerations can access GPU nodes - intentional, not accidental
3. Complements resource requests - Works alongside nvidia.com/gpu: 1 for layered protection
4. Prevents accidental placement - Random pods won't land on GPU nodes during scale-up events

LIMITATIONS:
1. Requires manual toleration setup - Every GPU pod must add tolerations - easy to forget
2. Extra YAML complexity - Additional stanza in every GPU pod spec
3. Easy to misconfigure - Typo in taint key/value/effect breaks scheduling silently
4. No warning on missing toleration - Pod just won't schedule - no proactive feedback

Best practice: Taint all GPU nodes in heterogeneous clusters to prevent resource waste.

Timing: 90 seconds
-->
