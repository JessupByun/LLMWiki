---
type: method
status: in-progress
updated: 2026-09-19
summary: "A one-hidden-layer, overcomplete autoencoder trained with an L1 sparsity penalty to decompose a model's activations into a larger set of sparsely-activating, more interpretable features."
cluster: ["Mechanistic interpretability"]
sources: []
tags: [interpretability, mechanistic-interpretability, superposition, dictionary-learning]
aliases: ["SAE", "sparse autoencoder"]
---

# Method — Sparse Autoencoder (SAE)

## Definition

A sparse autoencoder is a simple, one-hidden-layer autoencoder used as a lightweight form of dictionary learning: a ReLU encoder projects an activation vector up into a much larger, **overcomplete** hidden layer (more hidden units than input dimensions), and a linear decoder reconstructs the original activation from that hidden layer.
It is trained to minimize reconstruction error (mean squared error) plus an L1 penalty on the hidden layer's activations, which pushes most hidden units to be zero for any given input - so each input activates only a small, sparse subset of the (large) hidden layer.

Each hidden unit is interpreted as a candidate **feature**: a direction in the original activation space (given by its column of the decoder weights) that the sparsity penalty encourages to correspond to one specific, sparsely-occurring concept, rather than the tangled mixture of concepts a single raw neuron often represents.

## Why it works

An SAE is applied not to a model's inputs or outputs but to its internal activations (e.g. an MLP layer's output), on the hypothesis that those activations are a [[Concept — Superposition]] of more "true features" than there are neurons to hold them.
Recovering that overcomplete set exactly is the NP-hard problem of compressed sensing in general, but an SAE only needs to find a workable approximation, and its architecture (a linear projection, a nonlinearity, and another linear projection) closely mirrors the MLP layer it's decoding - a deliberate design choice, on the reasoning that the SAE should be no more powerful at recovering hidden structure than the network being studied could plausibly be using.

Two departures from a standard autoencoder turn out to matter in practice: **untying** the encoder and decoder weights (rather than sharing one matrix for both), since features with very similar "true" directions still need distinguishable encoder projections to avoid being confused with each other on the way in, even though their decoder ("dictionary") directions can stay nearly identical; and periodically **resampling dead hidden units** - reinitializing units that have stopped firing to instead fit whichever training examples the autoencoder currently reconstructs worst, which recovers more usable features than leaving dead units in place.

## Relations

- Introduced and validated by [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]] as "Approach 2" from [[Paper — Toy Models of Superposition (2022)]]: finding an overcomplete basis that describes a model already exhibiting superposition, rather than trying to train superposition away.
- The features it recovers are validated by causal ablation and by "pinned" activation clamping to steer generation, connecting it to ordinary circuit-analysis techniques.
- Directly scaled up in [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]], from a one-layer toy transformer to a real production model.

## Up

- [[Cluster — Mechanistic interpretability]]
