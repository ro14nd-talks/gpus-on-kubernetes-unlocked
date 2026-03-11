---
layout: default
---

# Scheduling GPUs Is Only Half the Battle

<div class="mt-8 grid grid-cols-[45%_55%] gap-8">

<div class="px-4 pt-3 pb-3 bg-green-200 dark:bg-green-900 rounded">

<div class="mb-2">

### 🌟 What We Have So Far

</div>

- Discovery labels nodes with GPU capabilities
- Device plugins expose GPUs as schedulable resources
- Scheduler can place GPU workloads on correct nodes

</div>

<div class="px-4 pt-3 pb-3 bg-red-200 dark:bg-red-900 rounded">

<div class="mb-2">

### ⛔ What's Still Missing for Production

</div>

- Driver installation and updates
- Container runtime configuration
- GPU health monitoring
- Lifecycle management across cluster

</div>

<div v-click class="col-span-2 mt-4 py-2 px-3 bg-blue-200 dark:bg-blue-900 rounded text-center font-semibold">

Manual SSH into nodes doesn't scale, we need declarative automation

</div>

</div>

<!--
We've built discovery and allocation layers. GPUs are schedulable. But production needs more.

What we have:
- NFD/GFD label nodes with GPU details
- Device plugins expose nvidia.com/gpu resource
- Scheduler knows which nodes have GPUs and can place pods

What's missing:
- Driver installation: NVIDIA kernel modules, CUDA libraries must match kernel version
- Container runtime: Need NVIDIA Container Toolkit to inject GPU devices into containers
- Monitoring: Track GPU health, temperature, ECC errors
- Lifecycle management: Keep driver versions, configs synchronized across all GPU nodes

Traditional approach: SSH into each GPU node, install drivers manually, configure runtime, set up monitoring.

Problems:
- Configuration drift across nodes
- Driver/kernel version mismatches
- Manual updates don't scale to 100+ node clusters
- Not declarative, not GitOps-friendly

This is where the GPU Operator comes in—it automates ALL of this declaratively.

Timing: 90 seconds
-->
