# NVIDIA MIG Provides Hardware-Isolated Partitions

<div class="mt-3 px-2 py-1 bg-gray-100 dark:bg-gray-800 rounded">

**Fixed Profiles** (A100, H100, B100/B200 only) — Examples: 1g.5gb, 2g.10gb, 3g.20gb, 7g.40gb

</div>

<div class="grid grid-cols-2 gap-6 mt-3">

<div class="px-2 py-1 bg-cyan-100 dark:bg-cyan-900 rounded text-sm" style="line-height: 1.3;">

<div style="margin-bottom: 0.25rem; font-weight: 600;">Single Strategy</div>
<ul style="margin: 0; padding-left: 1.2rem; line-height: 1.4;">
<li>Identical GPU partitioning, simple to manage</li>
<li>Easy to scale uniformly</li>
</ul>

</div>

<div class="px-2 py-1 bg-purple-100 dark:bg-purple-900 rounded text-sm" style="line-height: 1.3;">

<div style="margin-bottom: 0.25rem; font-weight: 600;">Mixed Strategy</div>
<ul style="margin: 0; padding-left: 1.2rem; line-height: 1.4;">
<li>Per-GPU partitioning, flexible placement</li>
<li>More complex configuration</li>
</ul>

</div>

</div>

<div class="grid grid-cols-2 gap-8 mt-3">

<div style="font-size: 0.9em;">

```yaml {lines:false}
# ClusterPolicy
devicePlugin:
  config:
    name: mig-config        # ConfigMap name
    default: mixed-profile  # Profile name
---
# ConfigMap: mig-config
data:
  mixed-profile: |         # Profile referenced above
    mig-configs:
      ...:
        mig-devices:
          "2g.10gb": 2     # 2× 10GB (4/7 slices)
          "3g.20gb": 1     # 1× 20GB (3/7 slices)
```

</div>

<div style="display: flex; align-items: center; justify-content: center;">

<img src="/images/mig-partition.svg" alt="MIG Partitioning" style="width: 100%; max-width: 650px;" />

</div>

</div>

<!--
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

Timing: 120 seconds
-->
