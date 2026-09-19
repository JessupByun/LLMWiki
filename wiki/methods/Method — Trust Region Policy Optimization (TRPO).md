---
type: method
status: in-progress
updated: 2026-09-18
summary: "Policy-gradient algorithm that maximizes a local surrogate objective subject to a hard constraint on the average KL divergence between the new and old policy, giving a theoretically grounded monotonic-improvement guarantee."
cluster: ["Reinforcement learning"]
sources: []
tags: [rl, policy-gradient, trust-region]
aliases: ["TRPO"]
---

# Method — Trust Region Policy Optimization (TRPO)

## Definition

At each iteration, TRPO improves the current policy by solving a constrained optimization problem: maximize a local (first-order-accurate) approximation of expected return, subject to the average KL divergence between the new policy and the old policy staying below a fixed threshold δ.
Introduced in [[Paper — Trust Region Policy Optimization (2015)]].

The constraint is approximated as a quadratic form using the Fisher information matrix, and the resulting problem is solved approximately: the conjugate gradient algorithm finds a search direction without ever forming the full Fisher matrix, and a line search then finds a step size along that direction that both improves the true (nonlinear) objective and satisfies the true KL constraint.

## Why it works

Bounding the **distributional** distance between the old and new policy - how differently the two policies actually behave - is what makes an update trustworthy, in a way that a fixed step size or a raw parameter-space penalty cannot guarantee.
A policy update that changes parameters only slightly can still change the resulting action distribution drastically in regions the parameterization is sensitive to, and vice versa; constraining KL divergence directly targets the quantity that determines whether the local surrogate objective computed from old-policy trajectory data is still a valid approximation after the update.
This is what lets TRPO take large, confident steps where the approximation holds well and automatically stay cautious where it doesn't, without any manual step-size tuning.

## Relations

- Origin: [[Paper — Trust Region Policy Optimization (2015)]].
- Simplified into a much cheaper first-order approximation by [[Method — Proximal Policy Optimization (PPO)]], which replaces the KL constraint and conjugate-gradient solve with a clipped probability-ratio objective, at the cost of losing TRPO's formal guarantee.

## Up

- [[Cluster — Reinforcement learning]]
