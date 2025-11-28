---
layout: default
---

# When to Use Each Scheduling Approach

<img src="/images/decision-tree.svg" class="w-full" alt="GPU Scheduling Decision Tree" />

<!--
Decision tree for scheduling approach:

Homogeneous cluster (one GPU type): Resource request sufficient

Multiple GPU types:
- Know exact model needed: nodeSelector
- Flexible requirements (min memory): Affinity with Gt
- Need to protect expensive nodes: Add taints

Pattern: Start simple, add complexity only when needed.

Most production: Resource + affinity + taints combined

Timing: 90 seconds
-->
