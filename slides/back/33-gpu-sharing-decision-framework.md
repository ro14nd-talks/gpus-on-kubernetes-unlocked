---
layout: default
---

# GPU Sharing Decision Framework

```mermaid
graph TD
    A[Need GPU sharing?] -->|Yes| B[Need isolation?]
    B -->|Yes| C[Have Ampere+ GPUs?]
    C -->|Yes| D[MIG]
    C -->|No| E[Time-slicing<br/>No isolation option]
    B -->|No| F[Bursty workloads?]
    F -->|Yes| G[Time-slicing]
    F -->|No| H[Production SLA?]
    H -->|Yes| D
    H -->|No| G

    A -->|No| I[Exclusive allocation]

    style D fill:#4CAF50
    style G fill:#2196F3
    style E fill:#FF9800
    style I fill:#9C27B0
```

<!--
Decision tree for GPU sharing:

Start: Do you need sharing at all, or is exclusive allocation sufficient?

If sharing:
- Need isolation (production, multi-tenant): MIG if available, otherwise time-slicing with caution
- Don't need isolation (dev, bursty): Time-slicing
- Production SLA requirements: MIG preferred

Most LLM inference: Exclusive allocation (models too large)
Many small models: Consider sharing

Timing: 90 seconds
-->
