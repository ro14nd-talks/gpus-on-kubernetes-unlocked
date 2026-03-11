---
layout: default
---

# Match GPUs by Memory Size with Node Affinity

<div class="mt-6">

```yaml {4-7,lines:false}
affinity:
  nodeAffinity:
    ...
    matchExpressions:
    - key: nvidia.com/gpu.memory
      operator: Gt  # Greater than
      values: ["40000"]
```

</div>

<div class="mt-4 grid grid-cols-2 gap-8">

<div class="px-4 pt-3 pb-3 bg-green-200 dark:bg-green-900 rounded">

<div class="mb-2">

### 🌟 Strengths

</div>

- Rich operators (Gt, Lt, In, NotIn)
- Express minimum requirements
- Allow multiple GPU options

</div>

<div class="px-4 pt-3 pb-3 bg-red-200 dark:bg-red-900 rounded">

<div class="mb-2">

### ⛔ Limitations

</div>

- More verbose than nodeSelector
- Complex syntax to learn
- Harder to debug failures

</div>

</div>

<div v-click class="mt-4 py-2 px-3 bg-blue-200 dark:bg-blue-900 rounded text-center font-semibold">

**Best For:** Flexible GPU requirements (>40GB memory, any Ampere/Hopper)

</div>

<!--
Node affinity unlocks rich expressions: 'greater than', 'in list', 'exists' operators.

This spec: "GPU memory MUST be > 40000 MiB (40GB)"

Matches: A100 40GB, A100 80GB, H100 80GB
Rejects: T4 16GB, A10 24GB

Can also use preferredDuringScheduling for soft preferences: "prefer Hopper but Ampere is okay"

Use case: Workload needs minimum memory but flexible on GPU model.

Timing: 120 seconds
-->
