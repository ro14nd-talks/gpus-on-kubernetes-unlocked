---
layout: default
---

# Time-Slicing vs MIG: Choosing the Right Strategy

<div class="overflow-auto">

| Aspect | Time-Slicing | MIG |
|--------|--------------|-----|
| **Isolation** | None (shared memory) | Strong (dedicated memory) |
| **Fault Tolerance** | One crash affects all | Isolated fault domains |
| **Resource Guarantee** | Compute time only | Memory + compute |
| **Flexibility** | Can burst to full GPU | Fixed partition |
| **Hardware Support** | All NVIDIA GPUs | Ampere+ only (A100, H100) |
| **Configuration** | Replica count | Fixed profiles |
| **Overhead** | Minimal | Some (partition mgmt) |
| **Use Case** | Dev, bursty workloads | Production, multi-tenant |
| **Performance** | Variable (contention) | Predictable |

</div>

<!--
Quick comparison summary:

Time-slicing: Flexible, but no isolation
MIG: Isolated, but rigid

Your choice depends on requirements:
- Need isolation? MIG
- Need flexibility? Time-slicing
- Have Ampere+ GPUs? MIG is an option
- Have older GPUs? Time-slicing only
- Dev environment? Time-slicing
- Production multi-tenant? MIG

Timing: 90 seconds
-->
