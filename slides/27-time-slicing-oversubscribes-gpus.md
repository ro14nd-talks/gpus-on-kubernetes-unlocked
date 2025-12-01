# Time-Slicing Oversubscribes GPUs

<div class="grid grid-cols-2 gap-8 mt-3">

<div>

<div class="px-3 py-2 bg-blue-100 dark:bg-blue-900 rounded">

- 1 physical GPU → N virtual GPUs
- Processes interleave (like CPU time-sharing)
- No memory isolation
- Node advertises N GPUs to scheduler

</div>

<div class="mt-2">

```yaml {lines:false}
# ConfigMap
sharing:
  timeslicing:
    resources:
    - name: nvidia.com/gpu
      replicas: 4  # 1 physical → 4 virtual
---
# Referenced in ClusterPolicy
devicePlugin:
  config:
    name: time-slicing-config
```

</div>

</div>

<div class="mt-6">

<img src="/images/time-slicing-funnel.svg" alt="Time-slicing funnel diagram" class="w-full" />

</div>

</div>

<div class="mt-3 px-3 py-1 bg-blue-100 dark:bg-blue-900 rounded text-center font-semibold" v-click>

Trade memory isolation for higher density — use for dev/test and bursty workloads

</div>

<!--
Time-slicing is oversubscription.

1 physical GPU advertised as N schedulable resources. Kubernetes thinks N GPUs exist.

Pods time-share actual GPU - similar to CPU scheduler interleaving processes.

ConfigMap defines sharing policy, referenced in ClusterPolicy devicePlugin config.

Key: Compute time-sharing, NOT memory isolation. All pods share same VRAM.

Why dev/test and bursty workloads:
- Dev workloads are intermittent (short experiments, tests, debugging) with lots of idle time
- Bursty workloads can use full GPU when available (unlike MIG's hard partitions)
- Cost efficiency over isolation (share 1 GPU among 4-8 developers vs buying each a dedicated GPU)
- No strict SLAs needed - acceptable performance variability
- NOT for production: no memory isolation = pods can interfere, unpredictable performance

Production alternative: MIG provides hardware isolation and guaranteed resources.

Timing: 90 seconds
-->
