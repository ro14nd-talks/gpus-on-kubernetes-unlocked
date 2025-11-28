---
layout: default
---

# Two Motives for Multi-GPU

<div class="grid grid-cols-2 gap-8">

<div>

## 1. Throughput Scaling

<div class="text-sm mt-2">(Data Parallelism)</div>

```mermaid
graph TD
    A[Load Balancer] --> B[Replica 1<br/>GPU 0]
    A --> C[Replica 2<br/>GPU 1]
    A --> D[Replica 3<br/>GPU 2]
    A --> E[Replica 4<br/>GPU 3]

    style A fill:#FF9800
    style B fill:#4CAF50
    style C fill:#4CAF50
    style D fill:#4CAF50
    style E fill:#4CAF50
```

**Goal:** Serve more concurrent requests
**Method:** Multiple independent replicas
**Benefit:** Higher QPS

</div>

<div>

## 2. Model Parallelism

<div class="text-sm mt-2">(Model too large for 1 GPU)</div>

```mermaid
graph LR
    A[Request] --> B[GPU 0<br/>Layers 1-10]
    B --> C[GPU 1<br/>Layers 11-20]
    C --> D[GPU 2<br/>Layers 21-30]
    D --> E[Response]

    style B fill:#2196F3
    style C fill:#2196F3
    style D fill:#2196F3
```

**Goal:** Fit model exceeding 1 GPU
**Method:** Split model across GPUs
**Benefit:** Serve larger models

</div>

</div>

<!--
Two completely different reasons to use multiple GPUs:

1. Throughput (left): Model fits on 1 GPU, want to handle more requests/sec. Solution: N copies on N GPUs, each handles different requests. Horizontal scaling.

2. Model Parallelism (right): Model too big for 1 GPU. Need multiple GPUs to hold one instance. Solution: Split model across GPUs, each processes part of each request. Vertical scaling.

Fundamentally different patterns with different trade-offs. Let's explore each.

Timing: 120 seconds
-->
