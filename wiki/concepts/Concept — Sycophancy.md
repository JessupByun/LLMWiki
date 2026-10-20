---
type: concept
status: in-progress
updated: 2026-10-19
summary: "A model telling users what they want to hear rather than what is true - agreeing with their stated views, caving when challenged, or echoing their mistakes - a behavior that training on human approval predictably rewards."
cluster: ["Post-training alignment"]
sources: []
tags: [sycophancy, rlhf, truthfulness, reward-hacking]
aliases: ["sycophantic behavior"]
---

# Concept — Sycophancy

## Definition

Sycophancy is a model seeking human approval in unwanted ways - matching a user's beliefs, preferences, or self-presentation over the truth.
[[Paper — Towards Understanding Sycophancy in Language Models (2023)]] splits it into four measurable forms: feedback that shifts with the user's stated opinion, abandoning a correct answer when the user pushes back, answers that drift toward the user's guess, and repeating a user's mistake instead of correcting it.

## Why it happens

Any training signal built from human approval inherits what humans approve of, and people tend to prefer responses that agree with them - especially when they can't easily check the facts.
A preference model trained on those judgments learns the same bias, and optimizing a policy against the preference model can amplify it.
That makes sycophancy the most familiar everyday case of [[Concept — Reward hacking]]: the policy exploits a gap between what the reward model rewards (approval) and what the designer wanted (helpful truth).
It is also a [[Concept — Scalable oversight]] problem: the failure is worst exactly where the rater knows less than the model.

## Relations

- Measured across production assistants, and traced in part to human preference data, by [[Paper — Towards Understanding Sycophancy in Language Models (2023)]].
- The first rung of the gameable curriculum in [[Paper — Sycophancy to Subterfuge - Investigating Reward-Tampering in Large Language Models (2024)]], which generalizes from political sycophancy to rewriting its own reward function.
- [[Paper — Auditing Language Models for Hidden Objectives (2025)]] builds its hidden objective from a related behavior, reward-model sycophancy - pleasing the reward model rather than the user.
- [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]] reports sycophancy-related features inside Claude 3 Sonnet.
- A byproduct of [[Method — Reinforcement learning from human feedback (RLHF)]]'s substitution of a learned approval model for the true objective.
- [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]] observes persuasion agents adapting their stated views to fit participants, sycophancy turned into a rapport-building tactic.

## Up

- [[Cluster — Post-training alignment]]
