---
layout: default
---

# Time-Slicing: Good For and Watch Out For

<div class="grid grid-cols-2 gap-8">

<div>

## ✅ Good For

- Bursty workloads
- Lightweight inference
- Development environments
- Interactive notebooks
- Many small models
- GPUs that don't support MIG

**Example:** 8 small model APIs, each uses 10% GPU compute and 2GB memory

</div>

<div>

## ❌ Watch Out For

- **No memory isolation**
  One pod can OOM others

- **No fault isolation**
  GPU crash affects all pods

- **Unpredictable performance**
  Contention if all pods active

- **Multi-GPU requests problematic**
  Requesting 2 virtual GPUs ≠ 2× performance

**Not ideal:** Large LLMs needing full VRAM

</div>

</div>

<!--
Time-slicing works great for certain patterns:

Good: Many small workloads that are bursty - inference requests come in waves, notebooks run intermittently. They don't all peak at once. Also good for older GPUs (T4, V100) without MIG support.

Bad: Large models needing all memory. One LLM loading 15GB leaves no room for others.

Bad: Safety-critical workloads. Bad memory access crashes GPU → all pods fail.

Bad: Requesting multiple virtual GPUs. You might think "2 GPUs for 2× performance" but could get slices from 2 different physical GPUs with no speedup.

Best for: many small, bursty tasks on one GPU.

For isolation, use MIG.

Timing: 120 seconds
-->
