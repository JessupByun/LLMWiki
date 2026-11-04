---
type: concept
status: mature
updated: 2026-11-04
summary: "Sequence-modeling architecture built entirely on attention, no recurrence or convolution; the substrate nearly all LLM work targets."
cluster: ["Foundational canon"]
sources: []
tags: [architecture, attention, transformer]
aliases: []
---

# Concept — Transformer architecture

## Definition

A neural sequence-modeling architecture built entirely on **attention**, with no recurrence or convolution, introduced in [[Paper — Attention Is All You Need (2017)]].
A Transformer is a stack of identical blocks, each made of two sublayers:

- **Multi-head self-attention:** every position computes a query, key, and value; its output is a softmax-weighted sum of all positions' values, `Attention(Q,K,V) = softmax(QKᵀ/√dₖ)V`, run in several heads in parallel over different learned projections and then concatenated.
  The `√dₖ` scaling keeps large dot products from saturating the softmax.
- **Position-wise feed-forward network (MLP):** the same two-layer network applied independently at every position, typically expanding to about 4x the model width and back.
- **Residual connection plus layer normalization** around each sublayer, so each block adds an update to a running representation rather than replacing it - originally after the residual sum (post-norm), and in GPT-2 and most later LLMs before the sublayer (pre-norm).
- **Positional information**, since attention is order-blind: originally sinusoidal or learned vectors added to the token embeddings, in most recent open-weight LLMs [[Method — Rotary Position Embedding (RoPE)]] applied inside attention.

The original model is an encoder-decoder for translation: the decoder adds causal masking (no attending to future positions) and cross-attention over the encoder's output.
Encoder-only stacks (BERT) drop the decoder; decoder-only stacks (the GPT lineage and nearly all modern LLMs) drop the encoder and cross-attention and keep the causal mask.

## Why it works

Self-attention connects any two positions in a constant (O(1)) number of sequential steps, versus O(n) for a recurrent network, so long-range dependencies don't have to survive being carried forward step by step.
Because nothing depends on the previous position's output during training, every position in a sequence is computed in parallel, which is what lets the architecture use modern accelerators efficiently and scale to very large models and datasets.
The price is attention's cost growing quadratically with sequence length, which kernels like [[Method — FlashAttention]] attack at the hardware level.
The residual structure also gives the model a shared "residual stream" that every attention head and MLP reads from and writes to, which is the object mechanistic interpretability decomposes.

## Relations

- Origin paper: [[Paper — Attention Is All You Need (2017)]].
- Built from [[Method — Layer Normalization]] and the residual connections of [[Paper — Deep Residual Learning for Image Recognition (2015)]].
- Encoder-only variant: [[Paper — BERT (2018)]]; decoder-only variant: [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] onward; applied to image patches in [[Paper — An Image is Worth 16x16 Words (2020)]].
- Its decoder-only form supports [[Concept — In-context learning]].
- Contrast with [[Concept — Convolutional neural networks (CNNs)]], the architecture whose depth/data/compute recipe this one inherited and extended to sequences.

## Up

- [[Cluster — Foundational canon]]
