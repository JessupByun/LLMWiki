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
| [[Paper — In-context Learning and Induction Heads (2022)]] | Extends induction heads from a toy-model curiosity to a candidate explanation for most in-context learning in transformers of any size, via a purely behavioral definition, causal ablations, and the discovery of a universal early-training "phase change." |
| [[Paper — Toy Models of Superposition (2022)]] | Demonstrates, in a fully understood toy model, that neural networks represent more features than they have neurons by packing them into almost-orthogonal directions - resolving the first paper's admitted blind spot around MLP-layer polysemanticity and setting the dictionary-learning research agenda this cluster pursues next. |
| [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]] | Trains a sparse autoencoder on a one-layer transformer's MLP activations and extracts thousands of far-more-interpretable-than-neurons features, giving the first compelling proof that dictionary learning can recover real structure from superposition. |
| [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]] | Scales the same sparse-autoencoder technique from a one-layer toy model up to a real production model (Claude 3 Sonnet), recovering millions of abstract features and giving the first causal steering evidence (the Golden Gate Bridge feature) that a recovered feature actually drives model behavior. |

## Methods and concepts

- [[Concept — Induction heads]]
- [[Concept — Residual stream]]
- [[Concept — Superposition]]
- [[Method — Sparse Autoencoder (SAE)]]

## Open threads

Scaling Monosemanticity has now closed this cluster's most pressing open question - dictionary learning does scale past a one-layer toy model, at least as far as Claude 3 Sonnet - though it opens a new one in its place: dictionary completeness remains far off even at 34M features, so a still-larger-scale follow-up would be a natural next addition if the reading queue ever reopens.
[[Question — Is in-context learning genuine learning or pattern recognition]] tracks the open thread between this cluster's induction-head evidence and [[Concept — In-context learning]]'s behavioral framing in [[Cluster — Foundational canon]].

## Up

Top-level cluster; no parent.
