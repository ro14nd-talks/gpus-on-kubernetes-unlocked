# Brainstorm: GPUs on Kubernetes Unlocked

## Talk Metadata
**Title**: GPUs on Kubernetes Unlocked
**Format**: Conference talk
**Audience**: Kubernetes practitioners who need to deploy GPU workloads
**Objectives**: Deploy and optimize GPU workloads on Kubernetes

## Abstract (CFP Submitted)
Kubernetes speaks CPU and memory out-of-the-box, but large-language-model workloads live or die by GPUs. We start by framing the gap: until the control-plane knows what accelerators exist on each node, the scheduler can't place Pods intelligently. Node Feature Discovery, extended with GPU awareness, gives Kubernetes that vocabulary, attaching labels for architecture, memory and sharing mode. The device-plugin framework then turns each accelerator—or fraction of one—into a schedulable resource, while the GPU Operator keeps drivers, runtimes and metrics aligned so clusters stay declarative. Dynamic Resource Allocation lifts the abstraction another step by letting workloads request a "high-memory GPU" and leaving the exact match to the scheduler. Why bother? Model size and latency targets dictate very different layouts. Some deployments thrive when several GPUs cooperate inside a single server; others must stretch across nodes when no box is big enough. At the opposite end of the scale, time-slicing and Multi-Instance GPU let lighter tasks share silicon safely, squeezing more value from every watt. By the end you'll understand how discovery, allocation and sharing fit together and where to start when your next tuning run or inference service demands more accelerator muscle than Kubernetes exposes today.

## Core Narrative Arc (from CFP)

### Act 1: The Gap
**Problem**: Kubernetes knows CPU/memory natively, but GPUs are invisible by default
**Impact**: Scheduler can't place GPU workloads intelligently
**Stakes**: LLM workloads "live or die by GPUs"

### Act 2: Building the Foundation (Layers)

#### Layer 1: Discovery - Giving Kubernetes a Vocabulary
- **Node Feature Discovery (NFD)**: General-purpose hardware detection
- **GPU Feature Discovery (GFD)**: GPU-specific labels (architecture, memory, sharing mode)
- **Result**: Control plane now knows what accelerators exist on each node

#### Layer 2: Allocation - Making GPUs Schedulable
- **Device Plugin Framework**: Turns accelerators into schedulable resources
- **Extended Resources**: `nvidia.com/gpu` as first-class resource
- **Resource-based Scheduling**: Request GPUs like CPU/memory

#### Layer 3: Management - Staying Declarative
- **GPU Operator**: Unified management layer
  - Drivers installation
  - Container runtime configuration
  - Metrics and monitoring (DCGM)
  - Keeps everything aligned declaratively

#### Layer 4: Advanced Allocation - Higher Abstraction
- **Dynamic Resource Allocation (DRA)**: Intent-based GPU requests
- **Example**: Request "high-memory GPU" → scheduler finds exact match
- **Benefit**: Workload doesn't hardcode GPU model

### Act 3: Deployment Patterns ("Why Bother?")

**Driver**: Model size and latency targets dictate different layouts

#### Pattern 1: Single-Server Multi-GPU
- **When**: Need tight GPU cooperation, low-latency communication
- **How**: Multiple GPUs in one server (NVLink, NVSwitch)
- **Use Case**: Tensor parallelism for large models

#### Pattern 2: Multi-Node Multi-GPU
- **When**: "No box is big enough" - model exceeds single-server capacity
- **How**: Stretch across nodes, pipeline parallelism
- **Trade-off**: Network communication overhead

#### Pattern 3: GPU Sharing
- **When**: Lighter tasks, maximize silicon utilization
- **How**: Time-slicing and Multi-Instance GPU (MIG)
- **Benefit**: "Squeezing more value from every watt"
- **Safety**: Share without interference

### Resolution: Practical Guidance
By the end: Understand how discovery, allocation, and sharing fit together
Next step: Where to start when workload "demands more accelerator muscle"

## Technical Content (Detailed)

### 1. The Native vs Extended Resource Model
- CPU/memory: First-class K8s resources
- GPUs: Extended resources via device plugins
- Why this matters: Different discovery and allocation mechanisms

