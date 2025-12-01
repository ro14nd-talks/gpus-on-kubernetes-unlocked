---
layout: default
---

# Tensor Parallelism Splits Layers Horizontally

<div class="grid grid-cols-2 gap-8">

<div>

**Inside one server:**
- NVLink interconnect (~600 GB/s)
- All GPUs process same layer
- Frequent communication

**Kubernetes:**
```yaml
resources:
  limits:
    nvidia.com/gpu: 4
```

**Use case:** Large models, low-latency critical

</div>

<div>

<img src="/images/tensor-parallelism-single-node.svg" alt="Tensor Parallelism Single Node" style="width: 100%; height: auto;" />

</div>

</div>

<!--
Tensor parallelism is tight cooperation - multiple GPUs on same request simultaneously.

Split model layers horizontally across GPUs. All GPUs process SAME layer at same time, different parts.

After each layer, exchange results via NVLink. High communication frequency.

Why single-server: NVLink ~600 GB/s - 50× faster than network. Required for frequent syncs.

Use case: Llama2-70B needs 2-4 A100 GPUs. Tensor parallelism splits across GPUs in one server.

Limitation: Typically 2-8 GPUs per server (NVLink topology limit).

When model exceeds single server, span multiple nodes.

Timing: 120 seconds
-->
