---
type: method
status: in-progress
updated: 2026-10-20
summary: "Testing scalable-oversight techniques today by picking tasks where a model outperforms non-expert humans but not experts: non-experts try to get reliable answers out of the model using a technique, and experts, who stay out of the loop, grade whether it worked."
cluster: ["Scalable oversight"]
sources: []
tags: [scalable-oversight, sandwiching, evaluation, human-ai-interaction]
aliases: ["sandwiching paradigm", "sandwiching experiment"]
---

# Method — Sandwiching

## Definition

Sandwiching places a model's capability between two groups of humans on some task: better than non-experts, worse than experts.
Non-experts, using an oversight technique, try to align the model - get it to answer reliably - without expert help, while experts measure success afterward.
A technique that lets non-experts reliably supervise a model that knows more than they do is evidence it could let experts supervise models that exceed them, which can't yet be tested directly.
The idea is credited to Cotra and turned into an experimental paradigm by [[Paper — Measuring Progress on Scalable Oversight for Large Language Models (2022)]].

## How it works

The task must have ground truth the experts can check but non-experts can't, such as specialist multiple-choice questions or reading comprehension under time limits or without access to the source text.
The model should be capable but misaligned in the operational sense: it could perform well with a small intervention, yet does poorly when simply asked.
Techniques that deliberately make models argue for wrong answers, like debate, fit naturally, because they create misaligned behavior for the non-experts to overcome even with a well-behaved model.

## Where it appears in this wiki

- [[Paper — Measuring Progress on Scalable Oversight for Large Language Models (2022)]] defines it and runs a proof of concept with a 52B assistant on MMLU and time-limited QuALITY.
- [[Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)]] runs debate on QuALITY with judges who never see the story, an information-asymmetric variant.
- [[Paper — Towards Understanding Sycophancy in Language Models (2023)]] mimics the setting with crowd-workers judging convincing falsehoods without fact-checking tools.
- [[Paper — Weak-to-Strong Generalization (2023)]] uses a setup its authors say resembles sandwiching, swapping the non-expert humans for a weak model and studying generalization rather than oversight techniques.

## Relations

- A widely used empirical test bed for [[Concept — Scalable oversight]] and for proposals like [[Paper — AI Safety via Debate (2018)]] and [[Paper — Supervising Strong Learners by Amplifying Weak Experts (2018)]].
- [[Paper — Training Language Models to Win Debates with Self-Play Improves Judge Accuracy (2024)]] trains debaters against a weak model judge inside the information-asymmetric QuALITY setup, which it calls a variant of sandwiching, with ground truth used only for evaluation.

## Up

- [[Cluster — Scalable oversight]]
