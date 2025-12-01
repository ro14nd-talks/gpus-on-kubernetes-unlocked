---
theme: default
background: white
class: text-center
highlighter: shiki
lineNumbers: true
drawings:
  persist: false
transition: slide-left
title: GPUs on Kubernetes Unlocked
info: |
  ## GPUs on Kubernetes Unlocked

  Understanding GPU scheduling and resource management

  Presenter: Roland Huß, Distinguished Engineer, Red Hat
mdc: true
---

# GPUs on Kubernetes Unlocked

## Understanding GPU scheduling and resource management

<div class="mt-16">

### Roland Huß
#### Distinguished Engineer, Red Hat

</div>

---
src: ./slides/02-book-introduction.md
---
<!-- Slide 2: Book Introduction -->

---
src: ./slides/03-kubernetes-lacks-native-gpu-support.md
---
<!-- Slide 3: Kubernetes Lacks Native GPU Support -->

---
src: ./slides/04-three-layers-enable-gpu-scheduling.md
---
<!-- Slide 4: Three Layers Enable GPU Scheduling -->

---
src: ./slides/05-section-discovery-layer.md
---
<!-- Slide 5: Discovery Layer -->

---
src: ./slides/06-heterogeneous-clusters-need-hardware-labels.md
---
<!-- Slide 6: Heterogeneous Clusters Need Hardware Labels -->

---
src: ./slides/07-nfd-detects-general-hardware-features.md
---
<!-- Slide 7: NFD Detects General Hardware Features -->

---
src: ./slides/08-gfd-adds-gpu-specific-intelligence.md
---
<!-- Slide 8: GFD Adds GPU-Specific Intelligence -->

---
src: ./slides/09-nfd-vs-gfd-different-levels-of-detail.md
---
<!-- Slide 9: NFD vs GFD: Different Levels of Detail -->

---
src: ./slides/10-section-allocation-layer.md
---
<!-- Slide 10: Allocation Layer -->

---
src: ./slides/11-device-plugins-turn-gpus-into-schedulable-resources.md
---
<!-- Slide 11: Device Plugins Turn GPUs Into Schedulable Resources -->

---
src: ./slides/12-resource-based-scheduling-simple-and-direct.md
---
<!-- Slide 12: Resource-Based Scheduling: Simple and Direct -->

---
src: ./slides/13-resource-based-strengths-and-limitations.md
---
<!-- Slide 13: Resource-Based: Strengths and Limitations -->

---
src: ./slides/14-nodeselector-provides-simple-label-matching.md
---
<!-- Slide 14: nodeSelector Provides Simple Label Matching -->

---
src: ./slides/15-node-affinity-enables-rich-gpu-selection.md
---
<!-- Slide 15: Node Affinity Enables Rich GPU Selection -->

---
src: ./slides/16-taints-protect-gpu-nodes-from-cpu-workloads.md
---
<!-- Slide 16: Taints Protect GPU Nodes From CPU Workloads -->

---
src: ./slides/17-when-to-use-each-scheduling-approach.md
---
<!-- Slide 17: When to Use Each Scheduling Approach -->

---
src: ./slides/18-combined-approach-is-production-best-practice.md
---
<!-- Slide 18: Combined Approach Is Production Best Practice -->

---
src: ./slides/19-dra-requests-gpus-by-intent-not-model.md
---
<!-- Slide 19: DRA Requests GPUs By Intent Not Model -->

---
src: ./slides/20-pods-claim-resources-scheduler-allocates-devices.md
---
<!-- Slide 20: Pods Claim Resources, Scheduler Allocates Devices -->

---
src: ./slides/21-dra-benefits-and-trade-offs.md
---
<!-- Slide 21: DRA: Benefits and Trade-offs -->

---
src: ./slides/22-section-orchestration-layer.md
---
<!-- Slide 22: Orchestration Layer -->

---
src: ./slides/23-gpu-clusters-need-five-operational-components.md
---
<!-- Slide 23: GPU Clusters Need Five Operational Components -->

