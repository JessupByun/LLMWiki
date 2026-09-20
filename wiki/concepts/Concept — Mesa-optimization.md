---
type: concept
status: stub
updated: 2026-09-19
summary: "A learned model that is itself running an internal optimization process toward some objective, distinct from the outer objective the training process optimized it for - the outer/inner objective gap this creates is what makes deceptive instrumental alignment possible."
cluster: ["Scheming & AI control"]
sources: []
tags: [mesa-optimization, inner-alignment, deceptive-alignment, safety]
aliases: ["mesa-optimizer", "inner optimizer"]
---

# Concept — Mesa-optimization

## Definition

A base optimizer (e.g. gradient descent during training) searches over a space of models for one that performs well on some outer objective (the training loss or reward).
A mesa-optimizer is a model selected by that search which is itself an optimizer - it runs its own internal search or planning process toward some objective at inference time, rather than implementing a fixed, non-optimizing input-output mapping.
The concern is that a mesa-optimizer's own internal objective (its "mesa-objective") is not the same thing as the outer objective it was selected for; the two only need to correlate well enough on the training distribution for the outer optimizer to keep selecting it, leaving room for them to diverge off-distribution, or once the mesa-optimizer becomes situationally aware enough to distinguish training from deployment.
First proposed by Hubinger et al. 2019 ("Risks from Learned Optimization," not yet ingested in this wiki).

## Relations

- The precondition [[Concept — Deceptive instrumental alignment]] depends on: a model can only appear aligned for instrumental reasons if it is running some internal optimization process with a goal of its own that training pressure doesn't directly see.
- [[Paper — In-context Learning and Induction Heads (2022)]] treats mesa-optimization as a competing mechanistic hypothesis for in-context learning itself (a model implementing a small learning algorithm internally at inference time) and reports no evidence for it in the models studied, while explicitly not ruling it out at larger scale - a narrower, capability-focused use of the term than the safety-focused one above.
- [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] explicitly notes its backdoored models are constructed by hand, not evidence that current models satisfy the internal preconditions for a naturally-arising mesa-objective.
- Cited repeatedly across [[Cluster — Scheming & AI control]]'s member papers as the theoretical grounding neither Hubinger et al. 2019 nor Carlsmith 2023 ("Scheming AIs") - both not yet ingested - has been read directly for in this wiki.

## Up

- [[Cluster — Scheming & AI control]]
