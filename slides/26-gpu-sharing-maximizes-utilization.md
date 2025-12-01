# NVIDIA GPU Sharing Maximizes Utilization

<div class="mt-3 px-3 py-1 bg-red-100 dark:bg-red-900 rounded">

**The Challenge:**
- GPUs expensive, often <30% utilized
- Small models waste full GPU capacity
- Dev/test environments need flexibility

</div>

<div class="grid grid-cols-2 gap-12 mt-3">

<div class="px-2 py-1 bg-blue-100 dark:bg-blue-900 rounded">

**Time-Slicing**

<div class="mt-2 space-y-2">
<div>🌟 Works on any GPU</div>
<div>🌟 Flexible for dev/bursty workloads</div>
<div>⛔ No isolation between pods</div>
</div>

</div>

<div class="px-2 py-1 bg-purple-100 dark:bg-purple-900 rounded">

**MIG (Multi-Instance GPU)**

<div class="mt-2 space-y-2">
<div>🌟 Hardware isolation</div>
<div>🌟 Production-ready for multi-tenant</div>
<div>⛔ A100/H100 only</div>
</div>

</div>

</div>

<div class="mt-6 px-4 py-3 bg-blue-100 dark:bg-blue-900 rounded text-center font-semibold" v-click>

Pick sharing strategy based on isolation needs and GPU model

</div>

<!--
Why this slide matters: Before diving into technical details of time-slicing and MIG, establish WHY GPU sharing matters and WHAT options exist.

The problem: GPUs are the most expensive infrastructure component, but exclusive allocation leads to massive underutilization. A 7B model using 10GB sits on a 40GB A100 - wasting 75% of the memory.

Two approaches from NVIDIA:
1. Time-slicing: Software-based oversubscription, works on any GPU, no isolation
2. MIG (Multi-Instance GPU): Hardware partitioning, A100/H100 only, strong isolation

This overview prepares audience for detailed exploration of each technique.

Timing: 90 seconds
-->
