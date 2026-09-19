---
type: concept
status: in-progress
updated: 2026-09-19
summary: "Getting reliable training signal for a system whose true objective is too expensive to evaluate on every example, by combining a limited budget of accurate feedback with cheaper, more frequent proxies."
cluster: ["Post-training alignment"]
sources: []
tags: [alignment, scalable-oversight, safety]
aliases: ["semi-supervised reinforcement learning"]
---

# Concept — Scalable oversight

## Definition

The problem of training a system well when the designer's true objective (e.g. "would a human, given hours to inspect this output, actually approve of it?") is too expensive to evaluate on every training example.
The designer is forced to substitute a cheaper, more frequent proxy signal - and the gap between the cheap proxy and the expensive true objective is exactly where side effects and [[Concept — Reward hacking]] slip through unpenalized.

[[Paper — Concrete Problems in AI Safety (2016)]] names and frames the problem, proposing *semi-supervised reinforcement learning* as one concrete operationalization: an RL agent that sees its true reward on only a small, possibly actively-chosen fraction of timesteps, and must make the most of dense but unlabeled experience the rest of the time.

## Why it matters

Every post-training pipeline in this wiki faces a version of this trade-off.
A human labeling every RLHF comparison is the expensive, accurate signal; a learned reward model is the cheap, frequent proxy trained to approximate it.
The entire liability of [[Method — Reinforcement learning from human feedback (RLHF)]] - a policy that finds and exploits the gap between the proxy and the true objective - is a direct instance of the scalable-oversight problem going unsolved.

Process supervision in [[Paper — Let's Verify Step by Step (2023)]] can be read as a scalable-oversight move in this exact sense: instead of one expensive final-answer judgment, cheaper-to-verify per-step correctness labels are collected at much higher density, producing a more reliable overall signal without a proportional increase in total human labeling cost.

## Relations

- Named and framed in [[Paper — Concrete Problems in AI Safety (2016)]].
- The gap this concept describes is exactly what [[Concept — Reward hacking]] exploits.
- Addressed empirically, in the process-vs-outcome-supervision sense, by [[Paper — Let's Verify Step by Step (2023)]].
- Motivates [[Method — Reinforcement learning from human feedback (RLHF)]]'s entire reward-model design: substitute a learned, cheap-to-query proxy for an expensive human judgment at every step.

## Up

- [[Cluster — Post-training alignment]]
