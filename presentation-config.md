---
topic: GPUs on Kubernetes Unlocked
duration_minutes: 40
target_slide_count: 26
audience: Kubernetes practitioners (conference attendees)
style: Technical/Developer-focused
visuals: Comprehensive (mermaid diagrams + existing images)
slide_timing: 90s per slide (default)
---

# Presentation Configuration

## Timing Breakdown
- **Duration**: 40 minutes (middle of 30-45 range)
- **Slide Count**: ~26 slides at 90 seconds each
- **Buffer**: Built-in for Q&A transitions

## Slide Timing Strategy
- **Title/Intro**: 60s
- **Problem framing**: 90s
- **Technical deep-dives**: 2-3 minutes (2 slides)
- **Quick transitions**: 60s
- **Summary/Conclusion**: 90s

## Visual Assets Available

### From Book Chapter 5 (`images/raw/`)
1. `ch05-gpu-throughput-scaling.png` - Data parallelism with multiple replicas
2. `ch05-gpu-model-parallelism.png` - Tensor vs pipeline parallelism
3. `ch05-gpu-single-node.png` - Single-node multi-GPU architecture
4. `ch05-gpu-multiple-nodes.png` - Multi-node multi-GPU architecture

### User-Provided Diagrams
1. **Three Layers Architecture** - Overview of Discovery → Allocation → Orchestration layers (use early in presentation)

### Mermaid Diagrams to Create
1. Discovery flow (NFD → GFD → Labels → Scheduler)
2. Device plugin registration and allocation flow
3. Scheduling decision tree (resource vs label-based)
4. Time-slicing vs MIG comparison (side-by-side)
5. Deployment pattern decision tree

## Tone Guidelines (from CFP)
- **Practical and systems-oriented**: How pieces fit together
- **Conversational but technical**: "Live or die by GPUs"
- **Action-oriented**: Where to start, what to use when
- **Avoid**: Academic theory, hand-waving, excessive abstraction
- **Emphasize**: Concrete patterns, decision frameworks, production-ready advice
