---
layout: default
---

# Debugging GPU Scheduling Issues

**Pod stuck pending:**
- Check: `kubectl describe pod` events
- Verify: Device plugin running
- Confirm: Node has GPU capacity

**GPU not visible in container:**
- Check: NVIDIA Container Toolkit installed
- Verify: `CUDA_VISIBLE_DEVICES` env var
- Test: `nvidia-smi` in container

**Unexpected placement:**
- Review: Labels and selectors
- Check: Taints and tolerations

<!--
Common issues and solutions:

Pod pending: kubectl describe pod, look for "Insufficient nvidia.com/gpu". Verify device plugin running, check node capacity.

GPU not visible: Container toolkit provides runtime hooks. Verify CUDA_VISIBLE_DEVICES set by kubelet. Test: kubectl exec -- nvidia-smi should show GPU.

Unexpected placement: Check nodeSelector/affinity syntax. Verify taints match tolerations. Review kubectl get events.

Timing: 120 seconds
-->
