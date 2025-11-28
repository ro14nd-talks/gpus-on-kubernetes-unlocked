---
layout: default
---

# Throughput Scaling Characteristics

<div class="grid grid-cols-2 gap-8">

<div>

## How It Works

1. Model fits on 1 GPU
2. Deploy N replicas on N GPUs
3. Load balancer distributes requests
4. Each replica independent

**Kubernetes Pattern:**

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  replicas: 8
  template:
    spec:
      containers:
      - name: model
        resources:
          limits:
            nvidia.com/gpu: 1
```

</div>

<div>

## When to Use

- Model < 1 GPU memory
- High request volume
- Want simple scaling
- Can tolerate memory footprint

**Example:** 7B model (~8GB) on 8× A100 for 800 RPS

**Scaling:** Simple (HPA/KEDA)

**Memory Cost:** N × model size

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
