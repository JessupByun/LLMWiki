---
type: method
status: mature
updated: 2026-09-18
summary: "Compute exact attention by tiling the computation into blocks that fit in fast on-chip GPU memory, using an incremental online-softmax update so the full attention matrix is never written to slow memory."
cluster: ["Foundational canon"]
sources: []
tags: [transformer, systems, efficiency]
aliases: ["FlashAttention", "IO-aware attention"]
---

# Method — FlashAttention

## Definition

Split the query, key, and value matrices into blocks small enough to fit in a GPU's fast on-chip SRAM, and compute attention block by block rather than materializing the full N-by-N score matrix in slow HBM.
An online-softmax update keeps a running maximum and running sum as each new block is processed, rescaling the output accumulated so far whenever a larger maximum appears, so the result is mathematically exact rather than approximate.
The backward pass recomputes the attention matrix from the same small blocks on the fly instead of storing it from the forward pass.

## Why it works

On a GPU, arithmetic is fast and memory access is slow; standard attention is bottlenecked by repeatedly reading and writing the full attention matrix to slow memory, not by the number of floating-point operations.
Tiling keeps all the data a block needs in fast on-chip memory for the whole time it's being processed, so the large intermediate matrix is never written to or read from slow memory at all - trading a modest increase in recomputation (in the backward pass) for a large reduction in memory traffic, which is what actually determines wall-clock speed.

## Relations

- Origin: [[Paper — FlashAttention (2022)]] (Dao et al.), applied to the scaled dot-product attention of [[Paper — Attention Is All You Need (2017)]].
- Implemented as an optional fused kernel in [[Repo — karpathy-nanoGPT]]'s causal self-attention.

## Up

[[Cluster — Foundational canon]]
