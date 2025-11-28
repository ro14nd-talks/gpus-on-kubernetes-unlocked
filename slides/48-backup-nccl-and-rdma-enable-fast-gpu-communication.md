---
layout: default
---

# NCCL and RDMA Enable Fast GPU Communication

**NCCL** (NVIDIA Collective Communication Library):
- Collective primitives: all-reduce, broadcast, all-gather
- Used by PyTorch, vLLM internally
- Optimized for GPU topologies

**RDMA** (Remote Direct Memory Access):
- Bypass CPU for direct memory access
- Requires InfiniBand or RoCE adapters
- Significantly reduces multi-node latency

**Best practice:** NCCL over RDMA for multi-node

<!--
NCCL provides collective communication primitives for distributed training:
- all-reduce: Combine gradients across GPUs
- broadcast: Send parameters from one to all
- all-gather: Collect data from all GPUs

RDMA benefits: Direct memory-to-memory between nodes, bypasses CPU and kernel, reduces latency 10× vs TCP.

Requires InfiniBand or RoCE adapters. Essential for large-scale training.

Timing: 120 seconds
-->
