---
layout: default
---

# MIG: Good For and Limitations

<div class="grid grid-cols-2 gap-8">

<div>

## ✅ Good For

- Multi-tenancy
- Production QoS requirements
- Multiple smaller models
- Guaranteed resources
- Fault isolation critical
- Newer hardware (A100, H100)

**Example:** 7 separate model services, each needs dedicated 5GB

</div>

<div>

## ❌ Limitations

- **Fixed profiles**
  Can't make arbitrary sizes

- **No bursting**
  Can't borrow unused capacity

- **Granularity**
  Smallest: 5GB (on A100)

- **Hardware requirement**
  Only Ampere+ GPUs

- **Complexity**
  More moving parts

**Not ideal:** Very large models (>40GB)

</div>

</div>

<!--
MIG shines for multi-tenant production:

Good: Multiple teams' workloads need isolation. Each team gets guaranteed slice, can't interfere.

Good: Many smaller models (7B params ~5-8GB). Give each its own MIG instance.

Bad: One giant model needing 80GB. MIG partitions it - would need whole GPU anyway.

Bad: Want 15GB partition. Stuck with fixed profiles - 10GB or 20GB, nothing between.

Bad: Workloads that could burst. Time-slicing lets you use idle capacity. MIG hard-limits to partition.

MIG is isolation and predictability at cost of flexibility.

Can combine them!

Timing: 120 seconds
-->
