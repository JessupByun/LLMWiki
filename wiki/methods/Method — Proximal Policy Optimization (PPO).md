---
type: method
status: in-progress
updated: 2026-09-02
summary: "Policy-gradient RL algorithm that clips the probability ratio between new and old policy, giving trust-region-like stability without a second-order solve."
cluster: ["Reinforcement learning"]
sources: []
tags: [rl, policy-gradient, ppo]
aliases: ["PPO"]
---

# Method — Proximal Policy Optimization (PPO)

## Definition

A policy-gradient algorithm that alternates between collecting a batch of trajectory data with the current policy and running several epochs of minibatch stochastic gradient ascent on that same batch, using an objective that clips how much credit a single update can take for moving the policy's action probabilities.
Introduced in [[Paper — Proximal Policy Optimization (2017)]].

The clip acts on the ratio `r_t(θ) = π_θ(a_t|s_t) / π_θ_old(a_t|s_t)`: once `r_t` moves outside `[1-ε, 1+ε]` in the direction the advantage estimate already favors, the objective stops rewarding further movement in that update.
This is what makes reusing the same batch of data for multiple gradient steps safe - naive policy gradient objectives reused this way tend to produce destructively large updates.

## Why it works

The clip substitutes for a trust region.
Trust region methods (e.g. TRPO) get the same safety property - don't let one update destroy the policy - by solving a constrained optimization problem with a conjugate-gradient step every iteration.
PPO gets a similar effect for a fraction of the implementation and compute cost, using only first-order optimization, which is what let it become a default choice rather than a specialist tool.

That default-choice status is why it matters for alignment specifically: any procedure that needs to optimize a policy against a learned, scalar reward can plug PPO in as the RL step, and [[Method — Reinforcement learning from human feedback (RLHF)]] does exactly that.

## Relations

- Origin: [[Paper — Proximal Policy Optimization (2017)]].
- The RL algorithm used in step 3 of [[Method — Reinforcement learning from human feedback (RLHF)]], concretely demonstrated at LLM scale in [[Paper — Training LMs to Follow Instructions (2022)]].
- Improves on [[Method — Trust Region Policy Optimization (TRPO)]] (not yet ingested).

## Up

- [[Cluster — Reinforcement learning]]
