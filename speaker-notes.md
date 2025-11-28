# Speaker Notes: GPUs on Kubernetes Unlocked

**Total Duration**: ~32 minutes (32 slides × 1 minute each)
**Target Audience**: Kubernetes practitioners at conference
**Delivery Style**: Technical but conversational, question-based patterns section

---

## Slide 1: Title Slide
**Timing**: 1 minute

**Notes**:
Welcome! GPUs on Kubernetes - from invisible hardware to production workloads.

**Opening**:
- "Good [morning/afternoon]! Thank you for joining this session."
- "Today we're going to unlock GPUs on Kubernetes."
- "If you're running LLM workloads or planning to, this talk will show you exactly how to make GPUs work in K8s."
- "We'll cover discovery, allocation, and sharing—the complete stack."

**Set Expectations**:
- "By the end, you'll know how to deploy the GPU Operator, schedule workloads intelligently, and choose the right deployment pattern for your use case."

---

## Slide 2: The Problem
**Timing**: 1 minute

**Notes**:
Kubernetes was built for CPU and memory. But LLMs run on GPUs. Without GPU support, your workloads can't run.

**Key Points**:
- Kubernetes native resources: CPU cores, memory (RAM)
- LLM workloads fundamentally depend on GPUs
- Example: "A 70B parameter model needs 140GB of GPU memory—that won't run on CPU"
- Without GPU support, your pods simply won't schedule

**Transition**:
"So what's the gap we need to fill?"

---

## Slide 3: The Gap
**Timing**: 1 minute

**Notes**:
Until the control plane knows GPUs exist, it's all manual work. Let's see how to make GPUs first-class resources.

**Build Up Each Point**:
1. **Control plane blind**: "Kubernetes API server has no idea GPUs exist"
2. **Scheduler can't place**: "Scheduler might put your GPU workload on a CPU-only node"
3. **Manual operations**: "Operators SSH into each node, install drivers by hand"
4. **Not declarative**: "Everything we love about K8s—declarative config, GitOps—none of that works for GPUs by default"

**Emphasize**: "We need to fix this"

**Transition**:
"And here's how we do it—with three layers."

---

## Slide 4: Three Layers
**Timing**: 1 minute

**Notes**:
Discovery → Allocation → Orchestration. Each layer builds on the previous one.

**Explain the Diagram**:
- **Orange (Discovery Layer)**: "First, we need to give Kubernetes a vocabulary—labels that describe GPU capabilities"
- **Blue (Allocation Layer)**: "Second, we make GPUs schedulable resources that the scheduler understands"
- **Green (Orchestration Layer)**: "Third, we manage everything declaratively with the GPU Operator"

**Key Message**: "Each layer builds on the one below it. You can't allocate what you haven't discovered. You can't manage what you haven't allocated."

**Transition**:
"Let's start at the foundation: Discovery."

---

## Slide 5: Discovery Layer (Section Header)
**Timing**: Brief transition (included in next slide timing)

**Notes**:
Give Kubernetes a vocabulary

**Transition**:
"How does Kubernetes learn what GPUs exist in your cluster?"

---

## Slide 6: Node Feature Discovery
**Timing**: 1 minute

**Notes**:
NFD detects "a GPU exists" but not which model or memory.

**Explain NFD**:
- "NFD is a general-purpose hardware detector"
- "Deploy it once as a DaemonSet—one agent per node"
- "Each agent scans hardware: CPU features, PCI devices, network interfaces"

**Show the Command**:
- "Installation is one kubectl apply command"
- "Point it at the GitHub repo, uses Kustomize"

**Show the Label**:
- "`pci-0302_10de.present: true`"
- "Class 0302 means 3D controller—that's a GPU"
- "Vendor 10de is NVIDIA"
- "So we know: 'this node has an NVIDIA GPU'"

**Limitation**:
- "But NFD doesn't know *which* GPU, how much memory, what CUDA version"
- "For that, we need GPU Feature Discovery"

---

## Slide 7: GPU Feature Discovery
**Timing**: 1 minute

**Notes**:
GFD adds GPU-specific details. Now we can schedule intelligently.

**Explain GFD**:
- "GFD is the GPU specialist"
- "Part of the NVIDIA GPU Operator (we'll see that later)"
- "Runs on GPU nodes, queries nvidia-smi for details"

**Walk Through Labels**:
- **gpu.product**: "Exact model—A100-SXM4-40GB"
- **gpu.memory**: "Memory in MiB—40,537 is about 40GB"
- **gpu.family**: "Architecture—ampere, hopper, turing"
- **mig.capable**: "Can this GPU be partitioned with MIG?"

