---
type: method
status: in-progress
updated: 2026-10-20
summary: "Changing a model's behavior by adding, subtracting, or projecting out a direction in its internal activations - usually one found by contrasting activations on examples with and without a behavior - rather than by changing its prompt or retraining it."
cluster: ["Mechanistic interpretability"]
sources: []
tags: [interpretability, activation-steering, representation-engineering, control]
aliases: ["activation addition", "steering vectors", "directional ablation", "representation control"]
---

# Method — Activation steering

## Definition

An intervention on a model's residual stream (see [[Concept — Residual stream]]) during the forward pass: add a vector to push behavior toward a concept, subtract it to push away, or project it out entirely so the model can't represent it.
The vector usually comes from contrasting activations on examples with and without a behavior - **difference-in-means** (the average of one group minus the other), or the top principal component of paired differences - though it can also be a single interpretable feature from a sparse autoencoder.

## Why it works

It relies on the observation that many high-level concepts are represented roughly linearly, as directions in activation space.
If a behavior is driven by how far activations point along one direction, then moving them along it changes the behavior, and removing it disables the behavior.
A contrastive direction can often serve as a monitor too, by projecting activations onto it - persona vectors use one vector for both - though the direction that best predicts a concept is not always the one that best controls it, as Representation Engineering found.

Steering can also be applied during training, not just inference, and a projection-out can be folded permanently into the weights.
Additive steering at large strengths degrades general capabilities, so results are usually reported alongside a capability benchmark; projecting a direction out can be much more surgical.

## Where it appears in this wiki

- [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]] frames reading and controlling concept directions - honesty, harmlessness, power-seeking - as a top-down approach to interpretability.
- [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]] steers with single SAE features, as in the Golden Gate Bridge demonstration.
- [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]] shows refusal can be switched off by ablating one difference-in-means direction, or switched on by adding it, and bakes the ablation into the weights as a jailbreak.
- [[Paper — Persona Vectors - Monitoring and Controlling Character Traits in Language Models (2025)]] automates direction extraction for character traits and introduces preventative steering during finetuning.
- [[Paper — Detecting Strategic Deception Using Linear Probes (2025)]] uses the reading side only, as a deception monitor.

## Relations

- The intervention counterpart to linear probing, and a coarser, supervised alternative to the unsupervised features of [[Method — Sparse Autoencoder (SAE)]].
- Depends on the linear representations discussed under [[Concept — Superposition]], where many features share directions imperfectly.

## Up

- [[Cluster — Mechanistic interpretability]]
