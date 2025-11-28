---
layout: section
---

# Allocation Layer

## Making GPUs Schedulable

<div class="flex justify-center mt-12">
  <img src="/images/layered-archictecture-allocation.svg" class="h-30 opacity-60" alt="Allocation layer highlighted in the three-layer architecture" />
</div>

<!--
Discovery gives us labeled nodes. Now we move to the second layer: Allocation.

Labels alone don't allocate resources. We need to expose GPUs as countable, schedulable resources that Kubernetes understands.

That's where device plugins and scheduling strategies come in.

Timing: 20 seconds
-->
