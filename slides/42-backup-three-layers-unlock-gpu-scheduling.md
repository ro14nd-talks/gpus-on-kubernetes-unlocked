---
layout: center
---

# Three Layers Unlock GPU Scheduling

**Discovery:** NFD + GFD → Vocabulary

**Allocation:** Device plugins → Schedulable resources

**Orchestration:** GPU Operator → Declarative

**Patterns:** Match layout to workload

**Start:** GPU Operator + resource-based scheduling

<!--
Complete recap:

1. Discovery: NFD (general hardware), GFD (GPU-specific) give scheduler visibility

2. Allocation: Device plugins expose as nvidia.com/gpu, label-based selection for precision

3. Orchestration: GPU Operator automates drivers, runtime, monitoring - keeps declarative

4. Patterns: Choose based on model size and latency - throughput scaling, tensor parallelism, pipeline parallelism, GPU sharing

Where to start:
1. Deploy GPU Operator (handles all layers)
2. Simple resource-based scheduling
3. Add labels for heterogeneous fleets
4. Choose pattern for workload
5. Monitor with DCGM, optimize

Final thought: "When your next workload demands GPU acceleration, you now know how discovery, allocation, and sharing fit together."

Timing: 90 seconds
-->
