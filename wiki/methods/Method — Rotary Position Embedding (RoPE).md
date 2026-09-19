---
type: method
status: mature
updated: 2026-09-18
summary: "Rotate each query/key vector by an angle proportional to its token position, so the attention dot product between two tokens depends only on their relative distance."
cluster: ["Foundational canon"]
sources: []
tags: [positional-encoding, architecture]
aliases: ["RoPE", "Rotary Position Embedding"]
---

# Method — Rotary Position Embedding (RoPE)

## Definition

Split each query or key vector into pairs of coordinates, and rotate the pair belonging to token position m by an angle m*theta_i, where theta_i is a fixed, geometrically decreasing frequency assigned to that pair.
The dot product of a query rotated by m*theta and a key rotated by n*theta depends only on the angle difference (m-n)*theta, so relative position between any two tokens emerges from rotating each one by its own absolute position, with no separate relative-position term added anywhere.

## Why it works

Because rotation preserves vector length, applying it to queries and keys doesn't distort the magnitudes attention relies on, only the angle between vectors - and that angle is exactly what a dot-product attention score measures.
This keeps queries and keys factored apart (useful for linear-attention variants) and generalizes cleanly to sequence lengths beyond what the model saw in training, since nothing about the rotation is tied to a fixed maximum length the way a learned absolute-position table is.
That length-flexibility is why later context-extension techniques work by rescaling or interpolating RoPE's rotation frequencies rather than retraining a position table from scratch.

## Relations

- Origin: [[Paper — RoFormer - Enhanced Transformer with Rotary Position Embedding (2021)]] (Su et al.), replacing the additive absolute position encoding of [[Paper — Attention Is All You Need (2017)]].
- Inherits its frequency schedule from the sinusoidal encoding introduced in [[Paper — Attention Is All You Need (2017)]].

## Up

[[Cluster — Foundational canon]]