**Right Side (click)**:
"Now the scheduler knows:
- Which GPU model is on each node
- How much memory it has
- What architecture family
- What capabilities it supports"

**Transition**:
"Discovery complete. Now let's make these GPUs schedulable."

---

## Slide 8: Allocation Layer (Section Header)
**Timing**: Brief transition

**Notes**:
Make GPUs schedulable

**Transition**:
"Labels are great, but they don't make GPUs schedulable. For that, we need device plugins."

---

## Slide 9: Device Plugins
**Timing**: 1 minute

**Notes**:
Device plugins turn GPUs into Kubernetes resources.

**Explain Each Function** (build with clicks):
1. **Register with Kubelet**: "Plugin talks to kubelet via gRPC"
2. **Report GPU inventory**: "Kubelet asks 'what GPUs do you have?' Plugin responds with IDs and health"
3. **Expose as nvidia.com/gpu**: "Creates an extended resource, just like CPU or memory"
4. **Scheduler sees it**: "Now the API server advertises 'this node has 4 GPUs available'"

**Bottom Message** (click):
"GPUs become schedulable resources"

**Key Point**: "This is the magic moment—GPUs go from invisible to first-class resources in Kubernetes"

---

## Slide 10: Request a GPU
**Timing**: 1 minute

**Notes**:
Simplest approach: request like CPU. But scheduler is GPU-blind.

**Show the YAML**:
- "Just like requesting CPU or memory"
- "`nvidia.com/gpu: 1` in your limits"
- "Scheduler finds a node with a free GPU, kubelet allocates it"

**Pros and Cons** (click):
**Simple but limited**:
- "Works great if all your GPUs are identical"
- "But in real clusters, you have mixed GPUs—A100s, T4s, V100s"
- "A model needing 40GB won't fit on a 16GB T4"
- "This approach treats all GPUs the same—scheduler picks randomly"

**Transition**:
"So how do we add precision?"

---

## Slide 11: Add Precision with Labels
**Timing**: 1 minute

**Notes**:
Combine resource requests with labels for intelligent placement.

**First Example**:
- "Combine resource request with nodeSelector"
- "Still request `nvidia.com/gpu: 1`"
- "But also: `gpu.family: ampere`"
- "Now you get an Ampere GPU specifically—A100 or A30"

**Second Example** (click):
- "Node affinity gives you even more power"
- "Rich expressions: greater-than, in, not-in"
- "Here: GPU memory must be > 40,000 MiB"
- "Scheduler only considers nodes with 40GB+ GPUs"

**Transition**:
"But what about protecting GPU nodes from non-GPU workloads?"

---

## Slide 12: Protect GPU Nodes
**Timing**: 1 minute

**Notes**:
Taints keep GPU nodes for GPU workloads only.

**Left Side**:
**Taint GPU nodes**:
- "Use kubectl taint with a label selector"
- "All nodes with `gpu.count` label get tainted"
- "`NoSchedule` effect means: default deny"

**Right Side** (click):
**Pods must tolerate**:
- "GPU workloads add a toleration"
- "`operator: Exists` means tolerate any value"
- "Now this pod *can* schedule on tainted GPU nodes"

**Bottom Message** (click):
- "Why do this? Expensive GPU nodes shouldn't run nginx or random batch jobs"
- "Taints protect them—only GPU workloads allowed"

---

## Slide 13: Dynamic Resource Allocation
**Timing**: 1 minute

**Notes**:
DRA is the future: intent-based GPU requests. Still experimental.

**Explain DRA**:
- "Beta in Kubernetes 1.33, disabled by default"
- "Think of it like PersistentVolumeClaims for GPUs"
- "You declare intent: 'I need an A100 with at least 40Gi memory'"
- "Scheduler resolves to specific device at scheduling time"

**Show the YAML**:
- "ResourceClaimTemplate describes what you want"
- "`deviceClassName`: gpu.nvidia.com/a100"
- "`minMemory: 40Gi`"
- "Scheduler finds a matching GPU"

**Status** (click):
- "Still experimental—NVIDIA has a DRA driver but it's marked experimental"
- "Not production-ready yet"
- "But watch this space—it's where Kubernetes is headed"

**Transition**:
"Scheduling works. But what about operations?"

---

## Slide 14: Orchestration Layer (Section Header)
**Timing**: Brief transition

**Notes**:
Stay declarative

**Transition**:
"We can schedule GPUs. But who installs the drivers? Who configures the runtime? Who monitors GPU health?"

---

## Slide 15: What's Still Missing?
**Timing**: 1 minute

