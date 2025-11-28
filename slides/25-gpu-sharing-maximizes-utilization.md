# GPU Sharing Maximizes Utilization

<div class="grid grid-cols-2 gap-8 mt-8">

<div class="p-4 bg-orange-100 dark:bg-orange-900 rounded">

**The Challenge:**
- GPUs expensive, often <30% utilized
- Small models waste full GPU capacity
- Dev/test environments need flexibility

</div>

<div class="p-4 bg-blue-100 dark:bg-blue-900 rounded">

**NVIDIA Sharing Techniques:**

**Time-Slicing**
- ✓ Works on any GPU
- ✓ Flexible for dev/bursty workloads
- ✗ No isolation between pods

**MIG (Multi-Instance GPU)**
- ✓ Hardware isolation
- ✓ Production-ready for multi-tenant
- ✗ A100/H100 only

</div>

</div>

<div class="mt-8 px-4 py-3 bg-green-100 dark:bg-green-900 rounded text-center font-semibold" v-click>

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
