---
layout: default
---

# NFD Detects Presence, GFD Provides Specifications

<div class="overflow-auto mt-8">

| Aspect | **NFD** | **GFD** |
|--------|-----|-----|
| **Scope** | General hardware | NVIDIA GPUs |
| **Detail** | Basic presence | Detailed specs |
| **Labels** | `feature.node.k8s.io/` | `nvidia.com/` |
| **Use Case** | "Has GPU" | "Has A100 40GB" |
| **Install** | Standalone | Via GPU Operator |

</div>

<!--
Quick comparison:

NFD: "this node has an NVIDIA GPU"
GFD: "this node has 4× A100 40GB GPUs with CUDA 12.2"

Both label the same nodes, different detail levels.

The scheduler uses these labels for intelligent placement decisions.

Now that we can discover and label GPUs, how do we allocate them?

Timing: 90 seconds
-->
