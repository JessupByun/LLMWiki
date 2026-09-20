---
type: method
status: in-progress
updated: 2026-09-19
summary: "Train a policy with RL against a reward that is computed by an automatic, deterministic checker (a matched final answer, a passing test suite) rather than a learned reward model, so there is no learned function for the policy to reward-hack."
cluster: ["Reinforcement learning"]
sources: []
tags: [rl, reward-design, reasoning, post-training]
aliases: ["RLVR", "RL from verifiable rewards", "reinforcement learning with verifiable rewards"]
---

# Method — RL from Verifiable Rewards (RLVR)

## Definition

An RL training regime where the reward signal comes entirely from a rule-based verifier - does the final answer match a known ground truth, does the generated code pass a test suite, does the output satisfy a checkable format constraint - rather than from a neural network trained to predict human or model preferences.
Because the verifier is a fixed, deterministic program rather than a learned function, it cannot itself be reward-hacked: there is no approximation error between the verifier and "true" correctness for the policy to exploit, since the verifier's judgment *is* the definition of correctness for that task.
This restricts RLVR to domains where correctness is checkable at all (math, code, formal logic) - it has no direct equivalent for open-ended tasks like writing, where no automatic checker for "good" exists.

## Why it works

Reward hacking under RL is fundamentally a mismatch problem: a learned reward model is only a proxy for what it was trained to approximate, and enough optimization pressure against any proxy tends to find the gap between the proxy and the true objective, as [[Concept — Reward hacking]] and [[Paper — Scaling Laws for Reward Model Overoptimization (2022)]] document directly for preference-based reward models.
RLVR sidesteps this by removing the proxy: if the reward *is* an automatic check of the actual property being optimized for (a correct final answer), there is no gap left to exploit, at least on the specific outcome the verifier checks.
The tradeoff is that the checker only judges the final output, saying nothing about the reasoning process that produced it - which is exactly what makes it possible to apply RLVR with no supervised reasoning demonstrations at all, since the policy is free to discover whatever intermediate process actually gets it to a correct output.

## Relations

- The training regime run by [[Paper — DeepSeek-R1 - Incentivizing Reasoning Capability in LLMs via Reinforcement Learning (2025)]], which popularized the name and demonstrated it at scale with no SFT initialization, using [[Method — Group Relative Policy Optimization (GRPO)]] as the underlying optimizer.
- Contrasts directly with process- and outcome-based neural reward models (as used in [[Paper — Let's Verify Step by Step (2023)]] and DeepSeekMath's own process-supervision variant of GRPO) - RLVR is the choice to use no learned reward model for the reasoning-RL stage at all, explicitly to avoid the reward hacking those neural reward models remain vulnerable to under large-scale RL.
- Shares its "reward from an automatic checker" structure with the coding-competition and math-answer rewards in [[Paper — DeepSeekMath - Pushing the Limits of Mathematical Reasoning in Open Language Models (2024)]], though that paper does not use the RLVR name and still relies on process supervision as its best-performing variant.

## Up

- [[Cluster — Reinforcement learning]]
