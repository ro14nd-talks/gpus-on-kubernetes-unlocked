# Time-Slicing Trades Isolation for Density

<div class="grid grid-cols-2 gap-12 mt-6">

<div class="px-3 py-2 bg-green-100 dark:bg-green-900 rounded">

## 🌟 Good For

<div class="mt-3">

- Dev/test & interactive notebooks
- Bursty lightweight inference
- Many small models (<5GB each)
- GPUs without MIG (T4, V100)

</div>

</div>

<div class="px-3 py-2 bg-red-100 dark:bg-red-900 rounded">

## ⛔ Watch Out For

<div class="mt-3">

- No memory isolation
- No fault isolation
- Unpredictable performance
- Multi-GPU requests ineffective

</div>

</div>

</div>

<div class="mt-4 px-4 py-2 bg-violet-100 dark:bg-violet-900 rounded">

<ul style="list-style: none; padding-left: 0;">
<li>💚 8 small models (2GB each) share one GPU with bursty usage</li>
<li>⭐ Dev notebooks sitting idle 80% of time</li>
<li>⛔ Single large LLM (40GB) blocks all other workloads</li>
</ul>

</div>

<div class="mt-4 px-4 py-2 bg-blue-100 dark:bg-blue-900 rounded text-center font-semibold" v-click>

For isolation guarantees → use MIG instead

</div>

<!--
Time-slicing works great for certain patterns:

Good: Many small workloads that are bursty - inference requests come in waves, notebooks run intermittently. They don't all peak at once. Also good for older GPUs (T4, V100) without MIG support.

Watch out for:
- No memory isolation: One pod can OOM others (one LLM loading 15GB leaves no room for others)
- No fault isolation: Bad memory access crashes GPU → all pods fail (safety-critical workloads problematic)
- Unpredictable performance: Contention if all pods active simultaneously
- Multi-GPU requests ineffective: Requesting 2 virtual GPUs ≠ 2× performance (could get slices from 2 different physical GPUs with no speedup)

Best for: many small, bursty tasks on one GPU.

For isolation, use MIG.

Timing: 90 seconds
-->
