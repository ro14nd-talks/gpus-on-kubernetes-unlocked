# Presenter Notes - GPUs on Kubernetes Unlocked

**Presentation Duration:** 40 minutes
**Total Slides:** 47 (39 main slides + 8 backup slides)
**Timing:** 90 seconds per slide average
**Presenter:** Roland Huß, Distinguished Engineer, Red Hat

---

## Slide 02: Book Introduction
**Timing:** 30 seconds

This presentation is based on Chapter 5 of our O'Reilly book, Generative AI on Kubernetes.

Context:
- Book provides comprehensive coverage of GenAI on K8s
- This talk focuses specifically on GPU infrastructure
- Chapter 5 covers discovery, allocation, orchestration, and deployment patterns
- Book has 9 chapters covering entire GenAI stack

Transition: "Let's start with the fundamental problem we're solving."

---

## Slide 03: Kubernetes Lacks Native GPU Support
**Timing:** 90 seconds

Kubernetes was designed around CPU and memory - these are first-class resources with native support.

But large language models live and die by GPUs. Until the control plane knows what accelerators exist on each node, the scheduler can't place pods intelligently.

This is the GPU gap we need to bridge.

---

## Slide 04: Three Layers Enable GPU Scheduling
**Timing:** 90 seconds

The solution has three distinct layers working together:

1. Discovery Layer: NFD and GFD label nodes with GPU capabilities
2. Allocation Layer: Device plugins expose GPUs as schedulable resources
3. Orchestration Layer: GPU Operator manages the complete lifecycle

Each layer builds on the one below it. Let's start at the foundation.

---

## Slide 05: Discovery Layer - Finding GPUs in Your Cluster
**Timing:** 20 seconds

We just saw the three-layer architecture. Let's start at the foundation: the Discovery Layer.

Before Kubernetes can schedule GPU workloads, it needs to know which nodes have GPUs and what their capabilities are.

This is where NFD and GFD come in—they label nodes with hardware capabilities.

---

## Slide 06: Hardware Labels Enable Intelligent GPU Scheduling
**Timing:** 90 seconds

Imagine a cluster with mixed GPU types - some nodes have T4s, others A100s, maybe some H100s.

Without labels, scheduler picks randomly. Pod fails because GPU doesn't meet requirements.

Two tools solve this: NFD for general hardware, GFD for GPU-specific details.

---

## Slide 07: NFD Provides Foundation for Hardware Discovery
**Timing:** 120 seconds

Node Feature Discovery is the foundation - detects all hardware types.

It labels nodes with CPU details, PCI devices, network interfaces.

For GPUs: detects PCI device class (0302) and vendor ID (10de for NVIDIA).

But NFD only knows "GPU exists" - not model, memory, or capabilities.

---

## Slide 08: GFD Delivers Model-Specific GPU Information
**Timing:** 90 seconds

GPU Feature Discovery adds the detailed GPU information NFD lacks.

Uses nvidia-smi to gather comprehensive data: exact model, memory size, architecture, CUDA capabilities, MIG support.

These labels enable fine-grained scheduling - target "A100 with 40GB+" or "Hopper architecture only".

GFD is part of the GPU Operator we'll cover later.

---

## Slide 09: NFD Detects Presence, GFD Provides Specifications
**Timing:** 90 seconds

Quick comparison:

NFD: "this node has an NVIDIA GPU"
GFD: "this node has 4× A100 40GB GPUs with CUDA 12.2"

Both label the same nodes, different detail levels.

The scheduler uses these labels for intelligent placement decisions.

Now that we can discover and label GPUs, how do we allocate them?

---

## Slide 10: Allocation Layer - Making GPUs Schedulable
**Timing:** 20 seconds

Discovery gives us labeled nodes. Now we move to the second layer: Allocation.

Labels alone don't allocate resources. We need to expose GPUs as countable, schedulable resources that Kubernetes understands.

That's where device plugins and scheduling strategies come in.

---

## Slide 11: Device Plugins Turn GPUs Into Schedulable Resources
**Timing:** 120 seconds