**Notes**:
Scheduling works, but operations are still manual.

**Build the List** (clicks):
1. **Driver installation**: "NVIDIA drivers, kernel modules, CUDA libraries—must match GPU and kernel"
2. **Runtime configuration**: "Container runtime needs to inject GPU devices into containers"
3. **Health monitoring**: "ECC errors, temperature throttling, GPU resets"
4. **Metrics collection**: "Utilization, memory usage, power draw"
5. **Keeping everything in sync**: "Driver version must match device plugin config, runtime must be configured correctly"

**Bottom Message** (click):
"All of this is manual work" (emphasize the pain)
- "SSH into each node"
- "Install drivers by hand"
- "Configure containerd manually"
- "Doesn't scale, not declarative"

**Transition**:
"Enter the GPU Operator."

---

## Slide 16: GPU Operator
**Timing**: 1 minute

**Notes**:
GPU Operator automates everything: drivers, runtime, monitoring.

**Big Message**:
"One operator to manage it all"

**Components** (build with clicks):
- **Driver containers**: "Automated driver installation"
- **Container toolkit**: "Runtime configuration"
- **GPU Feature Discovery**: "We already saw this—GFD labels"
- **Device plugin**: "We saw this too—exposes nvidia.com/gpu"
- **MIG manager**: "Handles GPU partitioning (coming up)"
- **DCGM metrics**: "Prometheus metrics for monitoring"

**Key Point**:
- "Six components, one operator"
- "Deploy once, everything is configured"
- "All declarative via ClusterPolicy custom resource"

---

## Slide 17: Declarative Management
**Timing**: 1 minute

**Notes**:
Configure once with ClusterPolicy. Operator handles the rest.

**Show the ClusterPolicy**:
- "This is a Kubernetes custom resource"
- "Defines desired state for GPU management"

**Walk Through**:
- **gfd: enabled**: "Turn on GPU Feature Discovery"
- **devicePlugin.config**: "Reference a ConfigMap for advanced config like time-slicing"
- **mig.strategy: mixed**: "Enable MIG with mixed profiles"

**Bottom Message** (click):
"Change policy, operator reconciles cluster"
- "Edit this YAML, apply it"
- "Operator sees the change, reconciles all nodes"
- "Drivers update, configs sync, everything stays aligned"
- "That's declarative management"

**Transition**:
"Now let's talk about deployment patterns. You know *how* to schedule GPUs—but *when* do you use what?"

---

## Slide 18: Deployment Patterns (Section Header)
**Timing**: Brief transition

**Notes**:
Model size and latency dictate layout

**Transition**:
"Different workloads need different GPU layouts. Let's see four common patterns."

---

## Slide 19: Four Common Patterns
**Timing**: 1 minute

**Notes**:
Different workloads need different GPU layouts.

**Present as Questions** (clicks):
1. "Need to handle more requests? → Throughput scaling"
2. "Model needs tight GPU cooperation? → Single-server multi-GPU"
3. "Model too huge for one server? → Multi-node multi-GPU"
4. "Workloads are light and bursty? → GPU sharing"

**Key Message**:
"The pattern you choose depends on your requirements: model size, latency needs, throughput targets, budget"

**Transition**:
"Let's look at each pattern."

---

## Slide 20: More Requests? Scale Horizontally
**Timing**: 1 minute

**Notes**:
Simple horizontal scaling. Each replica serves different requests.

**Show the Image**:
"Multiple independent model servers behind a load balancer"

**Explain**:
- "Just a Deployment with replicas: 8"
- "Each pod gets 1 GPU"
- "Each replica serves different user requests independently"

**Result** (click):
- "8 GPUs = 8× throughput"
- "Multiple independent replicas"

**Use Case**:
- "High queries per second"
- "Model fits in single GPU (e.g., 7B model quantized to 8GB)"
- "You have 1000 concurrent users—scale horizontally"

**Limitation**:
"Doesn't reduce single-request latency. Each request still processes on one GPU."

---

## Slide 21: Tight Cooperation? Tensor Parallelism
**Timing**: 1 minute

**Notes**:
When model needs 2-8 GPUs and low latency matters.

**Show the Image**:
"Multiple GPUs in one server, connected by fast NVLink"

**Explain** (clicks):
- "Split layers horizontally across GPUs"
- "All GPUs work on the same layer simultaneously"
- "Frequent communication after each layer—need fast interconnects"
- "NVLink provides ~600 GB/s bandwidth"

**YAML**:
- "Request `nvidia.com/gpu: 4`"
- "All on same node"

