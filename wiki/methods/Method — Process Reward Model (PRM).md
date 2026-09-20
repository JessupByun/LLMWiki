---
type: method
status: mature
updated: 2026-09-18
summary: "Train a reward model to predict the correctness of each individual step in a multi-step solution, rather than only the final answer, and score a full solution as the product of its per-step correctness probabilities."
cluster: ["Post-training alignment"]
sources: []
tags: [reward-modeling, process-supervision, scalable-oversight, reasoning]
aliases: ["PRM", "process reward model", "process supervision"]
---

# Method — Process Reward Model (PRM)

## Definition

Collect labels for the correctness of each individual step in a model-generated multi-step solution (positive, negative, or neutral), rather than a single label for the solution as a whole.
Train the reward model as a per-step classifier: given a solution prefix ending right after a given step, predict the probability that step is correct.
Score a full candidate solution as the product of its per-step correctness probabilities, so a single bad step anywhere in the chain sharply lowers the whole solution's score.

## Why it works

A reward model trained only on final-answer correctness faces a hard credit-assignment problem - it must infer, from a single bit of feedback per solution, which of many steps actually caused a failure, and on hard problems that signal is weak since almost every sampled attempt contains an error somewhere.
Step-level labels give the reward model a direct, localized signal instead, both which steps are correct and precisely where a solution goes wrong, which makes credit assignment far easier and produces a more reliable reward model for searching over many candidate solutions.
It is also more directly interpretable and alignment-relevant than outcome supervision: it rewards a reasoning process a human can inspect and endorse, rather than relying on a final answer as an imperfect proxy that can be reached via invalid reasoning.

## Relations

- Origin: [[Paper — Let's Verify Step by Step (2023)]] (Lightman et al.), which also releases PRM800K, a public dataset of 800K step-level human correctness labels.
- Directly contrasted with outcome-supervised reward models (ORMs), the standard reward-modeling approach used in [[Method — Reinforcement learning from human feedback (RLHF)]] pipelines such as [[Paper — Training LMs to Follow Instructions (2022)]].
- Scores solutions generated with [[Method — Chain-of-Thought Prompting]], from [[Paper — Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022)]].
- Directly relevant to [[Concept — Reward hacking]]: a structural mitigation for reward models being gamed via an easily-measured but imperfect proxy.

## Up

- [[Cluster — Post-training alignment]]
