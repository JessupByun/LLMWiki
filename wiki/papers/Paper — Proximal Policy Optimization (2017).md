---
type: paper
status: in-progress
updated: 2026-09-02
summary: "PPO - a clipped policy-gradient objective that gets trust-region-level stability with only first-order optimization, and became the RL algorithm RLHF pipelines actually run."
cluster: ["Reinforcement learning", "Foundational canon"]
sources: ["raw/papers/Paper — Proximal Policy Optimization (2017).pdf"]
tags: [rl, policy-gradient, ppo, optimization]
aliases: ["arxiv:1707.06347", "Schulman et al. 2017", "PPO"]
---

# Paper — Proximal Policy Optimization (2017)

Commonly known as **PPO**.

## Metadata

- **arXiv:** 1707.06347 (v2, 2017-08-28)
- **Authors:** John Schulman, Filip Wolski, Prafulla Dhariwal, Alec Radford, Oleg Klimov (OpenAI)

## Impact

PPO became the default policy-gradient algorithm for the next several years of deep RL, and it is specifically the algorithm that turns "fit a reward model to human preferences" into an actual trained policy in modern RLHF pipelines.
[[Paper — Training LMs to Follow Instructions (2022)]] runs PPO, by name, as the third stage of the InstructGPT recipe.
Its appeal is not raw performance so much as being simple enough that everyone actually used it: a few lines of code change to a vanilla policy-gradient implementation, compatible with shared policy/value architectures and techniques like dropout that its main competitor could not handle.

## Problem it solved

Three existing options each had a disqualifying flaw for the kind of general-purpose, large-scale RL people wanted to run.
Q-learning with function approximation "fails on many simple problems" and is poorly understood.
Vanilla policy gradient has poor sample efficiency and robustness, because performing multiple optimization steps on the same batch of data using the naive objective is "not well-justified" and "often leads to destructively large policy updates."
Trust region policy optimization (TRPO) fixes that instability by constraining each update's KL divergence from the old policy, but the fix is expensive: it needs a conjugate-gradient solve with a linear approximation to the objective and a quadratic approximation to the constraint, and it is "not compatible with architectures that include noise (such as dropout) or parameter sharing" between the policy and value function.
The paper's goal was TRPO's stability and data efficiency, using only first-order optimization.

## Core idea: clip the probability ratio

Define the probability ratio `r_t(θ) = π_θ(a_t|s_t) / π_θ_old(a_t|s_t)`, so `r = 1` exactly at the old policy.
TRPO's surrogate objective is `L^CPI(θ) = E_t[r_t(θ) · A_t]`, where `A_t` is the advantage estimate; maximizing this without a constraint pushes `r` arbitrarily far from 1 and destroys the policy.

PPO's fix: clip the ratio to the interval `[1-ε, 1+ε]` (ε = 0.2 in the paper), then take the **minimum** of the clipped and unclipped objective:

> L^CLIP(θ) = E_t[ min( r_t(θ)·A_t, clip(r_t(θ), 1-ε, 1+ε)·A_t ) ]

Concretely: when the advantage is positive, the clip caps how much credit the objective gives for pushing `r` above `1+ε` - once a good action's probability has already increased enough, there is no more incentive to push it further in this update.
When the advantage is negative, the same clip caps how much the objective can push `r` below `1-ε`.
Taking the min means the clip only ever removes an *upside* the unclipped objective would have offered; it never adds an extra penalty, so `L^CLIP` is a pessimistic (lower) bound on the true surrogate.
The result: large beneficial-looking updates get capped rather than trusted outright, without a KL constraint or second-order solve anywhere in the loop.

An adaptive KL-penalty variant is also provided as a baseline, but the paper reports it performs worse than clipping (Table 1: 0.82 average normalized score for `ε=0.2` clipping vs 0.71-0.74 for the KL variants).

## How it's built

- **Actor-critic style rollout.** N parallel actors each run the current policy for a fixed horizon T, producing NT timesteps of data; the surrogate loss is built on that batch and optimized with K epochs of minibatch SGD (Adam) before the policy is updated for the next iteration.
- **Advantage estimation.** A truncated version of generalized advantage estimation (GAE), which reduces to a simple k-step return-minus-baseline estimator when its λ parameter is 1.
- **Combined objective.** When policy and value function share parameters, the loss adds a value-function squared-error term and an entropy bonus to the clipped policy loss: `L^{CLIP+VF+S}`.

## Results (highlights)

- **Continuous control (MuJoCo, 7 tasks):** clipping with ε=0.2 scored 0.82 (normalized), beating no-clipping-or-penalty (-0.39, actually worse than a random policy on one task) and every KL-penalty variant tried.
- **Beats prior methods head-to-head:** outperforms TRPO, vanilla policy gradient, and the cross-entropy method on nearly all 7 MuJoCo environments tested.
- **Atari (49 games):** won on average-reward-over-training 30/49 times vs. A2C's 1 and ACER's 18, while being "much simpler" to implement than ACER.
- **Scales to high-dimensional continuous control:** a 3D humanoid learns to run, steer around a randomly-relocated target, and recover from being pelted with cubes.

## Why it endures

PPO's staying power is less about beating every RL algorithm invented after it and more about being the thing people could actually deploy reliably at scale - OpenAI Five's Dota 2 system and countless robotics results ran on it, and it became the unmarked default RL step wherever a learned reward needs to be optimized.
That last use case is what made it load-bearing for alignment specifically: swap trajectory-clip comparisons for pairs of LLM responses, and Christiano et al.'s [[Paper — Deep RL from Human Preferences (2017)]] loop plus PPO is recognizably the RLHF stage of every instruction-tuned model since.

## Limitations

- The clip is a heuristic, not a guarantee - unlike TRPO's constraint, nothing in the objective bounds how far a single update can actually move the policy in the worst case.
- Performance is sensitive to the clip parameter ε and to advantage-estimator choices (GAE's λ).
- The adaptive-KL alternative, included as an important baseline, is empirically dominated by clipping in the paper's own experiments.

## Relations

- Improves on [[Method — Trust Region Policy Optimization (TRPO)]] (not yet ingested) by replacing its hard KL constraint and conjugate-gradient solve with a clipped first-order objective.
- Method hub: [[Method — Proximal Policy Optimization (PPO)]].
- The RL algorithm inside [[Method — Reinforcement learning from human feedback (RLHF)]]'s third stage, as used explicitly in [[Paper — Training LMs to Follow Instructions (2022)]].
- [[Paper — Deep RL from Human Preferences (2017)]] already names PPO as A2C/TRPO's successor in its own "why it endures" discussion, despite predating this paper's wide adoption.

## Up

- [[Cluster — Reinforcement learning]]
- [[Cluster — Foundational canon]]
