---
layout: default
---

# DRA Is Promising But Not Production-Ready

<div class="mt-20 grid grid-cols-2 gap-8">

<div v-click="1" class="px-4 pt-3 pb-3 bg-green-100 dark:bg-green-900 rounded">

<div class="mb-2">

### ✅ Benefits

</div>

- Precise requirements
- Dynamic allocation
- No manual labels
- Future-proof architecture

</div>

<div v-click="2" class="px-4 pt-3 pb-3 bg-orange-100 dark:bg-orange-900 rounded">

<div class="mb-2">

### ❌ Current Limitations

</div>

- Beta API
- Limited adoption
- Experimental driver
- Feature gate required

</div>

</div>

<div v-click="3" class="mt-12 py-2 px-3 bg-purple-100 dark:bg-purple-900 rounded text-center">

Wait for production, but start learning now

</div>

<!--
DRA benefits are compelling: precise specifications like "40GB+ A100" without manual label conventions, intelligent dynamic allocation resolved at schedule time.

Example use cases for DRA:
- MIG partitioning
- Topology-aware allocation
- Device sharing policies
- Composable multi-device requests

Current status and limitations:
- Beta in Kubernetes 1.33 (disabled by default, requires feature gate)
- API still evolving, may change
- NVIDIA DRA driver experimental
- Few production users, limited ecosystem adoption
- Cluster autoscalers don't fully understand DRA claims yet

Future direction:
- DRA is Kubernetes' intended replacement for device plugins
- Will become the standard mechanism for extended resources
- Vendors (NVIDIA, AMD, Intel) actively developing DRA drivers
- Moving toward production readiness

Recommendation: Use traditional scheduling (resource requests + labels) for production today, but start learning DRA now.

When it stabilizes, DRA will replace much manual labeling and selection logic with intent-based requests.

Timing: 90 seconds
-->
