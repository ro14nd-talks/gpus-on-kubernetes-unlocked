---
layout: default
---

# Multi-Node Topology and Bandwidth Hierarchy

```mermaid
graph LR
    A[NVLink within GPU] -->|900 GB/s| B[GPU-to-GPU same card]
    C[NVSwitch within node] -->|600 GB/s| D[GPU-to-GPU same server]
    E[PCIe 4.0 x16] -->|32 GB/s| F[CPU-GPU]
    G[100 Gbit Ethernet] -->|12.5 GB/s| H[Node-to-Node]
    I[InfiniBand HDR] -->|25 GB/s| J[Node-to-Node<br/>with RDMA]

    style C fill:#4CAF50
    style G fill:#FF5252
    style I fill:#FF9800
```

<div class="grid grid-cols-3 gap-4 mt-8">

<div>

### Intra-GPU
**900 GB/s**
Memory bandwidth

</div>

<div>

### Intra-Node
**600 GB/s**
NVSwitch
→ Tensor parallelism

</div>

<div>

### Inter-Node
**12.5 GB/s** (Ethernet)
**25 GB/s** (InfiniBand)
→ Pipeline parallelism

</div>

</div>

<!--
Bandwidth hierarchy visualization:

Within GPU: 900 GB/s memory
Between GPUs same node (NVSwitch): 600 GB/s
Over PCIe: 32 GB/s
Between nodes (Ethernet): 12.5 GB/s
Between nodes (InfiniBand): 25 GB/s

Massive drop crossing network.

Why:
- Tensor parallelism stays within nodes
- Pipeline parallelism goes across nodes
- InfiniBand helps but can't match NVLink

Design guidance:
- Prefer single-node if possible
- Tensor within nodes, pipeline across
- Invest in InfiniBand if multi-node required

Timing: 90 seconds
-->