Device Plugin framework extends Kubernetes beyond CPU and memory.

gRPC interface between kubelet and vendor plugin.

The flow:
1. Device plugin registers with kubelet via gRPC
2. Kubelet asks for device list
3. Plugin reports GPU inventory (IDs, health)
4. Kubelet advertises to API server as extended resource
5. Scheduler reads nvidia.com/gpu: 4 and can schedule pods

Clean abstraction - Kubernetes doesn't need GPU-specific code.

NVIDIA, AMD, Intel each provide their own device plugin.

This is the magic moment—GPUs go from invisible hardware to first-class schedulable resources.

---

## Slide 12: Resource-Based Scheduling: Simple and Direct
**Timing:** 90 seconds

Simplest scheduling approach: request GPUs like CPU or memory.

Device plugin advertises count, pod requests count, scheduler matches.

Works great for homogeneous clusters with one GPU type.

Problem: scheduler sees all GPUs as identical. Model needs 40GB but gets scheduled on 16GB T4 → fails at runtime.

For heterogeneous clusters, we need label-based precision.

---

## Slide 13: Resource Requests Trade Simplicity for GPU Precision
**Timing:** 90 seconds

Resource-based scheduling shines in simplicity.

If all your nodes have identical GPUs (all T4s or all A100s), this is perfect.

But in production heterogeneous clusters with mix of GPU types, the limitations become painful.

Scheduler can't distinguish between a $5K T4 and a $30K H100.

For precise placement, combine with labels.

---

## Slide 14: Request Specific GPU Models with nodeSelector
**Timing:** 90 seconds

NodeSelector: simplest label-based approach - direct key-value match.

Pod specifies exact requirement, scheduler ONLY considers nodes with that label.

Example: Workload tested on T4s in dev, wants same in production for consistency.

Limitation: Can't express "T4 OR A10" or "memory > 24GB". All-or-nothing.

For flexible requirements, use node affinity.

---

## Slide 15: Match GPUs by Memory Size with Node Affinity
**Timing:** 120 seconds

Node affinity unlocks rich expressions: 'greater than', 'in list', 'exists' operators.

This spec: "GPU memory MUST be > 40000 MiB (40GB)"

Matches: A100 40GB, A100 80GB, H100 80GB
Rejects: T4 16GB, A10 24GB

Can also use preferredDuringScheduling for soft preferences: "prefer Hopper but Ampere is okay"

Use case: Workload needs minimum memory but flexible on GPU model.

---

## Slide 16: Taints Protect GPU Nodes From CPU Workloads
**Timing:** 90 seconds

Taints protect expensive GPU nodes from running cheap workloads.

Left example: Node taint with NoSchedule effect rejects pods without matching toleration.
Right example: Pod toleration opts-in to GPU nodes.

STRENGTHS:
1. Protects expensive GPU resources - Prevents $30K/month GPU nodes from running cheap workloads like nginx
2. Explicit opt-in model - Only pods with tolerations can access GPU nodes - intentional, not accidental
3. Complements resource requests - Works alongside nvidia.com/gpu: 1 for layered protection
4. Prevents accidental placement - Random pods won't land on GPU nodes during scale-up events

LIMITATIONS:
1. Requires manual toleration setup - Every GPU pod must add tolerations - easy to forget
2. Extra YAML complexity - Additional stanza in every GPU pod spec
3. Easy to misconfigure - Typo in taint key/value/effect breaks scheduling silently
4. No warning on missing toleration - Pod just won't schedule - no proactive feedback

Best practice: Taint all GPU nodes in heterogeneous clusters to prevent resource waste.

---

## Slide 17: When to Use Each Scheduling Approach
**Timing:** 90 seconds

Decision tree for scheduling approach:

Homogeneous cluster (one GPU type): Resource request sufficient

Multiple GPU types:
- Know exact model needed: nodeSelector
- Flexible requirements (min memory): Affinity with Gt
- Need to protect expensive nodes: Add taints

