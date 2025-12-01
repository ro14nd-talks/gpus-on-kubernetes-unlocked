---
layout: default
---

# Can Combine Time-Slicing and MIG for Maximum Density

```yaml
mig:
  strategy: single  # Create 2× 20GB MIG instances
sharing:
  replicas: 2       # Time-slice each MIG instance 2×
# Result: 4 schedulable units per physical GPU
```

<div class="mt-8 grid grid-cols-2 gap-8">

<div>

**Pattern:**
- Create MIG instances (memory isolation)
- Time-slice each instance (increase density)
- Balance: Some isolation + Higher utilization

</div>

<div>

**Example:**
- 7g.80gb MIG instance × 4 time-slicing replicas
- 4 pods share 80GB MIG partition
- Memory isolation + Compute sharing

</div>

</div>

<!--
Advanced technique: Combine both for best of both worlds.

Create MIG instances for memory isolation. Then time-slice each instance for density.

Example: 2× 20GB MIG instances, time-slice each 2×. Result: 4 virtual GPUs with some memory isolation.

Labels show -SHARED suffix to indicate this mode.

For most LLM inference, won't use these much - models too large. But for many smaller models or batch jobs, invaluable.

Speaking of large models: what if one GPU isn't enough?

Timing: 90 seconds
-->
