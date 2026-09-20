---
type: method
status: in-progress
updated: 2026-09-19
summary: "A PPO variant that drops the learned value network, instead estimating each sampled response's advantage from its normalized reward relative to a group of other responses sampled for the same question."
cluster: ["Reinforcement learning"]
sources: []
tags: [rl, grpo, ppo, policy-gradient]
aliases: ["GRPO"]
---

# Method — Group Relative Policy Optimization (GRPO)

## Definition

A policy-gradient RL algorithm that, for each question, samples a group of G responses from the current policy, scores each with a reward model or rule-based checker, and sets each response's advantage to its reward normalized against the group's own mean and standard deviation: `A_i = (r_i - mean(r)) / std(r)`.
No separate value network is trained; the group itself supplies the baseline that a value function would otherwise estimate.
Introduced in [[Paper — DeepSeekMath - Pushing the Limits of Mathematical Reasoning in Open Language Models (2024)]].

Two variants: **outcome supervision** assigns the same group-normalized reward to every token in a response, while **process supervision** uses a step-level reward model and sums normalized per-step rewards forward from each token's position, giving a finer-grained advantage signal for long multi-step reasoning.

## Why it works

[[Method — Proximal Policy Optimization (PPO)]] needs a learned value network purely to produce a low-variance baseline for its advantage estimate - a network roughly the size of the policy itself, adding substantial memory and compute cost, and one that is awkward to train accurately when (as in most LLM RL settings) only the final token of a response ever receives a real reward.
GRPO sidesteps this by noting that if you already sample several responses to the same question, their own reward distribution *is* a baseline: a response scoring above the group average gets reinforced, one scoring below gets suppressed, with no value function ever fit.
This also aligns naturally with how reward models are typically trained in the first place - on relative comparisons between multiple outputs to the same prompt - so the advantage computation and the reward signal it consumes share the same comparative structure.

## Relations

- A variant of [[Method — Proximal Policy Optimization (PPO)]]; keeps PPO's clipped surrogate objective but replaces GAE-based advantage estimation with group-relative reward normalization, and moves the KL penalty against the reference policy directly into the loss rather than into the per-token reward.
- Introduced in [[Paper — DeepSeekMath - Pushing the Limits of Mathematical Reasoning in Open Language Models (2024)]], which found it improves both in-domain (GSM8K, MATH) and out-of-domain mathematical benchmarks over the same model's supervised fine-tuned checkpoint.
- Became the RL algorithm behind DeepSeek-R1's reasoning training, making this method the direct technical ancestor of the RL-on-verifiable-rewards paradigm that followed.

## Up

- [[Cluster — Reinforcement learning]]
