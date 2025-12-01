---
layout: default
---

# Pipeline Parallelism Splits Layers Vertically

<div class="grid grid-cols-2 gap-8">

<div>

**Across multiple nodes:**
- Network interconnect (12.5 GB/s)
- Different layers per GPU
- Sequential processing

**Requirements:**
- Ray.io, StatefulSets, gang scheduling
- NCCL configuration
- Micro-batching to hide latency

**Trade-off:** Communication overhead

**Benefit:** Scales beyond single server

</div>

<div>

<img src="/images/pipeline-parallelism-multi-node.svg" alt="Pipeline Parallelism Multi-Node" style="width: 100%; height: auto;" />

</div>

</div>

<!--
Pipeline parallelism stretches model across nodes when no box is big enough.

Split vertically by layers: GPU 1 layers 1-10, GPU 2 layers 11-20, etc.

Request flows sequentially through pipeline. Micro-batching hides latency.

Communication between stages via network (InfiniBand or Ethernet).

Network: 100 Gbit Ethernet = 12.5 GB/s vs 600 GB/s NVLink - 50× slower
Why it works: Lower communication frequency (between stages, not every layer)

Use case: GPT-3 175B params - exceeds single 8-GPU server, needs 16+ GPUs across nodes.

Orchestration: Ray.io for coordination, gang scheduling ensures all pods start together.

Timing: 120 seconds
-->
