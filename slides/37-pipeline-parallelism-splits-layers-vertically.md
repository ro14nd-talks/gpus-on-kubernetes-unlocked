---
layout: default
---

# Pipeline Parallelism Splits Layers Vertically

<div class="grid grid-cols-20 gap-8">

<div class="col-span-11">

<div class="grid grid-cols-1 gap-6">

<div class="px-4 pt-3 pb-3 bg-orange-200 dark:bg-orange-900 rounded">

<div class="mb-2">

### How it works

</div>

- Model layers distributed across multiple nodes
- Network: 12-25 GB/s (36-72× slower than NVLink)
- Sequential processing with micro-batching

</div>

<div class="px-4 pt-3 pb-3 bg-orange-200 dark:bg-orange-900 rounded">

<div class="mb-2">

### Trade-offs

</div>

**Advantages:**
- Unlimited scale - beyond single server
- Huge models - 100B+ parameters

**Limitations:**
- More complex - Ray.io, gang scheduling needed

</div>

</div>

</div>

<div class="col-span-9">

<div class="grid grid-cols-1 gap-6">

<div class="flex items-center justify-center">
  <img src="/images/pipeline-parallelism-multi-node.svg" alt="Pipeline Parallelism Architecture" class="max-w-full max-h-56 object-contain" />
</div>

<div class="flex items-center justify-center mt-12">
  <img src="/images/pipeline-parallelism.svg" alt="Pipeline Parallelism Concept" class="max-w-full max-h-48 object-contain" />
</div>

</div>

</div>

</div>

<!--
Pipeline parallelism: Distributes model layers across multiple nodes when the model is too large for a single server.

How it works:
- Different layers run on different GPUs/nodes (vertical split across layers)
- Layer 1-10 on Node 1, Layer 11-20 on Node 2, etc.
- Requests flow sequentially through the pipeline: Node 1 → Node 2 → Node 3
- After processing each layer group, intermediate results sent to next node
- Think: "Assembly line" - each station handles a different stage

Architecture:
- Multiple nodes connected via network (Ethernet or InfiniBand)
- 100 Gbit Ethernet = 12.5 GB/s bandwidth
- InfiniBand HDR = 25 GB/s (2× faster than Ethernet)
- Still 36-72× slower than NVLink (900 GB/s within server vs 12-25 GB/s between servers)
- PCIe 4.0 x16: 32 GB/s for CPU-GPU communication within node

Why it works despite slower network:
- Communication happens between stages (lower frequency than tensor parallelism)
- Micro-batching hides latency: while Node 2 processes batch N, Node 1 already starts batch N+1
- Overlapping computation and communication maximizes throughput
- Tolerates slower interconnects because communication is less frequent

Kubernetes deployment:
- Ray.io provides distributed coordination across nodes
- StatefulSets ensure stable network identities for inter-node communication
- Gang scheduling ensures all pods start together (using Kueue or Volcano)
- NCCL over RDMA for efficient GPU-to-GPU communication across network
- InfiniBand recommended for production (2× bandwidth vs Ethernet)

Real-world example:
- GPT-3 175B parameters exceeds single 8-GPU server capacity (~320GB needed, max 320GB per server)
- Solution: Distribute across 16+ GPUs on multiple nodes
- Each node holds consecutive layers of the model
- Models too large for single-server capacity require this approach

Design principle:
- Use when model exceeds single-server GPU capacity
- Ideal for models requiring 16+ GPUs (beyond typical 8-GPU server limit)

Limitations:
- Multi-node coordination increases operational complexity
- Higher latency than tensor parallelism due to sequential processing
- Requires specialized scheduling (gang scheduling) and orchestration tools
- Network bandwidth becomes bottleneck compared to single-server NVLink

Timing: 120 seconds
-->
