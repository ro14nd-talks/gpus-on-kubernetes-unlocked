---
layout: image-right
image: /images/ch05-gpu-model-parallelism.png
backgroundSize: contain
---

# Tensor vs Pipeline Parallelism Trade-offs

| **Aspect** | **Tensor** | **Pipeline** |
|------------|------------|--------------|
| **Splits** | Within layers | Across layers |
| **Communication** | After every layer | Once per stage |
| **Bandwidth** | Very high (NVLink) | Moderate (Ethernet) |
| **Latency** | Lower (parallel) | Higher (sequential) |
| **Best For** | Single-node | Multi-node |
| **Degree** | 2-8 GPUs | 2-16+ GPUs |

**Hybrid:** Tensor within nodes + Pipeline across nodes

<!--
Quick comparison:

Tensor: Needs fast links (NVLink), lower latency, single-node friendly. Common: 4-8 way within server.

Pipeline: Tolerates slower links, higher latency (sequential), multi-node capable. Common: 2-4 stages across nodes.

Best of both: Hybrid approach

Use tensor within each node (fast NVLink).
Use pipeline across nodes (tolerate network).

Example: 2 nodes, 8 GPUs each. 4-way tensor per node, 2-stage pipeline across nodes = 16 GPUs total.

Gives low latency of tensor locally, scalability of pipeline globally.

Most large-scale LLM serving uses this hybrid.

Timing: 120 seconds
-->
