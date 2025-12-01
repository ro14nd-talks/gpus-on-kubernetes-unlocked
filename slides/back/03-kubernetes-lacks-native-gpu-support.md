---
layout: center
---

# Kubernetes Lacks Native GPU Support

<div class="grid grid-cols-2 gap-12 mt-12 px-8 items-stretch">

<div class="border-3 border-green-500 rounded-lg p-8 bg-green-50 dark:bg-green-900/20 flex flex-col justify-between">

<div class="text-2xl font-bold text-center mb-6 text-green-700 dark:text-green-400">
Kubernetes Native
</div>

<div class="text-lg space-y-1 flex-1 flex flex-col justify-center pl-8">

• CPU & Memory

• Standard Resources

</div>

<div class="text-center mt-6 text-xl font-semibold text-green-600">
✓ Built-in
</div>

</div>

<div class="border-3 border-orange-500 rounded-lg p-8 bg-orange-50 dark:bg-orange-900/20 flex flex-col justify-between">

<div class="text-2xl font-bold text-center mb-6 text-orange-700 dark:text-orange-400">
LLM Requirements
</div>

<div class="text-lg space-y-1 flex-1 flex flex-col justify-center pl-8">

• GPU Compute

• VRAM & Interconnects

</div>

<div class="text-center mt-6 text-xl font-semibold text-orange-600">
🔧 Extensions Needed
</div>

</div>

</div>

<!--
Kubernetes was designed around CPU and memory - these are first-class resources with native support.

But large language models live and die by GPUs. Until the control plane knows what accelerators exist on each node, the scheduler can't place pods intelligently.

This is the GPU gap we need to bridge.

Timing: 90 seconds
-->
