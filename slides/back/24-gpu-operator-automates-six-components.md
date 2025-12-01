---
layout: default
---

# GPU Operator Automates Six Critical Components

<div class="mt-6 grid gap-8" style="grid-template-columns: 65% 35%;">

<div class="px-3 pt-2 pb-2 bg-green-100 dark:bg-green-900 rounded" style="display: flex; flex-direction: column; justify-content: center; height: 100%;">

<div class="mb-2">

### Managed Components

</div>

<table style="border: none; width: 100%; line-height: 1.3;">
<colgroup>
<col style="width: 5%;" />
<col style="width: 40%;" />
<col style="width: 55%;" />
</colgroup>
<tbody>
<tr style="border: none;"><td style="border: none; padding: 2px 4px; vertical-align: top;">✅</td><td style="border: none; padding: 2px 4px; vertical-align: top;"><strong>Device Plugin</strong></td><td style="border: none; padding: 2px 4px; vertical-align: top;"><em>Exposes nvidia.com/gpu resources</em></td></tr>
<tr style="border: none;"><td style="border: none; padding: 2px 4px; vertical-align: top;">✅</td><td style="border: none; padding: 2px 4px; vertical-align: top;"><strong>GPU Feature Discovery</strong></td><td style="border: none; padding: 2px 4px; vertical-align: top;"><em>Adds detailed GPU labels</em></td></tr>
<tr style="border: none;"><td style="border: none; padding: 2px 4px; vertical-align: top;">⬜</td><td style="border: none; padding: 2px 4px; vertical-align: top;"><strong>MIG Manager</strong></td><td style="border: none; padding: 2px 4px; vertical-align: top;"><em>Configures GPU partitioning</em></td></tr>
<tr style="border: none;"><td colspan="3" style="border: none; padding: 4px 0;"><hr style="border: none; border-top: 1px solid rgba(0,0,0,0.2); margin: 0;" /></td></tr>
<tr style="border: none;"><td style="border: none; padding: 2px 4px; vertical-align: top;">⬜</td><td style="border: none; padding: 2px 4px; vertical-align: top;"><strong>Driver Container</strong></td><td style="border: none; padding: 2px 4px; vertical-align: top;"><em>Installs NVIDIA drivers on host nodes</em></td></tr>
<tr style="border: none;"><td style="border: none; padding: 2px 4px; vertical-align: top;">⬜</td><td style="border: none; padding: 2px 4px; vertical-align: top;"><strong>Container Toolkit</strong></td><td style="border: none; padding: 2px 4px; vertical-align: top;"><em>Configures runtime to expose GPUs to containers</em></td></tr>
<tr style="border: none;"><td style="border: none; padding: 2px 4px; vertical-align: top;">⬜</td><td style="border: none; padding: 2px 4px; vertical-align: top;"><strong>DCGM Exporter</strong></td><td style="border: none; padding: 2px 4px; vertical-align: top;"><em>Prometheus metrics for monitoring</em></td></tr>
</tbody>
</table>

</div>

<div style="font-size: 0.9em; padding-right: 2rem;">

```yaml {5-11,lines:false}
apiVersion: nvidia.com/v1
kind: ClusterPolicy
metadata:
  name: gpu-cluster-policy
spec:
  driver:
    enabled: true
  toolkit:
    enabled: true
  devicePlugin:
    enabled: true
    config:
      name: gpu-sharing-config
  dcgmExporter:
    enabled: true
  mig:
    strategy: mixed
```

</div>

</div>

<div class="mt-6 px-3 pt-2 pb-2 bg-blue-100 dark:bg-blue-900 rounded text-center font-semibold" v-click>

**Change one YAML → operator reconfigures entire cluster**

</div>

<!--
NVIDIA GPU Operator is the orchestration layer—one operator managing everything.

We've seen Device Plugin (✓) and GFD (✓) in earlier sections. Now the full picture:

1. Driver Container: Automated driver deployment via privileged container
   - Compiles drivers for node's kernel or uses precompiled version
   - No SSH to nodes, no manual installation

2. Container Toolkit: Runtime hooks that inject GPU devices
   - Extends containerd/CRI-O to inject /dev/nvidia* devices
   - Automatic GPU driver access for containers

3. Device Plugin (✓): Already covered in allocation layer
   - Exposes nvidia.com/gpu as schedulable resource

4. GPU Feature Discovery (✓): Already covered in discovery layer
   - Enriches nodes with detailed NVIDIA-specific labels

5. MIG Manager: Handles Multi-Instance GPU partitioning
   - Automates MIG partition setup (we'll cover sharing strategies soon)
   - No manual nvidia-smi configuration needed

6. DCGM Exporter: GPU health and performance metrics
   - Utilization, memory, temperature, ECC errors → Prometheus
   - Integrates with cluster monitoring stack

All configured through single ClusterPolicy custom resource.
Fully declarative: no SSH, no manual updates, everything stays in sync.

Key message: "Instead of SSHing into nodes to install drivers and configure runtimes, you declare desired state in ClusterPolicy and the operator reconciles everything."

Timing: 90 seconds
-->
