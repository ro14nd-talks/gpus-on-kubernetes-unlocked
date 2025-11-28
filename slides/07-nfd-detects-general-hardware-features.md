---
layout: default
---

# NFD Provides Foundation for Hardware Discovery

<v-clicks>

- **General-purpose** hardware discovery
- Detects CPU, PCI devices, network
- Runs as DaemonSet and labels nodes automatically

</v-clicks>

<div v-click class="mt-4">

```yaml {fontSize:'11px'}
feature.node.kubernetes.io/pci-0302_10de.present: "true"
feature.node.kubernetes.io/pci-0302_1002.present: "true"
feature.node.kubernetes.io/kernel-version.full: "6.6.8"
feature.node.kubernetes.io/system-os_release.ID: "fedora"
```

<div class="mt-4 text-sm">

**PCI Label format:** `pci-<class>_<vendor>`
- **0302** = PCI class (3D graphics controller)
- **10de** = NVIDIA vendor ID | **1002** = AMD vendor ID

</div>

</div>

<div v-click class="mt-4 p-4 bg-blue-100 dark:bg-blue-900 rounded text-center">

Detects GPU **presence**, not model or memory

</div>

<!--
Node Feature Discovery is the foundation - detects all hardware types.

It labels nodes with CPU details, PCI devices, network interfaces.

For GPUs: detects PCI device class (0302) and vendor ID (10de for NVIDIA).

But NFD only knows "GPU exists" - not model, memory, or capabilities.

Timing: 120 seconds
-->
