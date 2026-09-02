---
type: concept
status: in-progress
updated: 2026-08-07
summary: "Sequence-modeling architecture built entirely on attention, no recurrence or convolution; the substrate nearly all LLM work targets."
cluster: ["Foundational canon"]
sources: []
tags: [architecture, attention, transformer]
aliases: []
---

# Concept — Transformer architecture

## Definition

A neural sequence-modeling architecture built entirely on **attention**, with no recurrence or convolution.
Introduced in [[Paper — Attention Is All You Need (2017)]].

## Core mechanism

- **Scaled dot-product attention:** `Attention(Q,K,V) = softmax(QKᵀ/√dₖ)V`. Maps a query against a set of key-value pairs; the output is a weighted sum of values, with weights from query-key compatibility. The `√dₖ` scaling prevents the softmax from saturating at large key dimensions.
- **Multi-head attention:** runs several attention heads in parallel over different learned linear projections of Q/K/V, then concatenates and re-projects. Lets the model attend to information from different representation subspaces simultaneously, which a single head averages away.
- **Positional encoding:** since attention has no inherent notion of sequence order, fixed (sinusoidal) or learned position vectors are added to token embeddings so the model can use ordering information.
- **Encoder-decoder stack:** encoder layers are self-attention plus feed-forward; decoder layers add causally-masked self-attention (blocking attention to future tokens) and cross-attention over the encoder's output.

## Why it matters

Self-attention connects any two positions in a sequence with a constant (O(1)) number of sequential operations, vs. O(n) for recurrent architectures.
This is what makes the architecture both parallelizable at training time and effective at long-range dependencies.

## Relations

- Origin paper: [[Paper — Attention Is All You Need (2017)]].
- Encoder-only and decoder-only variants underlie essentially all later large language models. Expect most future papers in generative/agentic AI to reference this concept page.
- Contrast with [[Concept — Convolutional neural networks (CNNs)]], the architecture whose depth/data/compute recipe this one inherited and extended to sequences.

## Up

- [[Cluster — Foundational canon]]
