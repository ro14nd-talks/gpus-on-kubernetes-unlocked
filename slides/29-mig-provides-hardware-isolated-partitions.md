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
Multi-Instance GPU (MIG) is true hardware partitioning, not time-sharing.

MIG notation: The format is {compute_slices}g.{memory}gb.
The "g" number refers to GPU compute slices (streaming multiprocessor groups).
An A100-80GB has 7 compute slices and 80GB total, so:
- 1g.10gb = 1/7 compute + 10GB memory (smallest useful partition)
- 2g.20gb = 2/7 compute + 20GB memory (good for medium inference)
- 3g.40gb = 3/7 compute + 40GB memory (larger models)
- 7g.80gb = full GPU (no partitioning)
The numbers must add up: 2x "2g.20gb" + 1x "3g.40gb" = 7 slices, 80GB total.
Not all combinations are valid, NVIDIA defines fixed profiles per GPU model.

Each MIG instance gets:
- Dedicated compute cores (not shared, not time-sliced)
- Fixed memory with hardware enforcement (can't exceed allocation)
- Separate fault domain (one instance crashes, others keep running)

Strategies:
- Single: All GPUs same MIG config (simple but inflexible)
- Mixed: Different partitions per GPU (flexible, complex)

MIG Manager in GPU Operator handles partition creation automatically.

Timing: 120 seconds
-->