---
src: ./slides/24-gpu-operator-automates-six-components.md
---
<!-- Slide 24: GPU Operator Automates Six Critical Components -->

---
src: ./slides/25-section-gpu-sharing.md
---
<!-- Slide 25: GPU Sharing -->

---
src: ./slides/26-gpu-sharing-maximizes-utilization.md
---
<!-- Slide 26: GPU Sharing Maximizes Utilization -->

---
src: ./slides/27-time-slicing-oversubscribes-gpus.md
---
<!-- Slide 27: Time-Slicing Oversubscribes GPUs -->

---
src: ./slides/28-time-slicing-good-for-and-watch-out-for.md
---
<!-- Slide 28: Time-Slicing: Good For and Watch Out For -->

---
src: ./slides/29-mig-provides-hardware-isolated-partitions.md
---
<!-- Slide 29: MIG Provides Hardware-Isolated Partitions -->

---
src: ./slides/30-mig-good-for-and-limitations.md
---
<!-- Slide 30: MIG: Good For and Limitations -->

---
src: ./slides/31-time-slicing-vs-mig-choosing-the-right-strategy.md
---
<!-- Slide 31: Time-Slicing vs MIG: Choosing the Right Strategy -->

---
src: ./slides/32-can-combine-time-slicing-and-mig-for-maximum-density.md
---
<!-- Slide 32: Can Combine Time-Slicing and MIG for Maximum Density -->

---
src: ./slides/33-gpu-sharing-decision-framework.md
---
<!-- Slide 33: GPU Sharing Decision Framework -->

---
src: ./slides/34-section-multi-gpu-patterns.md
---
<!-- Slide 34: Multi-GPU Patterns -->

---
src: ./slides/35-two-motives-for-multi-gpu.md
---
<!-- Slide 35: Two Motives for Multi-GPU -->

---
src: ./slides/36-throughput-scaling-characteristics.md
---
<!-- Slide 36: Throughput Scaling Characteristics -->

---
src: ./slides/37-tensor-parallelism-splits-layers-horizontally.md
---
<!-- Slide 37: Tensor Parallelism Splits Layers Horizontally -->

---
src: ./slides/38-pipeline-parallelism-splits-layers-vertically.md
---
<!-- Slide 38: Pipeline Parallelism Splits Layers Vertically -->

---
src: ./slides/39-tensor-vs-pipeline-parallelism-trade-offs.md
---
<!-- Slide 39: Tensor vs Pipeline Parallelism Trade-offs -->

---
src: ./slides/40-single-node-fast-and-simple.md
---
<!-- Slide 40: Single-Node: Fast and Simple -->

---
src: ./slides/41-multi-node-topology-and-bandwidth-hierarchy.md
---
<!-- Slide 41: Multi-Node Topology and Bandwidth Hierarchy -->

---
src: ./slides/42-five-strategies-maximize-gpu-utilization.md
---
<!-- Slide 42: Five Strategies Maximize GPU Utilization -->

---
src: ./slides/43-2025-trends-shift-gpus-to-shared-substrate.md
---
<!-- Slide 43: 2025 Trends Shift GPUs to Shared Substrate -->

---
src: ./slides/44-backup-three-layers-unlock-gpu-scheduling.md
---
<!-- Slide 44: Three Layers Unlock GPU Scheduling -->

---
src: ./slides/45-questions.md
---
<!-- Slide 45: Questions? -->

---
src: ./slides/46-backup-section.md
---
<!-- Slide 46: Backup Slides -->

---
src: ./slides/47-backup-nccl-and-rdma-enable-fast-gpu-communication.md
---
<!-- Slide 47: NCCL and RDMA Enable Fast GPU Communication -->

---
src: ./slides/48-backup-debugging-gpu-scheduling-issues.md
---
<!-- Slide 48: Debugging GPU Scheduling Issues -->

---
src: ./slides/49-backup-multi-instance-gpu-configuration-strategies.md
---
<!-- Slide 49: Multi-Instance GPU Configuration Strategies -->
