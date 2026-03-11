---
layout: default
---

# DRA Is Now Production-Ready

<div class="mt-20 grid grid-cols-2 gap-8">

<div class="px-4 pt-3 pb-3 bg-green-200 dark:bg-green-900 rounded">

<div class="mb-2">

### 🌟 Benefits

</div>

- Precise requirements
- Dynamic allocation
- No manual labels
- Future-proof architecture

</div>

<div class="px-4 pt-3 pb-3 bg-red-200 dark:bg-red-900 rounded">

<div class="mb-2">

### ⚠️ Considerations

</div>

- GA since K8s 1.34
- Sub-features still maturing
- Migration from device plugins
- Ecosystem catching up

</div>

</div>

<div v-click class="mt-12 py-2 px-3 bg-blue-200 dark:bg-blue-900 rounded text-center font-semibold">

Evaluate migrating from device plugins to DRA

</div>

<!--
DRA benefits are compelling: precise specifications like "40GB+ A100" without manual label conventions, intelligent dynamic allocation resolved at schedule time.

Example use cases for DRA:
- MIG partitioning
- Topology-aware allocation
- Device sharing policies
- Composable multi-device requests

Current status (as of March 2026):
- GA since Kubernetes 1.34 (stable, enabled by default)
- Stable API: resource.k8s.io/v1
- Beta features: admin access controls, prioritized lists
- Alpha features: extended resource allocation, device taints/tolerations
- Cloud providers actively integrating (GKE, AKS)
- NVIDIA, AMD, Intel shipping DRA drivers

DRA is Kubernetes' intended replacement for device plugins and the standard mechanism for device allocation going forward.

Recommendation: DRA is production-ready. Evaluate migration from device plugins + labels to intent-based DRA claims.

Timing: 90 seconds
-->