Pattern: Start simple, add complexity only when needed.

Most production: Resource + affinity + taints combined

---

## Slide 18: Combined Approach Is Production Best Practice
**Timing:** 90 seconds

Production workloads combine both mechanisms:

Resource request (nvidia.com/gpu: 1) ensures scheduler finds node with FREE GPU

Label selector ensures that GPU is correct type (Ampere architecture)

Double protection: right GPU + available GPU

This pattern is what you'll see in most production deployments.

---

## Slide 19: DRA Requests GPUs By Intent Not Model
**Timing:** 90 seconds

Dynamic Resource Allocation (DRA) shifts from static resource requests to intent-based allocation.

Key concept: Request by capability/intent, not hardcoded model names.

Example workload requirement: "GPU with minimum 40GB memory, MIG disabled"
Scheduler resolves this to actual GPU using DeviceClass and parameters.

DeviceClass resources are defined by GPU vendors (NVIDIA, AMD, Intel) as CRDs.
Each vendor provides a DRA driver that understands their hardware capabilities.

Similar to PVC for storage - you request by capacity/class/access mode, not specific disk model.

The beta status, adoption concerns, and future direction are covered in slide 21.

---

## Slide 20: Pods Claim Resources, Scheduler Allocates Devices
**Timing:** 90 seconds

DRA uses template-based pattern for resource allocation.

Left: ResourceClaimTemplate defines requirements (device class, memory, etc.)
Right: Pod references template via resourceClaims, uses it in containers

Flow:
1. Admin creates ResourceClaimTemplate (reusable)
2. Pod references template by name
3. Scheduler creates ResourceClaim from template
4. Allocates actual GPU matching requirements
5. Pod container claims the allocated resource

Similar to PVC/PV pattern: define requirements, bind to actual resource.

---

## Slide 21: DRA Is Promising But Not Production-Ready
**Timing:** 90 seconds

DRA benefits are compelling: precise specifications like "40GB+ A100" without manual label conventions, intelligent dynamic allocation resolved at schedule time.

Example use cases for DRA:
- MIG partitioning
- Topology-aware allocation
- Device sharing policies
- Composable multi-device requests

Current status and limitations:
- Beta in Kubernetes 1.33 (disabled by default, requires feature gate)
- API still evolving, may change
- NVIDIA DRA driver experimental
- Few production users, limited ecosystem adoption
- Cluster autoscalers don't fully understand DRA claims yet

Future direction:
- DRA is Kubernetes' intended replacement for device plugins
- Will become the standard mechanism for extended resources
- Vendors (NVIDIA, AMD, Intel) actively developing DRA drivers
- Moving toward production readiness

Recommendation: Use traditional scheduling (resource requests + labels) for production today, but start learning DRA now.

When it stabilizes, DRA will replace much manual labeling and selection logic with intent-based requests.

---

## Slide 22: Orchestration Layer - NVIDIA GPU Operator
**Timing:** 20 seconds

We've covered Discovery and Allocation. Now the top layer: Orchestration.

Discovery labels nodes. Device plugins expose resources. But production GPU support needs more: driver installation, runtime configuration, monitoring, lifecycle management.

The NVIDIA GPU Operator manages all of this declaratively.

---

## Slide 23: Scheduling GPUs Is Only Half the Battle
**Timing:** 90 seconds

We've built discovery and allocation layers. GPUs are schedulable. But production needs more.

What we have:
- NFD/GFD label nodes with GPU details
- Device plugins expose nvidia.com/gpu resource
- Scheduler knows which nodes have GPUs and can place pods

What's missing:
- Driver installation: NVIDIA kernel modules, CUDA libraries must match kernel version
- Container runtime: Need NVIDIA Container Toolkit to inject GPU devices into containers
- Monitoring: Track GPU health, temperature, ECC errors
- Lifecycle management: Keep driver versions, configs synchronized across all GPU nodes

Traditional approach: SSH into each GPU node, install drivers manually, configure runtime, set up monitoring.

