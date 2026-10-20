---
type: cluster
status: in-progress
updated: 2026-10-19
summary: "Getting reliable training signal for a system whose true objective is too expensive, or too far beyond human capability, to evaluate directly - the specific question of whether a supervisor can still meaningfully oversee a model that is, or will become, more capable than the supervisor itself."
cluster: []
sources: []
tags: [alignment, scalable-oversight, safety]
aliases: []
---

# Cluster — Scalable oversight

Split out from [[Cluster — Post-training alignment]]: that cluster is about shaping the model you already have - RLHF, DPO, preference learning - while this one asks a genuinely different question, first named and framed as [[Concept — Scalable oversight]] by [[Paper — Concrete Problems in AI Safety (2016)]]: can a supervisor meaningfully oversee a model that is, or will become, more capable than the supervisor itself?

**Three strategies.** The cluster's papers bet on different ways around the overseer-capability gap.
*Adversarial argument*: [[Paper — AI Safety via Debate (2018)]] has two agents debate in front of a judge, betting that lying is harder than refuting a lie, with a complexity-theoretic argument that a fixed-capability judge could in principle oversee agents of unbounded reasoning power.
*Decomposition*: [[Paper — Supervising Strong Learners by Amplifying Weak Experts (2018)]] has a human break hard questions into subquestions answered by copies of the agent, and trains the agent to imitate that team - the debate paper shows both reach the same idealized ceiling.
*Elicitation*: [[Paper — Weak-to-Strong Generalization (2023)]] asks whether a strong model generalizes beyond a flawed weak supervisor, eliciting knowledge it already has rather than relying on any adversarial or recursive structure.

**Making it testable.** None of this can be checked directly without superhuman models, so the empirical work here builds proxies.
[[Paper — Measuring Progress on Scalable Oversight for Large Language Models (2022)]] introduces *sandwiching*: tasks where a model beats non-experts but not experts, with non-experts trying to get reliable answers out of it and experts grading at the end.
Weak-to-strong generalization swaps the humans for a weak model.
[[Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)]] runs debate on QuALITY with judges denied the story entirely, an information-asymmetric variant of that setup, and finds the effect debate needs: making debaters more persuasive - judged only by whether they convince the judge - makes judges *more* accurate, while a single persuasive advisor makes them less accurate.

All of this assumes the model being overseen is not actively trying to deceive its overseer; that worst case is the subject of [[Cluster — Scheming & AI control]].

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — AI Safety via Debate (2018)]] | Adversarial argument: two agents debate in front of a judge who only decides who's more convincing, and optimal-play debate with an idealized polynomial-time judge can decide any question in PSPACE, versus only NP for direct judging without debate. |
| [[Paper — Supervising Strong Learners by Amplifying Weak Experts (2018)]] (Iterated Amplification) | Decomposition: a human answers hard questions by delegating subquestions to copies of the agent, and the agent learns to imitate that team, so the training signal grows with the agent; learns five algorithmic tasks nearly as fast as supervised learning. |
| [[Paper — Measuring Progress on Scalable Oversight for Large Language Models (2022)]] (sandwiching) | The experimental paradigm for testing oversight with today's models: non-experts get reliable answers out of a model that beats them, graded by experts; plain chat already lifts non-experts by up to 36 points. |
| [[Paper — Weak-to-Strong Generalization (2023)]] | Elicitation: strong pretrained models finetuned on much weaker models' labels consistently outperform their supervisors, turning the superalignment problem into a proxy that runs today. |
| [[Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)]] | Encouraging positive evidence for debate with LLM debaters: non-expert judges reach 76% (LLM) and 88% (human) accuracy, and more persuasive debaters make judges more accurate - the scaling property debate needs. |

## Methods and concepts

- [[Concept — Scalable oversight]]
- [[Concept — Weak-to-strong generalization]]
- [[Method — Sandwiching]]

## Open threads

The empirical debate result relies on verifiable evidence (a quote tool) and honest RLHF-trained debaters; whether debate holds up without verifiable evidence, or against a deceptive debater, is untested here - the second question is where this cluster meets [[Cluster — Scheming & AI control]].
Amplification has no empirical test beyond toy algorithmic tasks in this wiki, and recursive reward modeling and market-making, both named by the sandwiching paper, have no pages.
Weak-to-Strong Generalization's explicit "no deceptive alignment in base models" assumption is the same gap: average-case elicitation here versus worst-case adversarial robustness there.

## Up

Top-level cluster; no parent.