**Use Case**:
- "Model needs 2-8 GPUs worth of memory"
- "Low latency is critical"
- "Example: 70B model needs ~140GB—spread across 4× A100 (40GB each)"

---

## Slide 22: Huge Models? Pipeline Parallelism
**Timing**: 1 minute

**Notes**:
When no single server is big enough. Communication overhead increases.

**Show the Image**:
"Multiple nodes, each with GPUs, connected via network"

**Explain** (clicks):
- "Split model vertically by layers"
- "First layers on node 1, next layers on node 2"
- "Network communication (InfiniBand or Ethernet)"
- "~12.5 GB/s vs 600 GB/s intra-node—50x slower"

**Orchestration**:
- "Ray.io, StatefulSets, gang scheduling"
- "vLLM uses Ray internally for multi-node"

**Use Case**:
- "Model exceeds single-server capacity"
- "175B+ parameter models"

**Trade-off**:
- "Scales beyond one box, but communication overhead increases"
- "Operational complexity goes up"

---

## Slide 23: Tensor vs Pipeline Comparison
**Timing**: 1 minute

**Notes**:
Two parallelism strategies with different trade-offs.

**Show the Image**:
"Visual comparison of tensor (horizontal) vs pipeline (vertical) splitting"

**Walk Through Table**:
- **Split**: "Within layer vs by layer"
- **Communication**: "Frequent vs infrequent"
- **Best for**: "Single node vs multi-node"
- **Latency**: "Lower vs higher"

**Hybrid** (click):
- "In practice, combine them"
- "Tensor parallelism within each node (leverage NVLink)"
- "Pipeline parallelism across nodes (tolerate network)"
- "Example: 2 nodes, 4 GPUs each = 4-way tensor, 2-way pipeline"

---

## Slide 24: Light Workloads? Time-Slicing
**Timing**: 1 minute

**Notes**:
Oversubscribe GPUs for bursty, lightweight workloads.

**Explain**:
- "1 physical GPU → 8 virtual GPUs"
- "ConfigMap config: `replicas: 8`"
- "Each pod requests '1 GPU' but they're all on the same physical GPU"

**Key Points** (click):
- **No isolation**: "Pods share memory, can interfere"
- **Can burst**: "If others are idle, one pod can use full GPU"
- **90% cost savings**: "One A100 serving 8 small models instead of sitting idle"

**Use Case**:
- "Dev environments"
- "Bursty workloads (not all busy simultaneously)"
- "Lightweight inference"

**Warning**:
"Not for large LLMs—they need dedicated memory"

---

## Slide 25: Need Isolation? MIG
**Timing**: 1 minute

**Notes**:
MIG for safe sharing with guaranteed resources.

**Explain** (clicks):
- "Hardware partitioning on A100, H100, newer GPUs"
- "Dedicated memory per instance—can't steal from others"
- "Strong isolation—separate fault domains"
- "Fixed profiles—can't create arbitrary sizes"

**Profiles** (click):
- "1g.5gb, 2g.10gb, 4g.20gb, etc."
- "Example: Split A100 40GB into 7× 5GB instances"

**Use Case**:
- "Multi-tenant production"
- "Strict SLAs, guaranteed resources"
- "Multiple small models (e.g., several 7B models at ~5-10GB each)"

---

## Slide 26: Time-Slicing vs MIG
**Timing**: 1 minute

**Notes**:
Choose based on your isolation and cost requirements.

**Table Comparison**:
- **Isolation**: None vs Strong
- **Flexibility**: Any GPU vs A100/H100+ only
- **Use case**: Dev/bursty vs Production

**Summary** (click):
- "Time-slicing: flexibility and cost savings"
  - "Use for dev, experimentation, bursty workloads"
- "MIG: isolation and predictability"
  - "Use for production, multi-tenancy, guaranteed performance"

**Decision Framework**:
"Choose based on: Do you need isolation? Do you need guaranteed resources? Or do you prioritize flexibility and cost?"

---

## Slide 27: Best Practices (Section Header)
**Timing**: Brief transition

**Notes**:
Optimize for production

**Transition**:
"Let's wrap up with production best practices."

---

## Slide 28: Memory and Monitoring
**Timing**: 1 minute

**Notes**:
Production essentials: memory management and monitoring.

**Each Point** (clicks):
- **Pre-allocate**: "Avoid memory fragmentation—allocate large blocks upfront"
- **Quantize**: "4-bit/8-bit weights reduce memory 50-87% with minimal quality loss"
- **DCGM → Prometheus**: "GPU Operator deploys DCGM exporter automatically"
- **Watch metrics**: "Utilization, temperature, ECC errors"

