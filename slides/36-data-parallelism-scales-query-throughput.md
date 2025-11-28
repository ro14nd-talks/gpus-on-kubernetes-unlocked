---
layout: image-right
image: /images/ch05-gpu-throughput-scaling.png
backgroundSize: contain
---

# Data Parallelism Scales Query Throughput

**Pattern:** Independent model replicas

**Kubernetes:**
```yaml
replicas: 8
resources:
  limits:
    nvidia.com/gpu: 1
```

**Characteristics:**
- Latency: Same per-request
- Throughput: Linear scaling
- Coordination: None (independent)

<!--
Throughput scaling is straightforward: multiple independent copies.

If one instance handles 100 requests/sec, 8 instances handle 800.

Each replica completely independent. No coordination needed.

In Kubernetes: Deployment with replicas: 8. Add Service for load balancing.

Can autoscale with HPA based on RPS or latency.

Limitations: Each replica loads full model. Doesn't help with latency. Doesn't help if model doesn't fit 1 GPU.

For large models exceeding GPU memory, need model parallelism.

Timing: 120 seconds
-->