### 2. Node Feature Discovery (NFD)
- **Purpose**: Detect hardware features, apply node labels
- **Architecture**: DaemonSet on every node
- **Output**: Labels like `feature.node.kubernetes.io/pci-0302_10de.present: "true"`
- **Scope**: General-purpose (CPU, network, PCI devices)
- **Limitation**: Doesn't know GPU-specific details (memory, model, CUDA version)

### 3. GPU Feature Discovery (GFD)
- **Purpose**: GPU-specific labels for fine-grained scheduling
- **Part of**: NVIDIA GPU Operator
- **Key Labels**:
  - `nvidia.com/gpu.product`: Model (e.g., A100-SXM4-40GB)
  - `nvidia.com/gpu.memory`: Memory in MiB
  - `nvidia.com/gpu.family`: Architecture (ampere, hopper)
  - `nvidia.com/cuda.driver-version.full`: Driver version
  - `nvidia.com/mig.capable`: MIG support
  - `nvidia.com/gpu.replicas`: Time-slicing factor
- **Use Case**: Advanced scheduling (select specific GPU models)

### 4. Device Plugin Framework
- **Four Core Functions**:
  1. **Device Discovery**: Detect GPUs, report to Kubelet
  2. **Resource Allocation**: Exclusive allocation by default
  3. **Health Monitoring**: Report unhealthy devices
  4. **Scheduler Integration**: Expose as extended resource
- **Vendors**: NVIDIA, AMD, Intel, Google (TPU)
- **Limitation**: Static, exclusive allocation

### 5. GPU Scheduling Strategies

#### Resource-Based Scheduling (Simplest)
```yaml
resources:
  limits:
    nvidia.com/gpu: 1
```
- Scheduler finds node with free GPU
- No labels needed
- Treats all GPUs equally (blind to model/memory)

#### Label-Based Scheduling (Precision)
- **nodeSelector**: Direct label match
- **Node Affinity**: Rich expressions (required + preferred)
- **Taints/Tolerations**: Protect GPU nodes from non-GPU workloads

#### Combined Approach (Common)
- Resource request ensures GPU availability
- Labels/affinity select specific GPU type

### 6. NVIDIA GPU Operator
- **Architecture**: Operator managing multiple components
- **Components**:
  - Driver containers (kernel modules, CUDA)
  - GPU Feature Discovery (GFD)
  - Device Plugin
  - Container Toolkit (runtime hooks)
  - MIG Manager (partitioning)
  - DCGM Exporter (Prometheus metrics)
- **Configuration**: ClusterPolicy Custom Resource
- **Benefit**: Declarative, automated GPU enablement

### 7. Dynamic Resource Allocation (DRA)
- **Status**: Beta in K8s 1.33, disabled by default
- **Model**: ResourceClaimTemplate (like PVC for storage)
- **Example**:
```yaml
spec:
  devices:
    requests:
      - deviceClassName: gpu.nvidia.com/a100
        count: 1
        parameters:
          minMemory: "40Gi"
          migMode: "disabled"
```
- **Benefit**: Workload declares intent, scheduler resolves
- **Limitation**: Experimental, limited production use

### 8. Deployment Pattern Deep Dive

#### Single-Server Multi-GPU
- **Interconnect**: NVLink (~600 GB/s), NVSwitch
- **Communication**: Frequent, low-latency
- **Parallelism**: Tensor parallelism (split layers horizontally)
- **K8s**: Request multiple GPUs in one Pod
- **Use Case**: Large model requiring tight GPU cooperation

#### Multi-Node Multi-GPU
- **Interconnect**: Network (InfiniBand, Ethernet)
- **Bandwidth**: 100 Gbit Ethernet = 12.5 GB/s (vs 600 GB/s NVLink)
- **Parallelism**: Pipeline parallelism (split layers vertically)
- **Communication**: NCCL over RDMA
- **Orchestration**: Ray.io, StatefulSets, gang scheduling
- **Use Case**: Model exceeds single-server GPU memory

#### GPU Sharing

**Time-Slicing**:
- Oversubscribe GPUs (1 physical → N virtual)
- Multiple pods share compute time
- No memory isolation
- Use: Bursty workloads, dev environments

**Multi-Instance GPU (MIG)**:
- Hardware partitioning (A100, H100)
- Dedicated memory and compute per instance
- Strong isolation (memory, faults)
- Fixed profiles (1g.5gb, 2g.10gb, 4g.20gb, etc.)
- Use: Multi-tenant production, guaranteed resources

