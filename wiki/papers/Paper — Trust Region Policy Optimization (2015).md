---
type: paper
status: in-progress
updated: 2026-09-18
summary: "TRPO - solves a KL-divergence-constrained optimization problem at each update, giving the first policy-gradient method with a theoretically grounded monotonic-improvement guarantee that scales to large neural-network policies."
cluster: ["Reinforcement learning", "Foundational canon"]
sources: ["raw/papers/reinforcement-learning/Paper — Trust Region Policy Optimization (2015).pdf"]
tags: [rl, policy-gradient, trust-region, optimization]
aliases: ["arxiv:1502.05477", "Schulman et al. 2015", "TRPO"]
---

# Paper — Trust Region Policy Optimization (2015)

## Metadata

- **arXiv:** 1502.05477 (v5, 2017-04-20)
- **Venue:** ICML 2015.
- **Authors:** John Schulman, Sergey Levine, Philipp Moritz, Michael Jordan, Pieter Abbeel (UC Berkeley).

## Impact

Gave policy-gradient RL its first practical algorithm with a genuine theoretical guarantee: each update is provably not going to make the policy worse, and the algorithm still scales to large nonlinear policies like neural networks with tens of thousands of parameters - previously a major obstacle for model-free policy search.
TRPO's locomotion and Atari results were, at the time, some of the first demonstrations of learning complex continuous-control gaits and pixel-based game-playing policies from scratch with a single general-purpose method and minimally shaped rewards.
It became the reference baseline every subsequent policy-optimization method was measured against, and its own first-order simplification two years later, [[Paper — Proximal Policy Optimization (2017)]], is now the far more widely used descendant.

## Problem it solved

Before TRPO, none of the existing options for optimizing a large nonlinear policy were fully satisfying.
Vanilla policy gradient methods have poor sample efficiency, and reusing a batch of collected data for multiple gradient steps with the naive objective is "not well-justified" and often produces destructively large policy updates that collapse performance.
Derivative-free methods like the cross-entropy method (CEM) and covariance matrix adaptation (CMA) sidestep gradients entirely but scale poorly as the number of policy parameters grows, since they treat the policy as a black box.
The natural policy gradient offered a principled direction but no guidance on how large a step to take.
TRPO's goal was to close this gap: derive an update rule with a provable monotonic-improvement guarantee, then approximate it into something practical enough to run on real, large-scale problems.

## Core idea

The paper's theoretical starting point is Kakade and Langford's conservative policy iteration bound, which lower-bounds the true performance of a new policy in terms of a cheaper-to-compute local approximation, minus a penalty term.
TRPO's main theoretical contribution extends this bound from restrictive mixture policies to *any* stochastic policy, by replacing the mixture coefficient with a distance measure (total variation divergence, related to KL divergence) between the old and new policy.
This yields a policy-iteration scheme that is mathematically guaranteed to produce a monotonically improving sequence of policies: at each step, maximize the local surrogate objective minus a KL-divergence penalty with a specific coefficient.
In practice, the theoretically correct penalty coefficient is so conservative it forces prohibitively tiny steps, so TRPO's practical algorithm swaps the penalty for a hard constraint instead: maximize the surrogate objective subject to the average KL divergence between old and new policy staying under a fixed threshold δ - a **trust region**, sized in units of "how different is the new policy's behavior," not raw parameter distance.
Concretely, on the locomotion tasks (a simulated swimmer, hopper, and walker), this constraint is what lets TRPO take large, aggressive steps on the easy parts of the policy update while automatically staying cautious wherever the local approximation is less trustworthy - unlike a fixed learning rate, which has no way to tell those two situations apart.
The constrained problem itself is solved approximately: a linear approximation to the objective and a quadratic approximation to the KL constraint (via the Fisher information matrix) give a search direction through the conjugate gradient algorithm, followed by a line search that shrinks the step until both the true nonlinear objective improves and the true KL constraint is satisfied.

## How it's built

- **Sampling schemes:** two ways to estimate the objective and constraint from simulated data - *single path* (sample individual trajectories, usable model-free) and *vine* (branch multiple rollouts from a shared set of states, lower variance but requires a resettable simulator).
- **Search direction via conjugate gradient:** avoids ever forming the full Fisher information matrix by using only matrix-vector products, making the natural-gradient-style step tractable for policies with tens of thousands of parameters.
- **Line search:** starts from the maximal step size the quadratic approximation allows, then shrinks it until the actual (nonlinear) surrogate objective improves and the actual KL constraint holds - without this, the algorithm occasionally computes catastrophic steps.
- **Policy architectures:** a small fully-connected network outputting a Gaussian policy for continuous control (MuJoCo locomotion); a convolutional network over raw pixels with a categorical output for Atari.

## Results (highlights)

- **Locomotion (MuJoCo):** both single-path and vine TRPO learned working swim, hop, and walk gaits from scratch with general-purpose neural network policies and minimally informative rewards - the paper notes no prior method had done this for all three gaits with a single generic method.
- **Beat every baseline tried:** outperformed the natural policy gradient, CEM, and CMA on the harder locomotion tasks, where the gradient-free methods' sample complexity scaled unfavorably with parameter count.
- **Atari from raw pixels:** trained convolutional-network policies with ~33,500 parameters on 7 Atari games using only single-path and vine TRPO, achieving results competitive with contemporary deep Q-learning and Monte-Carlo tree search baselines despite using a single unspecialized method.

## Why it endures

TRPO established that constraining a policy update by *distributional* distance (KL divergence between action distributions), rather than by raw parameter distance or a fixed step size, is the right way to make large-scale policy-gradient optimization stable - the trust-region framing is what directly motivated PPO's much cheaper clipped-objective approximation of the same idea, and TRPO remains the standard theoretical reference point against which new policy-optimization methods explain what they're approximating or improving on.

## Limitations

The practical algorithm's hard KL constraint is a heuristic approximation of the theoretically justified penalty, so the paper's own monotonic-improvement proof does not literally carry over to the algorithm actually run in the experiments.
Each update requires a conjugate-gradient solve and multiple Fisher-vector products, making TRPO substantially more expensive per step than a plain policy-gradient method - the exact cost PPO was designed to cut.
The vine sampling variant, which gives lower-variance advantage estimates, requires a simulator that can be reset to arbitrary states, ruling it out for real-world or non-resettable settings.

## Relations

- Builds on Kakade and Langford's conservative policy iteration bound and the classic natural policy gradient (Kakade 2002), neither ingested here.
- Benchmarked against Atari, the domain [[Paper — Playing Atari with Deep RL (2013)]] (DQN) established as a standard deep-RL testbed.
- Method hub: [[Method — Trust Region Policy Optimization (TRPO)]].
- Simplified into a first-order, clipped-objective approximation by [[Paper — Proximal Policy Optimization (2017)]], which achieves similar update stability without the conjugate-gradient solve.

## Up

- [[Cluster — Reinforcement learning]]
- [[Cluster — Foundational canon]]
