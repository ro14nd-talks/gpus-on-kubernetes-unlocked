---
layout: default
---

# Multi-Instance GPU Configuration Strategies

**Single strategy:**
```yaml
mig:
  strategy: single
  devices:
    all: "1g.5gb"
```
All GPUs partitioned identically (simple)

**Mixed strategy:**
```yaml
mig:
  strategy: mixed
  devices:
    "0": "2g.10gb"
    "1": "1g.5gb"
```
Different profiles per GPU (flexible)

<!--
MIG configuration strategies:

Single: All GPUs partitioned same way. Example: Every A100 split into 7× 1g.5gb. Simple, uniform capacity. Good for homogeneous workloads.

Mixed: Different partitions per GPU. Example: GPU 0 = 2× 2g.10gb (large models), GPU 1 = 7× 1g.5gb (small models). Flexible, matches diversity.

Configuration via GPU Operator ClusterPolicy. Apply different configs to different node pools using labels.

Reconfiguration requires: Drain pods, reconfigure MIG, restart device plugin. Not dynamic - plan carefully.

Timing: 90 seconds
-->
