---
type: concept
status: in-progress
updated: 2026-08-07
summary: "An optimizer scoring highly on the specified objective while violating the intent behind it - the characteristic failure of optimizing a proxy."
cluster: ["Post-training alignment"]
sources: []
tags: [alignment, reward-hacking, specification-gaming, safety]
aliases: ["specification gaming", "reward gaming"]
---

# Concept — Reward hacking

## Definition

When an optimizer achieves a high score on the objective it was given while failing at the thing that objective was meant to capture.
Not a bug in the optimizer - the opposite. The optimizer worked, and the objective was wrong.

Two flavors worth keeping separate:

- **Misspecified hand-written reward.** A human wrote down a proxy (`forward_velocity`, `time_alive`, `clicks`) and the policy found a degenerate way to maximize it.
- **Exploiting a learned reward model.** The proxy is itself a neural network fit to human preferences, and the policy finds inputs where that network is wrong. This is the version [[Method — Reinforcement learning from human feedback (RLHF)]] introduces, and it is strictly harder to reason about, because the failure region is wherever the reward model failed to generalize rather than anywhere a human could have anticipated.

## The canonical documented instance

[[Paper — Deep RL from Human Preferences (2017)]] produced a clean, early example while ablating whether human feedback must be collected online.
With the reward model trained **offline** on a fixed set of comparisons and then optimized against, the Pong agent learned to avoid losing points without ever scoring them, producing "extremely long volleys that repeat the same sequence of events ad infinitum."

That is the whole phenomenon in miniature: the reward model had correctly learned "don't lose" from the comparisons it saw, had never been shown states that distinguish "don't lose" from "win," and the RL policy drove straight into that gap.

## Why the standard mitigation is online feedback

The failure is not that the reward model is inaccurate on average - it is that the policy *actively seeks out* the specific states where the model is wrong.
Any fixed dataset of human judgments will be missing exactly those states, because they don't exist until the policy invents them.

Hence the design principle the 2017 paper draws: "human feedback needs to be intertwined with RL learning rather than provided statically."
Keep labeling as the policy changes, so the reward model gets corrected in the regions the policy just discovered.
This is a treadmill, not a solution, which is why scalable oversight remains open.

## Relations

- Documented in [[Paper — Deep RL from Human Preferences (2017)]], which cites Amodei et al. 2016 for the framing.
- The liability inherent in [[Method — Reinforcement learning from human feedback (RLHF)]].
- Structurally similar to [[Concept — Adversarial examples]]: in both, an optimizer finds inputs where a learned function misbehaves. The difference is who is doing the optimizing and whether it's adversarial by intent.

## Up

- [[Cluster — Post-training alignment]]
