---
layout: default
---

# Data Parallelism Trades Memory for Throughput

<div class="grid grid-cols-2 gap-6 mb-8">

<div class="px-4 pt-3 pb-3 bg-green-100 dark:bg-green-900 rounded">

<div class="mb-2">

### 🌟 Strengths

</div>

- Model fits single GPU
- High request volume (QPS)
- Simple autoscaling needed
- Independent requests

</div>

<div class="px-4 pt-3 pb-3 bg-red-100 dark:bg-red-900 rounded">

<div class="mb-2">

### ⛔ Limitations

</div>

- N replicas = N × memory cost
- Doesn't reduce latency
- Won't help if model > 1 GPU
- Each GPU loads full model

</div>

</div>

<div class="grid grid-cols-2 gap-6">

<div>

```yaml {lines:false}
apiVersion: apps/v1
kind: Deployment
spec:
  replicas: 8  # <-- Linear scaling
  template:
    spec:
      containers:
      - name: model
        resources:
          limits:
            nvidia.com/gpu: 1
```

</div>

<div class="flex items-center justify-center">
  <img src="/images/data-parallelism-throughput.svg" alt="Data Parallelism Throughput Scaling" class="max-w-full max-h-48 object-contain" />
</div>

</div>

<!--
Straightforward horizontal scaling.

Kubernetes-native: Deployment + Service. Autoscale with HPA/KEDA.

Limitations:
- Each replica: full model in memory. 8 replicas of 40GB model = 320GB total
- Doesn't reduce latency
- Doesn't help if model doesn't fit 1 GPU

For models exceeding GPU memory, use model parallelism.

Timing: 90 seconds
-->
