---
layout: default
---

# GPUs on Kubernetes: Unlocked

<div class="grid grid-cols-[80%_20%] gap-4 h-[calc(100%-12rem)]">

<div class="flex items-center justify-center pb-20">
  <img src="/images/complete-stack.svg" alt="Complete GPU on Kubernetes Stack" class="w-full max-h-[450px] object-contain" />
</div>

<div class="flex flex-col items-center justify-center gap-4 pb-20">
  <img src="/images/book-cover.png" class="w-full rounded shadow-lg" alt="Book Cover" />
  <img src="/images/book-qr.png" class="w-full" alt="QR Code" />
  <a href="https://bit.ly/genaik8s" target="_blank" class="font-mono text-xs text-center opacity-75 hover:opacity-100 transition no-underline break-all -mt-2">bit.ly/genaik8s</a>
</div>

</div>

<!--
PRESENTER NOTES - Slide 39: GPUs on Kubernetes: Unlocked

This is the main conclusion slide - synthesizes the complete journey from gap to solution.

KEY MESSAGE:
You now have the complete pattern library to bridge the GPU scheduling gap and build production-ready GPU infrastructure.

TALKING POINTS (120 seconds):

1. FOUNDATION - Three Layers Bridge the Gap (30s)
   - Discovery: Makes GPUs visible through labels (NFD + GFD)
   - Allocation: Makes them schedulable through device plugins
   - Orchestration: Automates everything with GPU Operator (ClusterPolicy)
   - Result: Production-ready GPU scheduling on Kubernetes

2. OPTIMIZATION - Sharing & Scaling Maximize Value (40s)
   - GPU Sharing: Time-slicing and MIG maximize ROI on expensive hardware
   - Multi-GPU Patterns: Data, tensor, pipeline parallelism enable scale from single GPU to clusters
   - Decision Framework: Match pattern to workload (model size, latency requirements)
   - Result: Full infrastructure potential unlocked

3. CULTURAL SHIFT - GPUs as Shared Infrastructure (30s)
   - Treat GPUs as shared, policy-driven substrate (not pets)
   - Use declarative automation (ClusterPolicy, not SSH)
   - Start with proven patterns (device plugins, GPU Operator)
   - Adopt new tools as they mature (DRA, KAI scheduler, Kueue)

TRANSITION TO NEXT SLIDE:
"Let me give you five concrete strategies to maximize GPU utilization in your clusters..."

VISUAL CUES:
- Complete stack diagram shows all layers working together
- Single summary equation emphasizes how pieces fit
- Book reference for deeper dive into full GenAI stack

This was Chapter 3, one piece of the GenAI on Kubernetes puzzle. The book covers infrastructure, model serving, observability, security, cost management.

Timing: 120 seconds
-->
