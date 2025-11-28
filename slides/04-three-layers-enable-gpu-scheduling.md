---
layout: center
---

# Three Layers Enable GPU Scheduling

<div class="flex justify-center">
  <img src="/images/layered-architecture.svg" class="h-100" alt="Three-layer GPU architecture showing Orchestration, Allocation, and Discovery layers" />
</div>

<!--
The solution has three distinct layers working together:

1. Discovery Layer: NFD and GFD label nodes with GPU capabilities
2. Allocation Layer: Device plugins expose GPUs as schedulable resources
3. Orchestration Layer: GPU Operator manages the complete lifecycle

Each layer builds on the one below it. Let's start at the foundation.

Timing: 90 seconds
-->
