---
layout: section
---

# Discovery Layer

## Finding GPUs in Your Cluster

<div class="flex justify-center mt-12">
  <img src="/images/layered-archictecture-discovery.svg" class="h-30 opacity-60" alt="Discovery layer highlighted in the three-layer architecture" />
</div>

<!--
We just saw the three-layer architecture. Let's start at the foundation: the Discovery Layer.

Before Kubernetes can schedule GPU workloads, it needs to know which nodes have GPUs and what their capabilities are.

This is where NFD and GFD come in—they label nodes with hardware capabilities.

Timing: 20 seconds
-->