**Monitoring is Critical**:
"GPUs are expensive. You need to know: are they being used? Are they healthy?"

---

## Slide 29: Scaling and Cost
**Timing**: 1 minute

**Notes**:
Scale intelligently and optimize costs.

**Each Point** (clicks):
- **Auto-scale**: "KEDA or HPA based on request queue depth"
- **Gang scheduling**: "Kueue, Volcano for multi-pod jobs—all pods start together"
- **Share underutilized GPUs**: "If utilization is low, use time-slicing or MIG"
- **Right-size**: "Don't use an 80GB A100 for a 7B model that fits in 14GB—use a cheaper T4"

**Cost Optimization**:
"GPUs are the most expensive part of your infrastructure. Share them when possible, right-size them always."

---

## Slide 30: What's New in 2025
**Timing**: 1 minute

**Notes**:
The ecosystem is maturing fast.

**Each Point** (clicks):
- **NVIDIA KAI Scheduler**: "Open-sourced in January 2025—enterprise GPU scheduler"
- **Queue-based governance**: "Kueue for fair sharing across teams with quotas"
- **GPUs as shared substrate**: "Cultural shift—treat GPUs as shared pool, not per-team pets"
- **DRA moving to production**: "Dynamic Resource Allocation gaining vendor support"

**Key Message**:
"The ecosystem is evolving rapidly. New tools, better governance, moving toward production-grade DRA."

---

## Slide 31: Next Steps
**Timing**: 1 minute

**Notes**:
You now know how to unlock GPUs on Kubernetes.

**Actionable Steps** (clicks):
1. **Deploy GPU Operator**: "One Helm install, everything configured"
2. **Start simple**: "nvidia.com/gpu: 1—resource-based scheduling"
3. **Add labels**: "For heterogeneous fleets, use nodeSelector or affinity"
4. **Choose pattern**: "Based on model size and requirements"
5. **Monitor and optimize**: "DCGM metrics, watch utilization, iterate"

**Summary** (click):
"Discovery + Allocation + Orchestration"
- "Three layers that build on each other"
- "You now understand how they fit together"

---

## Slide 32: Thank You
**Timing**: 1 minute

**Notes**:
End of presentation. Open for questions.

**Closing**:
- "That's it! You now know how to unlock GPUs on Kubernetes."
- "From invisible hardware to production-ready workloads."
- "Discovery, allocation, orchestration, and deployment patterns."

**Open for Questions**:
- "I'm happy to take questions now."
- "Or catch me after the session if you want to go deeper on specific topics."

---

## Additional Tips for Delivery

### General Presentation Tips
- **Pace yourself**: 1 minute per slide is the target, but some may go faster (section headers) and some slower (complex topics)
- **Use the images**: Point to the diagrams, especially the three-layer architecture and the parallelism comparisons
- **Interactive elements**: Use `<v-clicks>` to build slides progressively—don't show everything at once
- **Questions**: Pause after each section (Discovery, Allocation, Orchestration, Patterns) to ask "Any questions so far?"

### Technical Depth
- **Adjust for audience**: If audience is more beginner, spend more time on basics. If advanced, go deeper on MIG strategies and DRA
- **Real examples**: If you have production experience, substitute your own examples
- **Live demo**: Consider adding a 2-minute live demo of deploying GPU Operator if time permits

### Common Questions to Prepare For
1. **"What about AMD GPUs?"**: "AMD has their own device plugin (amd-device-plugin). Same concepts, different vendor."
2. **"Can I mix NVIDIA and AMD?"**: "Yes, different resource names. But operational complexity increases."
3. **"MIG vs time-slicing—which should I use?"**: "Depends on isolation needs. Production with SLAs → MIG. Dev/test → time-slicing."
4. **"What if I don't have GPUs yet?"**: "Start with the architecture. Deploy GPU Operator when you get GPUs. It's all declarative."
5. **"How much does this cost?"**: "GPU Operator is free (open source). GPUs themselves—that's your cloud or hardware bill."

### Transitions to Watch
- **Discovery → Allocation**: "Labels are great, but they don't make GPUs schedulable..."
- **Allocation → Orchestration**: "Scheduling works, but who manages the drivers?"
- **Orchestration → Patterns**: "Now let's talk about when to use what..."
- **Patterns → Best Practices**: "You know the patterns, let's optimize for production..."

### Energy and Engagement
- **Vary your tone**: Technical content can be dry—inject energy during transitions
- **Use pauses**: After showing code, pause to let audience read
- **Eye contact**: Don't just read slides—engage with audience
- **Movement**: Step away from podium during section transitions

Good luck with your presentation! 🎤
