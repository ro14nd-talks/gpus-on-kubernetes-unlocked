---
layout: default
---

# Choose Time-Slicing for Flexibility, MIG for Isolation

<div class="overflow-auto">

|  | **Time-Slicing** | **MIG** |
|--------|--------------|-----|
| **Isolation & Faults** | Shared, cascade failures | Dedicated, isolated |
| **Resource Guarantees** | Time-sharing, variable | Memory + compute, predictable |
| **Flexibility** | Burst to full GPU | Fixed partition |
| **Complexity** | Simple replica count | Fixed profiles, partition mgmt |
| **Hardware Support** | All NVIDIA GPUs | Ampere+ only |
| **Use Case** | Dev, bursty workloads | Production, multi-tenant |

</div>

<div class="mt-4 px-4 py-2 bg-blue-200 dark:bg-blue-900 rounded text-center font-semibold" v-click>

Trade isolation for flexibility - choose based on your workload requirements

</div>

<!--
Streamlined comparison - 6 key decision factors:

1. Isolation & Faults: MIG provides true hardware isolation, time-slicing shares everything
2. Resource Guarantees: MIG guarantees memory+compute, time-slicing only guarantees time slices
3. Flexibility: Time-slicing can burst, MIG is locked to partition size
4. Complexity: Time-slicing is simple (just replica count), MIG requires understanding fixed profiles
5. Hardware Support: Time-slicing works on all GPUs, MIG needs Ampere+
6. Use Case: Dev/bursty → time-slicing, Production/multi-tenant → MIG

Decision framework:
- Need isolation? → MIG
- Need flexibility? → Time-slicing
- Have Ampere+ GPUs? → MIG available
- Have older GPUs (T4, V100)? → Time-slicing only option
- Dev environment? → Time-slicing
- Production multi-tenant? → MIG

Timing: 90 seconds
-->
