---
type: cluster
status: in-progress
updated: 2026-09-19
summary: "Reverse-engineering the internal computations of trained neural networks into human-interpretable algorithms, weights, and features."
cluster: []
sources: []
tags: [interpretability, mechanistic-interpretability, transformers, safety]
aliases: ["mech interp"]
---

# Cluster — Mechanistic interpretability

Attempting to reverse engineer the detailed computations a trained neural network performs, the way a programmer might reverse engineer a compiled binary into human-readable source code.
The motivating bet, made explicit by the paper that opens this cluster, is that this could eventually let researchers explain a model's current safety problems, catch new ones, and anticipate the failure modes of future models that haven't been built yet - a more systematic alternative to purely behavioral evaluation.

This cluster starts where the field starts for language models: the smallest possible toy transformers, studied until a simple, general mechanism (the induction head) falls out of the math.
Everything downstream - from measuring that mechanism at scale to the polysemanticity/superposition problem it runs straight into once MLP layers are added back in - inherits this cluster's founding move: rewrite the model, without changing it, into a form where its parameters are directly interpretable.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — A Mathematical Framework for Transformer Circuits (2021)]] | Opens mechanistic interpretability for transformer language models: reframes attention as independent, additive read/write operations on a shared residual stream, and discovers the induction head, the first general in-context-learning circuit found in an actual transformer. |

## Methods and concepts

- [[Concept — Induction heads]]
- [[Concept — Residual stream]]

## Open threads

This cluster is one paper old, so nearly everything is open.
Directly queued next in the backlog: Olsson et al.'s sequel paper measuring how much of in-context learning in much larger models induction heads actually explain, then Elhage et al.'s *Toy Models of Superposition* (2022) - this paper's own admitted blind spot, since MLP-layer neurons resist the same style of analysis - then the dictionary-learning line (*Towards Monosemanticity*, *Scaling Monosemanticity*) that tries to extract interpretable features from that superposition.

## Up

Top-level cluster; no parent.