### 9. Decision Framework

**When to Use What**:
- **Single GPU**: Model fits, simple deployment
- **Single-Server Multi-GPU**: Model needs 2-8 GPUs, low-latency critical
- **Multi-Node Multi-GPU**: Model exceeds server capacity
- **Time-Slicing**: Oversubscribe for light/bursty workloads
- **MIG**: Strict isolation, predictable performance

**Trade-offs**:
- Single-server: High performance, limited scale
- Multi-node: Unlimited scale, communication overhead
- Sharing: High utilization, potential contention

### 10. Best Practices for Production

#### Discovery
- Deploy NFD for general hardware labeling
- Enable GFD for GPU-specific labels
- Use labels for precise workload placement

#### Allocation
- Start with resource-based scheduling (simplest)
- Add label-based selection for heterogeneous fleets
- Taint GPU nodes to prevent non-GPU workloads

#### Management
- Use GPU Operator for declarative management
- Monitor with DCGM Exporter + Prometheus
- Configure MIG or time-slicing based on workload patterns

#### Optimization
- Quantization to reduce memory footprint
- Warm pods to avoid reload costs
- Topology awareness for multi-GPU (`nvidia-smi topo -m`)
- Memory defragmentation strategies

### 11. Emerging Trends (2025)
- **NVIDIA KAI Scheduler**: Open-source GPU scheduler (Jan 2025)
- **Kueue/Volcano**: Batch admission, gang scheduling
- **Queue-Based Governance**: GPUs as shared, policy-driven resources
- **DRA Evolution**: Moving toward production readiness

## Key Technical Concepts

**NCCL (NVIDIA Collective Communication Library)**:
- GPU-to-GPU communication primitives
- all-reduce, broadcast, all-gather
- Used by PyTorch, vLLM internally

**RDMA (Remote Direct Memory Access)**:
- Direct memory access bypassing CPU
- Reduces latency in multi-node setups
- Requires InfiniBand or RoCE adapters

**Tensor Parallelism**:
- Split layers horizontally across GPUs
- All GPUs work on same layer
- High communication frequency
- Best within single server

**Pipeline Parallelism**:
- Split model vertically by layers
- Sequential processing with micro-batching
- Lower communication frequency
- Tolerates slower interconnects

## Visual Opportunities
1. **The Gap**: K8s native resources vs extended resources
2. **Discovery Flow**: NFD → GFD → Node Labels → Scheduler
3. **Device Plugin Architecture**: Registration, discovery, allocation
4. **GPU Operator Components**: Layered architecture diagram
5. **Scheduling Comparison**: Resource-based vs label-based
6. **Time-Slicing vs MIG**: Side-by-side comparison
7. **Tensor vs Pipeline Parallelism**: Visual comparison
8. **Single-Server vs Multi-Node**: Architecture diagrams
9. **NCCL Communication**: Multi-GPU coordination patterns
10. **Decision Tree**: Which pattern for which use case

## Sources

### Primary Source
- O'Reilly Book: "Generative AI on Kubernetes" Chapter 5
- Comprehensive coverage of all topics

### Online Research (2025)
- [Schedule GPUs | Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/)
- [Device Plugins | Kubernetes](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/device-plugins/)
- [NVIDIA k8s-device-plugin](https://github.com/NVIDIA/k8s-device-plugin)
- [Kubernetes GPU Resource Management Best Practices](https://www.perfectscale.io/blog/kubernetes-gpu)
- [Kubernetes and GPU: Complete Guide 2025 - Collabnix](https://collabnix.com/kubernetes-and-gpu-the-complete-guide-to-ai-ml-acceleration-in-2025/)
- [GPU Resource Management Complete Technical Guide 2025 - Collabnix](https://collabnix.com/kubernetes-gpu-resource-management-best-practices-complete-technical-guide-for-2025/)

## Tone and Style Notes (from CFP)
- **Practical, hands-on**: "live or die by GPUs"
- **Systems-oriented**: Focus on how pieces fit together
- **Conversational but technical**: "squeezing more value from every watt"
- **Action-oriented**: "where to start when your next tuning run demands..."
- **Avoid**: Pure theory, academic tone, high-level hand-waving
- **Emphasize**: Concrete patterns, decision frameworks, production considerations
