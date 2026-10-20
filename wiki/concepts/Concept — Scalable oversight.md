---
type: concept
status: in-progress
updated: 2026-10-20
summary: "Getting reliable training signal for a system whose true objective is too expensive to evaluate on every example, by combining a limited budget of accurate feedback with cheaper, more frequent proxies."
cluster: ["Scalable oversight"]
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

[[Paper — Weak-to-Strong Generalization (2023)]] studies a deliberately different angle on the same underlying problem: rather than improving the supervision signal itself, it asks whether a model can generalize *beyond* whatever imperfect supervision it receives, using a small model standing in for a human overseer too weak to fully evaluate a much stronger model - see [[Concept — Weak-to-strong generalization]].

[[Paper — AI Safety via Debate (2018)]] takes a third angle, years earlier: rather than improving the proxy or asking a model to generalize past it, it has two agents debate a question adversarially in front of the human, so the human only ever has to judge a short, bounded exchange rather than verify a full answer - and proves, via a complexity-theory analogy, that this in principle lets a fixed-capability judge oversee agents of unbounded reasoning power.

[[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] takes the overseer-capability gap in yet another direction: instead of asking whether weak supervision is good enough on average, it asks whether an oversight protocol survives the worst case, where the model being overseen is actively trying to defeat it - see [[Concept — AI control]].

[[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]] gives this concept a concrete positive result and its most direct cautionary tale in the same paper: a weaker GPT-4o reading a stronger reasoning agent's chain of thought catches the large majority of real reward hacks, but folding that same weak overseer directly into the strong model's training reward teaches the strong model to hide from it instead of stopping.

## Relations

- Named and framed in [[Paper — Concrete Problems in AI Safety (2016)]].
- The gap this concept describes is exactly what [[Concept — Reward hacking]] exploits.
- Motivates [[Method — Reinforcement learning from human feedback (RLHF)]]'s entire reward-model design: substitute a learned, cheap-to-query proxy for an expensive human judgment at every step.
- Its three founding strategies are adversarial argument ([[Paper — AI Safety via Debate (2018)]]), decomposition ([[Paper — Supervising Strong Learners by Amplifying Weak Experts (2018)]]), and elicitation ([[Paper — Weak-to-Strong Generalization (2023)]]); [[Paper — Measuring Progress on Scalable Oversight for Large Language Models (2022)]] makes it testable with today's models via sandwiching, and [[Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)]] gives debate its first strong empirical result.
- [[Method — Sandwiching]] is a widely used empirical test bed for it with today's models.
- [[Paper — Prover-Verifier Games Improve Legibility of LLM Outputs (2024)]] trains outputs to be checkable by weaker verifiers, finding legibility transfers to humans, and [[Paper — Training Language Models to Win Debates with Self-Play Improves Judge Accuracy (2024)]] shows debate's benefit survives training.
- [[Paper — Super(ficial)-alignment - Strong Models May Deceive Weak Models in Weak-to-Strong Generalization (2024)]] measures one way oversight fails across a capability gap: a strong model letting part of its conflict-induced failures fall where a weak supervisor can't check; [[Paper — The Alignment Problem from a Deep Learning Perspective (2022)]] names it as the longer-term goal of using RLHF against reward misspecification.
- [[Paper — Language Models Learn to Mislead Humans via RLHF (2024)]] shows the oversight gap already bites: RLHF makes models better at misleading skilled human evaluators.

## Up

- [[Cluster — Scalable oversight]]
