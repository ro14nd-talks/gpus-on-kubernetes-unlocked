---
layout: default
---

# MIG Provides Isolation at Cost of Flexibility

<div class="grid grid-cols-2 gap-12 mt-6">

<div class="px-3 py-2 bg-green-200 dark:bg-green-900 rounded">

## 🌟 Good For

<div class="mt-3">

- Production multi-tenancy with isolation
- Multiple smaller models (7B params)
- Guaranteed resources required
- Ampere+ hardware (A100, H100)

</div>

</div>

<div class="px-3 py-2 bg-red-200 dark:bg-red-900 rounded">

## ⛔ Limitations

<div class="mt-3">

- Fixed partition sizes (min 5GB)
- No bursting beyond partition
- Ampere+ GPUs only
- More operational complexity

</div>

</div>

</div>

<div class="mt-4 px-4 py-2 bg-violet-200 dark:bg-violet-900 rounded">

<ul style="list-style: none; padding-left: 0;">
<li>⭐ Multi-tenant: 3 customers get 10GB slices - one crashes, others unaffected</li>
<li>⭐ 7 models (5GB each) share A100 with complete isolation</li>
<li>⛔ Dev needs 8GB, could burst to 15GB - locked at 10GB</li>
<li>⛔ 70B model (35GB) won't fit 20GB partition - needs full GPU</li>
</ul>

</div>

<div class="mt-4 px-4 py-2 bg-blue-200 dark:bg-blue-900 rounded text-center font-semibold" v-click>

For flexible sharing → use time-slicing instead

</div>

<!--
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

Timing: 120 seconds
-->
