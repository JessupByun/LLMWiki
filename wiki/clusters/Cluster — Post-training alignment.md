---
type: cluster
status: in-progress
updated: 2026-08-07
summary: "Turning a pretrained model into one that does what humans want - preference learning, reward modeling, RLHF and its successors."
cluster: []
sources: []
tags: [alignment, rlhf, post-training]
aliases: []
---

# Cluster — Post-training alignment

Everything that happens *after* pretraining to make a model behave as intended: preference collection, reward modeling, RL against a learned objective, and the failure modes that follow.

The organizing insight of this cluster, and the reason it exists as a pillar rather than a footnote to RL, is the move [[Paper — Deep RL from Human Preferences (2017)]] made: don't use humans as the reward signal, use them to train a *model* of the reward signal, then optimize that.
Everything downstream inherits both the leverage and the liability of that substitution - the leverage is a ~1000x reduction in human oversight cost, the liability is that you are now optimizing a proxy.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — Deep RL from Human Preferences (2017)]] | Origin of RLHF - learn a reward model from human comparisons of short clips, then optimize it, cutting oversight cost by ~3 orders of magnitude. |

## Methods and concepts

- [[Method — Reinforcement learning from human feedback (RLHF)]]
- [[Concept — Bradley-Terry preference model]]
- [[Concept — Reward hacking]]

## Open threads

Papers that would fill obvious gaps here: PPO (the algorithm RLHF actually runs on in practice), InstructGPT (RLHF at LLM scale), Constitutional AI (AI feedback replacing human feedback), DPO (removing the RL step entirely).

## Up

Top-level cluster; no parent.