Problems:
- Configuration drift across nodes
- Driver/kernel version mismatches
- Manual updates don't scale to 100+ node clusters
- Not declarative, not GitOps-friendly

This is where the GPU Operator comes in—it automates ALL of this declaratively.

---

## Slide 24: GPU Operator Automates Six Critical Components
**Timing:** 90 seconds

NVIDIA GPU Operator is the orchestration layer—one operator managing everything.

We've seen Device Plugin (✓) and GFD (✓) in earlier sections. Now the full picture:

1. Driver Container: Automated driver deployment via privileged container
   - Compiles drivers for node's kernel or uses precompiled version
   - No SSH to nodes, no manual installation

2. Container Toolkit: Runtime hooks that inject GPU devices
   - Extends containerd/CRI-O to inject /dev/nvidia* devices
   - Automatic GPU driver access for containers

3. Device Plugin (✓): Already covered in allocation layer
   - Exposes nvidia.com/gpu as schedulable resource

4. GPU Feature Discovery (✓): Already covered in discovery layer
   - Enriches nodes with detailed NVIDIA-specific labels

5. MIG Manager: Handles Multi-Instance GPU partitioning
   - Automates MIG partition setup (we'll cover sharing strategies soon)
   - No manual nvidia-smi configuration needed

6. DCGM Exporter: GPU health and performance metrics
   - Utilization, memory, temperature, ECC errors → Prometheus
   - Integrates with cluster monitoring stack

All configured through single ClusterPolicy custom resource.
Fully declarative: no SSH, no manual updates, everything stays in sync.

Key message: "Instead of SSHing into nodes to install drivers and configure runtimes, you declare desired state in ClusterPolicy and the operator reconciles everything."

---

## Slide 25: GPU Sharing - Sub-GPU Allocation
**Timing:** 15 seconds

So far: exclusive GPU access - one pod gets one whole GPU.

But what if workload only needs 20% of GPU? Multiple small models on one card?

GPUs are expensive. Sharing them efficiently is critical.

NVIDIA provides two mechanisms: time-slicing and MIG.

---

## Slide 26: NVIDIA GPU Sharing Maximizes Utilization
**Timing:** 90 seconds

Why this slide matters: Before diving into technical details of time-slicing and MIG, establish WHY GPU sharing matters and WHAT options exist.

The problem: GPUs are the most expensive infrastructure component, but exclusive allocation leads to massive underutilization. A 7B model using 10GB sits on a 40GB A100 - wasting 75% of the memory.

Two approaches from NVIDIA:
1. Time-slicing: Software-based oversubscription, works on any GPU, no isolation
2. MIG (Multi-Instance GPU): Hardware partitioning, A100/H100 only, strong isolation

This overview prepares audience for detailed exploration of each technique.

---

## Slide 27: Time-Slicing Oversubscribes GPUs
**Timing:** 90 seconds

Time-slicing is oversubscription.

1 physical GPU advertised as N schedulable resources. Kubernetes thinks N GPUs exist.

Pods time-share actual GPU - similar to CPU scheduler interleaving processes.

ConfigMap defines sharing policy, referenced in ClusterPolicy devicePlugin config.

Key: Compute time-sharing, NOT memory isolation. All pods share same VRAM.

Why dev/test and bursty workloads:
- Dev workloads are intermittent (short experiments, tests, debugging) with lots of idle time
- Bursty workloads can use full GPU when available (unlike MIG's hard partitions)
- Cost efficiency over isolation (share 1 GPU among 4-8 developers vs buying each a dedicated GPU)
- No strict SLAs needed - acceptable performance variability
- NOT for production: no memory isolation = pods can interfere, unpredictable performance

Production alternative: MIG provides hardware isolation and guaranteed resources.

---

## Slide 28: Time-Slicing Trades Isolation for Density
**Timing:** 90 seconds

Time-slicing works great for certain patterns:

Good: Many small workloads that are bursty - inference requests come in waves, notebooks run intermittently. They don't all peak at once. Also good for older GPUs (T4, V100) without MIG support.

Watch out for:
- No memory isolation: One pod can OOM others (one LLM loading 15GB leaves no room for others)
- No fault isolation: Bad memory access crashes GPU → all pods fail (safety-critical workloads problematic)
- Unpredictable performance: Contention if all pods active simultaneously
- Multi-GPU requests ineffective: Requesting 2 virtual GPUs ≠ 2× performance (could get slices from 2 different physical GPUs with no speedup)

Best for: many small, bursty tasks on one GPU.

For isolation, use MIG.

---

## Slide 29: NVIDIA MIG Provides Hardware-Isolated Partitions
**Timing:** 120 seconds

Multi-Instance GPU (MIG) is true hardware partitioning - not time-sharing.

A100 can be split into up to 7 MIG instances. Each gets:
- Fixed memory (5GB, 10GB, 20GB, 40GB profiles)
- Dedicated compute cores
- Separate fault domain

One MIG instance crashes, others keep running.

Strategies:
- Single: All GPUs same MIG config (simple but inflexible)
- Mixed: Different partitions per GPU (flexible, complex)

MIG Manager in GPU Operator handles partition creation automatically.

---

## Slide 30: MIG Provides Isolation at Cost of Flexibility
**Timing:** 120 seconds

MIG provides true hardware isolation - each partition gets dedicated memory and compute.

Good For - Real isolation value:
- Multi-tenant SaaS: Each customer gets guaranteed GPU slice, complete fault isolation
- Multiple inference endpoints: 7 different models (5GB each) on one A100, no memory stealing
- Production QoS: Guaranteed resources, predictable performance, one crash doesn't kill others
- Fixed workloads: Know exactly what each partition needs, benefit from dedicated resources

Limitations - Where it hurts:
- Fixed profiles: Can't get 15GB partition - stuck with 10GB or 20GB (fixed: 1g.5gb, 2g.10gb, 3g.20gb, 7g.40gb)
- No bursting: Dev workload might need 8GB normally but could use 15GB - MIG hard-limits to partition
- Large models: 70B model in 4-bit needs 35GB, doesn't fit 20GB partition - must use whole GPU anyway
- Ampere+ only: A100, H100, B100/B200 - older GPUs (T4, V100) can't use MIG

Real-world pattern: MIG for strict multi-tenancy/production, time-slicing for dev/bursty workloads.

Advanced: Can combine MIG + time-slicing (time-slice each MIG instance) for maximum density.

---

## Slide 31: Choose Time-Slicing for Flexibility, MIG for Isolation
**Timing:** 90 seconds

Streamlined comparison - 6 key decision factors:

1. Isolation & Faults: MIG provides true hardware isolation, time-slicing shares everything
2. Resource Guarantees: MIG guarantees memory+compute, time-slicing only guarantees time slices
3. Flexibility: Time-slicing can burst, MIG is locked to partition size
4. Complexity: Time-slicing is simple (just replica count), MIG requires understanding fixed profiles
5. Hardware Support: Time-slicing works on all GPUs, MIG needs Ampere+
6. Use Case: Dev/bursty → time-slicing, Production/multi-tenant → MIG

Decision framework:
- Need isolation? → MIG
- Need flexibility? → Time-slicing
- Have Ampere+ GPUs? → MIG available
- Have older GPUs (T4, V100)? → Time-slicing only option
- Dev environment? → Time-slicing
- Production multi-tenant? → MIG

---

## Slide 32: Can Combine Time-Slicing and MIG for Maximum Density
**Timing:** 90 seconds

Advanced technique: Combine both for best of both worlds.

Create MIG instances for memory isolation. Then time-slice each instance for density.

Example: 2× 20GB MIG instances, time-slice each 2×. Result: 4 virtual GPUs with some memory isolation.

Labels show -SHARED suffix to indicate this mode.

For most LLM inference, won't use these much - models too large. But for many smaller models or batch jobs, invaluable.

Speaking of large models: what if one GPU isn't enough?

---

## Slide 33: Multi-GPU Patterns - Scaling Out
**Timing:** 15 seconds

We've mastered splitting one GPU among many workloads.

Now flip the problem: one workload needs many GPUs.

Modern LLMs can exceed 100GB. Even A100 80GB isn't enough.

This is where multi-GPU patterns come in.

---

## Slide 34: Two Dimensions Define Three GPU Strategies
**Timing:** 90 seconds

This slide introduces the taxonomy of multi-GPU parallelism strategies. There are two key dimensions:

1. Strategy: Do we replicate the entire model (Data Parallelism) or split it (Model Parallelism)?
   - Data Parallelism: Multiple complete copies for throughput scaling
   - Model Parallelism: Split a single model when it's too large for one GPU

2. Deployment: Single-node vs multi-node determines the communication patterns
   - Tensor Parallelism: Single-node, layer-level horizontal splits, NVLink
   - Pipeline Parallelism: Multi-node capable, layer-level vertical splits, Ethernet

This is an intro slide. Next slides cover:
- Slide 35: Data Parallelism throughput scaling characteristics
- Slides 36-37: Model Parallelism strategies (Tensor and Pipeline)
- Slide 38: Comparison and best practices

---

## Slide 35: Data Parallelism Trades Memory for Throughput
**Timing:** 90 seconds

Straightforward horizontal scaling.

Kubernetes-native: Deployment + Service. Autoscale with HPA/KEDA.

Limitations:
- Each replica: full model in memory. 8 replicas of 40GB model = 320GB total
- Doesn't reduce latency
- Doesn't help if model doesn't fit 1 GPU

For models exceeding GPU memory, use model parallelism.

---

## Slide 36: Tensor Parallelism Splits Layers Horizontally
**Timing:** 120 seconds

Tensor parallelism: Multiple GPUs in one server work together on each request.

How it works:
- All GPUs in a single server cooperate on the SAME layer simultaneously
- Each GPU handles a different portion of the layer (horizontal split)
- After processing each layer, GPUs exchange results via NVLink
- This repeats for every layer in the model
- Think: "Many hands make light work" - all GPUs chip in on each step

Architecture:
- Single server with NVLink/NVSwitch interconnect
- 600 GB/s bandwidth between GPUs (50× faster than network)
- All-to-all connectivity: any GPU can talk to any other GPU
- Intra-GPU memory bandwidth: 900 GB/s (even faster within a single GPU)

Why it's fast:
- High communication frequency (after every layer) requires super-fast interconnect
- NVLink provides the speed needed for constant GPU-to-GPU data exchange
- Parallel processing: all GPUs work simultaneously, reducing latency

Kubernetes deployment:
- Single pod request: nvidia.com/gpu: 4 (or 8)
- Scheduler finds node with enough free GPUs
- CUDA_VISIBLE_DEVICES automatically configured by runtime
- Framework (vLLM, TGI, PyTorch) initializes tensor parallelism
- No special networking needed—everything stays local within the server

Real-world example:
- Llama2-70B model: 70 billion parameters, ~140GB memory at fp16 precision
- Single A100 GPU: 40GB memory → model won't fit!
- Solution: Split across 4 A100 GPUs (4 × 40GB = 160GB)
- Each GPU holds 1/4 of each layer
- Low latency critical for real-time inference

Design principle:
- Prefer single-node when possible (fastest, simplest)
- Ideal for models fitting 1-8 GPUs (most models up to ~70B params)

Limitations:
- Typically 2-8 GPUs per server (NVLink topology limit on most hardware)
- Can't exceed GPU count of largest node in your cluster
- If you need 16 GPUs and nodes have 8 max, must use pipeline parallelism instead

---

## Slide 37: Pipeline Parallelism Splits Layers Vertically
**Timing:** 120 seconds

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
- Still 24-50× slower than NVLink (600 GB/s within server vs 12-25 GB/s between servers)
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

---

## Slide 38: Tensor within nodes, pipeline across nodes
**Timing:** 120 seconds

Quick comparison of the two model parallelism strategies:

Tensor Parallelism (Single-Node):
- Horizontal split: all GPUs work on same layer, different parts
- Needs fast links (NVLink ~600 GB/s)
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
- Intra-Node (NVLink): 600 GB/s → Tensor parallelism sweet spot
- Inter-Node (Ethernet): 12.5 GB/s → 48× slower, pipeline tolerates this
- Inter-Node (InfiniBand): 25 GB/s → 2× better than Ethernet, still 24× slower than NVLink

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

---

## Slide 39: GPUs on Kubernetes: Unlocked
**Timing:** 120 seconds

This is the main conclusion slide - synthesizes the complete journey from gap to solution.

KEY MESSAGE:
You now have the complete pattern library to bridge the GPU scheduling gap and build production-ready GPU infrastructure.

TALKING POINTS:

1. FOUNDATION - Three Layers Bridge the Gap (30s)
   - Discovery: Makes GPUs visible through labels (NFD + GFD)
   - Allocation: Makes them schedulable through device plugins
   - Orchestration: Automates everything with GPU Operator (ClusterPolicy)
   - Result: Production-ready GPU scheduling on Kubernetes

2. OPTIMIZATION - Sharing & Scaling Maximize Value (40s)
   - GPU Sharing: Time-slicing and MIG maximize ROI on expensive hardware
   - Multi-GPU Patterns: Data, tensor, pipeline parallelism enable scale from single GPU to clusters
   - Decision Framework: Match pattern to workload (model size, latency requirements)
   - Result: Full infrastructure potential unlocked

3. CULTURAL SHIFT - GPUs as Shared Infrastructure (30s)
   - Treat GPUs as shared, policy-driven substrate (not pets)
   - Use declarative automation (ClusterPolicy, not SSH)
   - Start with proven patterns (device plugins, GPU Operator)
   - Adopt new tools as they mature (DRA, KAI scheduler, Kueue)

TRANSITION TO NEXT SLIDE:
"Let me give you five concrete strategies to maximize GPU utilization in your clusters..."

VISUAL CUES:
- Complete stack diagram shows all layers working together
- Single summary equation emphasizes how pieces fit
- Book reference for deeper dive into full GenAI stack

This was Chapter 5 - one piece of the GenAI on Kubernetes puzzle. The book covers infrastructure, model serving, observability, security, cost management.

---

## Backup Slides

The following backup slides (40-47) are available for Q&A or if time permits:

- **Slide 40:** Five Strategies Maximize GPU Utilization
- **Slide 41:** 2025 Trends Shift GPUs to Shared Substrate
- **Slide 42:** Three Layers Unlock GPU Scheduling (backup reference)
- **Slide 43:** Questions?
- **Slide 44:** Backup Section marker
- **Slide 45:** NCCL and RDMA Enable Fast GPU Communication
- **Slide 46:** Debugging GPU Scheduling Issues
- **Slide 47:** Multi-Instance GPU Configuration Strategies

These provide technical depth for anticipated questions without cluttering the main presentation flow.

---

## Key Transitions and Timing Notes

**Total Main Presentation Time:** ~35-38 minutes (slides 02-39)
- Introduction & Discovery: ~8 minutes (slides 02-09)
- Allocation Layer: ~11 minutes (slides 10-21)
- Orchestration Layer: ~4 minutes (slides 22-24)
- GPU Sharing: ~7 minutes (slides 25-32)
- Multi-GPU Patterns: ~6 minutes (slides 33-38)
- Conclusion: ~2 minutes (slide 39)

**Buffer Time:** 2-5 minutes for Q&A or backup slides

**Critical Transitions:**
- Slide 09 → 10: "Now that we can discover and label GPUs, how do we allocate them?"
- Slide 21 → 22: "We've covered Discovery and Allocation. Now the top layer: Orchestration."
- Slide 24 → 25: "GPUs are expensive. Sharing them efficiently is critical."
- Slide 32 → 33: "Speaking of large models: what if one GPU isn't enough?"

---

**End of Presenter Notes**
