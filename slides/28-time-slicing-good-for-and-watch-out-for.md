# Time-Slicing Trades Isolation for Density

<div class="grid grid-cols-2 gap-12 mt-6">

<div class="px-3 py-2 bg-green-200 dark:bg-green-900 rounded">

## 🌟 Good For

<div class="mt-3">

- Dev/test & interactive notebooks
- Bursty lightweight inference
- Many small models (<5GB each)
- GPUs without MIG (T4, V100)

</div>

</div>

<div class="px-3 py-2 bg-red-200 dark:bg-red-900 rounded">

## ⛔ Watch Out For

<div class="mt-3">

- No memory isolation
- No fault isolation
- Unpredictable performance
- Multi-GPU requests ineffective

</div>

</div>

</div>

<div class="mt-4 px-4 py-2 bg-violet-200 dark:bg-violet-900 rounded">

<ul style="list-style: none; padding-left: 0;">
<li>💚 8 small NLP models (2GB each) share one 16GB T4, each handling sporadic API calls</li>
<li>⭐ Team of 10 data scientists sharing 2 GPUs for Jupyter notebooks, rarely active simultaneously</li>
<li>⛔ One 40GB LLM exhausts all GPU memory, causing OOM kills for co-located workloads</li>
</ul>

</div>

<div class="mt-4 px-4 py-2 bg-blue-200 dark:bg-blue-900 rounded text-center font-semibold" v-click>

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

Examples: Walk through the three scenarios bottom-up. Start with the green one (8 small NLP models like sentiment analysis on a T4, each ~2GB, sporadic API calls). Then the star (team of data scientists sharing GPUs for notebooks, idle 80-90% of the time). Finally the red as a warning: one greedy LLM eats all GPU memory and OOM-kills everything else, because there's no memory enforcement.

Best for: many small, bursty tasks on one GPU.

For isolation, use MIG.

Timing: 90 seconds
-->
