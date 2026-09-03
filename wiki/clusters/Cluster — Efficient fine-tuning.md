---
type: cluster
status: in-progress
updated: 2026-09-03
summary: "Cheap ways to adapt a large pretrained model to a downstream task - parameter-efficient fine-tuning and quantization, orthogonal to what the fine-tuning is aiming for."
cluster: []
sources: []
tags: [peft, fine-tuning, efficiency, quantization, low-rank]
aliases: ["PEFT"]
---

# Cluster — Efficient fine-tuning

How to specialize a large pretrained model to a task or a user without paying full fine-tuning's cost in memory, storage, or compute.

This is deliberately orthogonal to [[Cluster — Post-training alignment]], the same way [[Cluster — Reinforcement learning]] is.
Post-training alignment owns *what a model should be fine-tuned toward* - preferences, harmlessness, instruction-following.
This cluster owns *how to fine-tune it there cheaply* - the technique is agnostic to the objective, whether that's supervised instruction-tuning, RLHF, or a narrow downstream task.
The two clusters intersect constantly in practice - LoRA and QLoRA are both routinely used as the actual mechanism underneath instruction-tuning and RLHF pipelines - but the papers here earn their place on efficiency merits, not alignment merits.

Not one of the north-star's named pillars, so per the standing rule this cluster only exists because two papers on the same theme were ingested together; a single paper here would have stayed in the canon or floated clusterless until a second one arrived.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — LoRA (2021)]] | Freeze the pretrained weights and train only a low-rank decomposition of the weight update, cutting GPT-3's trainable parameters 10,000x with zero added inference latency. |
| [[Paper — QLoRA (2023)]] | Backpropagate through a frozen 4-bit quantized base model into full-precision LoRA adapters, cutting 65B fine-tuning memory from 780GB to under 48GB with no performance loss. |

## Methods and concepts

- Low-rank adaptation (the `W0 + BA` reparameterization) - not yet split into its own Method page; currently documented on [[Paper — LoRA (2021)]] directly.
- 4-bit NormalFloat quantization - currently documented on [[Paper — QLoRA (2023)]] directly.

## Open threads

Papers that would fill obvious gaps here: the original adapter-layers paper (Houlsby et al. 2019) and prefix-tuning (Li & Liang 2021), both benchmarked directly against by LoRA but not yet ingested; a later LoRA variant addressing its batching/multi-task limitation would also be a natural next member.

## Up

Top-level cluster; no parent.
