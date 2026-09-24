---
type: cluster
status: in-progress
updated: 2026-09-24
summary: "Getting reliable training signal for a system whose true objective is too expensive, or too far beyond human capability, to evaluate directly - the specific question of whether a supervisor can still meaningfully oversee a model that is, or will become, more capable than the supervisor itself."
cluster: []
sources: []
tags: [alignment, scalable-oversight, safety]
aliases: []
---

# Cluster — Scalable oversight

Split out from [[Cluster — Post-training alignment]]: that cluster is about shaping the model you already have - RLHF, DPO, preference learning - while this one asks a genuinely different question, first named and framed as [[Concept — Scalable oversight]] by [[Paper — Concrete Problems in AI Safety (2016)]]: can a supervisor meaningfully oversee a model that is, or will become, more capable than the supervisor itself?

Two founding approaches anchor this cluster, arriving from opposite directions within a year of each other.
[[Paper — AI Safety via Debate (2018)]] proposes adversarial decomposition: two agents debate a question in front of a human judge who only has to decide who's more convincing, backed by a complexity-theoretic argument that this in principle lets a fixed-capability judge oversee agents of unbounded reasoning power.
[[Paper — Weak-to-Strong Generalization (2023)]] instead asks whether a model can generalize *beyond* whatever imperfect supervision it's given, replacing the far-future problem of humans overseeing superhuman AI with an empirically testable proxy available today - a small model supervising a much larger one.
The two share a target (the overseer-capability gap) but not a strategy: debate bets that lying is harder than refuting a lie inside an adversarial game, while weak-to-strong generalization bets that a capable model's own latent knowledge can be elicited past a flawed supervisor's mistakes without any adversarial structure at all.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — AI Safety via Debate (2018)]] | Founding approach #1: two agents debate in front of a human judge who only decides who's more convincing, and optimal-play debate with an idealized polynomial-time judge can decide any question in PSPACE, versus only NP for direct judging without debate. |
| [[Paper — Weak-to-Strong Generalization (2023)]] | Founding approach #2: finetunes strong pretrained models on labels from much weaker supervisors and shows the strong models consistently outperform their supervisors, turning the far-future superalignment problem into an empirically testable proxy today. |

## Methods and concepts

- [[Concept — Scalable oversight]]
- [[Concept — Weak-to-strong generalization]]

## Open threads

AI Safety via Debate's own comparison to Christiano's contemporaneous *iterative amplification* proposal (Christiano et al. 2018, not yet ingested) is a natural next addition, and would give this cluster a third founding approach alongside adversarial decomposition (debate) and elicitation (weak-to-strong generalization) - the provably-equivalent-ceiling result Debate's own page describes is specifically against amplification, not against weak-to-strong generalization.
Weak-to-Strong Generalization's explicit "no deceptive alignment in base models" assumption is exactly what [[Cluster — Scheming & AI control]] investigates directly - the two clusters study adjacent halves of the same overseer-capability gap, average-case elicitation here versus worst-case adversarial robustness there.

## Up

Top-level cluster; no parent.
