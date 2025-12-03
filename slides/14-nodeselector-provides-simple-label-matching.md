---
layout: default
---

# Request Specific GPU Models with nodeSelector

<div class="mt-6">

```yaml {3-4,lines:false}
spec:
  ...
  nodeSelector:
    nvidia.com/gpu.product: Tesla-T4  # <-- Exact match required
  ...
```

</div>

<div class="mt-4 grid grid-cols-2 gap-8">

<div class="px-4 pt-3 pb-3 bg-green-100 dark:bg-green-900 rounded">

<div class="mb-2">

### 🌟 Strengths

</div>

- Simple, explicit syntax
- Direct model specification
- Easy to debug and troubleshoot

</div>

<div class="px-4 pt-3 pb-3 bg-red-100 dark:bg-red-900 rounded">

<div class="mb-2">

### ⛔ Limitations

</div>

- All-or-nothing matching
- No alternatives ("T4 OR A10")
- Can't express ranges

</div>

</div>

<div v-click class="mt-4 py-2 px-3 bg-blue-100 dark:bg-blue-900 rounded text-center font-semibold">

**Best For:** Workloads tested on specific GPU model

</div>

<!--
NodeSelector: simplest label-based approach - direct key-value match.

Pod specifies exact requirement, scheduler ONLY considers nodes with that label.

Example: Workload tested on T4s in dev, wants same in production for consistency.

Limitation: Can't express "T4 OR A10" or "memory > 24GB". All-or-nothing.

For flexible requirements, use node affinity.

Timing: 90 seconds
-->
