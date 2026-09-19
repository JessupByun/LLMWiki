---
type: question
status: in-progress
updated: 2026-09-19
summary: "Whether a model doing well in-context is learning a new skill at inference time, or locating and running an already-learned behavior triggered by the prompt."
cluster: []
sources: []
tags: [in-context-learning, interpretability, mesa-optimization]
aliases: []
---

# Question — Is in-context learning genuine learning or pattern recognition?

## The question

[[Paper — Language Models are Few-Shot Learners (2020)]] is explicitly agnostic about this, describing in-context learning along a spectrum from recognizing a familiar task in an unfamiliar format to "learning a skill entirely de novo" - and notes the answer may vary from task to task.
Its own strongest evidence for anything past pure recognition is synthetic word-unscrambling: performance is near zero without in-context examples on tasks unlikely to appear during pretraining, and rises sharply with more examples.

## Evidence toward "locating an existing behavior"

[[Paper — In-context Learning and Induction Heads (2022)]] gives the most direct mechanistic evidence available so far, and it points toward the recognition/location end of the spectrum, at least for a well-defined slice of in-context learning.
It identifies a single, fully specified circuit - [[Concept — Induction heads]] - that forms during a sharp, universal training-time transition and, by ablation, accounts for nearly all measured in-context learning in small models.
The mechanism itself is a fixed lookup-and-copy algorithm (search the context for the last occurrence of the current token, predict what followed), not something that adapts its own weights or bootstraps a genuinely novel computation per prompt.
The same paper explicitly checked for evidence of mesa-optimization - a hypothesized internal optimizer that would look more like "genuine learning" - and found none in the models studied.

## What's still open

The induction-head mechanism explains literal and near-literal pattern copying, including some abstract analogical cases like translation, but the two papers use different notions of "in-context learning": Olsson et al. measure loss reduction over context position, Brown et al. measure task-level few-shot performance.
It remains unresolved whether harder few-shot tasks (arithmetic, novel synthetic tasks like word unscrambling) run on the same induction-head machinery, a more elaborate version of it, or a mechanism not yet identified.

## Up

- [[Concept — In-context learning]]
- [[Concept — Induction heads]]
