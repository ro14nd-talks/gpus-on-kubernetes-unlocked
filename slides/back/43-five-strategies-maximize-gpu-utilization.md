---
layout: default
---

# Five Strategies Maximize GPU Utilization

<v-clicks>

- **Memory:** Pre-allocate large blocks, avoid fragmentation
- **Utilization:** MIG/time-slicing for underutilized GPUs
- **Topology:** Use `nvidia-smi topo -m` for NVLink mapping
- **Scaling:** KEDA, HPA, gang scheduling
- **Monitoring:** DCGM → Prometheus metrics

</v-clicks>

<!--
Five practical strategies for production:

1. Memory: Pre-allocate to avoid fragmentation, use vLLM PagedAttention for KV cache, monitor for defrag needs

2. Utilization: Deploy MIG/time-slicing if GPUs underutilized, consolidate models, use quantization (4-bit/8-bit reduces memory 50-87%)

3. Topology: Run nvidia-smi topo -m to see interconnect, pin GPU indices for optimal NVLink grouping

4. Scaling: Use KEDA/HPA/Knative for throughput, gang scheduling (Kueue, Volcano) for multi-pod jobs, keep warm pods

5. Monitoring: DCGM Exporter for Prometheus, watch utilization, memory, temperature, ECC errors

Timing: 90 seconds
-->
