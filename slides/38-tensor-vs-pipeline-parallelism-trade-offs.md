---
layout: default
---

# Tensor within nodes, pipeline across nodes

<div class="overflow-x-auto">

<table class="text-lg">
<thead>
<tr>
<th><strong>Aspect</strong></th>
<th><strong>Tensor Parallelism</strong></th>
<th><strong>Pipeline Parallelism</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>Splits</strong></td>
<td>Within layers (horizontal)</td>
<td>Across layers (vertical)</td>
</tr>
<tr>
<td><strong>Communication</strong></td>
<td>After every layer</td>
<td>Between stages</td>
</tr>
<tr>
<td><strong>Bandwidth</strong></td>
<td>Very high (900 GB/s NVLink)</td>
<td>Network (12-25 GB/s, 36-72× slower)</td>
</tr>
<tr>
<td><strong>Latency</strong></td>
<td>Lower (parallel processing)</td>
<td>Higher (sequential processing)</td>
</tr>
<tr>
<td><strong>Complexity</strong></td>
<td>Simple (single pod)</td>
<td>Complex (distributed orchestration)</td>
</tr>
</tbody>
</table>

</div>

<div class="px-6 py-4 mt-4 bg-blue-100 dark:bg-blue-900 rounded text-center">
<p class="text-xl font-semibold">
<strong>Best practice:</strong> Tensor parallelism within nodes + Pipeline across nodes for models exceeding single-server capacity
</p>
</div>

<!--
Quick comparison of the two model parallelism strategies:

Tensor Parallelism (Single-Node):
- Horizontal split: all GPUs work on same layer, different parts
- Needs fast links (NVLink ~900 GB/s on H100)
- Lower latency due to parallel processing
- Single-node friendly
- Common: 2-8 GPUs within one server
- Simple Kubernetes: single pod, no special networking

Pipeline Parallelism (Multi-Node):
- Vertical split: different layers on different GPUs
- Tolerates slower links (Ethernet 12.5 GB/s, InfiniBand 25 GB/s)
- Higher latency due to sequential processing
- Multi-node capable, scales beyond single server
- Common: 2-4 stages across nodes, each stage can have multiple GPUs
- Complex orchestration: Ray.io, gang scheduling

Bandwidth hierarchy comparison:
- Intra-GPU: 900 GB/s (memory bandwidth)
- Intra-Node (NVLink): 900 GB/s → Tensor parallelism sweet spot
- Inter-Node (Ethernet): 12.5 GB/s → 72× slower, pipeline tolerates this
- Inter-Node (InfiniBand): 25 GB/s → 2× better than Ethernet, still 36× slower than NVLink

Degree details (removed from main slide):
- Tensor: 2-8 GPUs per server (limited by NVLink topology)
- Pipeline: 2-16+ GPUs across nodes (unlimited scale)

Best For context (removed from main slide):
- Tensor: Single-node deployments where model fits 2-8 GPUs
- Pipeline: Multi-node deployments for models exceeding single server

Best of both: Hybrid approach
- Use tensor within each node (leverage fast NVLink)
- Use pipeline across nodes (tolerate network latency)
- Example: 2 nodes × 8 GPUs each = 16 GPUs total
  - 4-way tensor parallelism per node (within-node cooperation)
  - 2-stage pipeline parallelism across nodes (cross-node scaling)
- Gives low latency of tensor locally, scalability of pipeline globally

Most large-scale LLM serving (GPT-4, Claude, etc.) uses this hybrid approach.

Design principle from bandwidth hierarchy:
- Prefer single-node when possible (fastest, simplest)
- When model exceeds single server: tensor within nodes, pipeline across
- Invest in InfiniBand if doing multi-node (2× faster than Ethernet)

Timing: 120 seconds
-->
