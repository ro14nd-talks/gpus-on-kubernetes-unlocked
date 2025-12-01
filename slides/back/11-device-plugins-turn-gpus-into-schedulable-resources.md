---
layout: default
---

# Device Plugins Turn GPUs Into Schedulable Resources

<div class="mt-6">

**Four Core Functions:**

<v-clicks>

1. **Device Discovery** — Detect GPUs, report to Kubelet
2. **Resource Allocation** — Exclusive access by default
3. **Health Monitoring** — Report unhealthy devices
4. **Scheduler Integration** — Expose as `nvidia.com/gpu`

</v-clicks>

</div>

<div v-click class="mt-6">

```plantuml {scale: 0.75}
@startuml
!define ICONURL https://raw.githubusercontent.com/tupadr3/plantuml-icon-font-sprites/v3.0.0
skinparam componentStyle rectangle
skinparam defaultTextAlignment center

rectangle "Device Plugin" as DP #a5d8ff {
}

rectangle "Kubelet" as K #d0bfff {
}

rectangle "API Server" as API #b2f2bb {
}

rectangle "Scheduler" as S #ffc9c9 {
}

rectangle "GPU Hardware" as GPU #ffe066 {
}

DP -right-> K : 1. Register\n(gRPC)
K -left-> DP : 2. List Devices
DP -right-> K : 3. Report GPUs
K -right-> API : 4. Advertise\n<color:green>nvidia.com/gpu: 4</color>
API -down-> S : 5. Read
GPU -up[dashed]-> DP : Discovers

note right of S
  Scheduler can now
  schedule GPU pods
end note

@enduml
```

</div>

<div v-click class="mt-4 p-4 bg-blue-100 dark:bg-blue-900 rounded text-center font-semibold">

**The magic moment:** GPUs go from invisible to schedulable

</div>

<!--
Device Plugin framework extends Kubernetes beyond CPU and memory.

gRPC interface between kubelet and vendor plugin.

The flow:
1. Device plugin registers with kubelet via gRPC
2. Kubelet asks for device list
3. Plugin reports GPU inventory (IDs, health)
4. Kubelet advertises to API server as extended resource
5. Scheduler reads nvidia.com/gpu: 4 and can schedule pods

Clean abstraction - Kubernetes doesn't need GPU-specific code.

NVIDIA, AMD, Intel each provide their own device plugin.

This is the magic moment—GPUs go from invisible hardware to first-class schedulable resources.

Timing: 120 seconds
-->
