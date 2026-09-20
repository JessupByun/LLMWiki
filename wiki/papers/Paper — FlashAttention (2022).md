---
type: paper
status: mature
updated: 2026-09-18
summary: "Restructures the attention computation to minimize reads and writes to slow GPU memory instead of minimizing FLOPs, computing the exact same attention output 2-4x faster with linear rather than quadratic memory."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — FlashAttention (2022).pdf"]
tags: [transformer, systems, efficiency]
aliases: ["arxiv:2205.14135", "Dao et al. 2022", "FlashAttention"]
---

# Paper — FlashAttention (2022)

## Metadata

arXiv:2205.14135.
Tri Dao, Daniel Y. Fu, Stefano Ermon, Atri Rudra, Christopher Re.
Stanford University; University at Buffalo, SUNY.
Submitted May 2022.

## Impact

Prior work speeding up attention mostly approximated it, trading away exactness to cut the number of floating-point operations, but most of these methods showed little real wall-clock speedup because they ignored the actual bottleneck: attention on a GPU is memory-bound, not compute-bound, so cutting FLOPs while still repeatedly reading and writing the full N-by-N attention matrix to slow memory barely helps.
This paper reorganizes the exact, unapproximated attention computation to minimize those memory reads and writes instead, using no new mathematics, just a different order of operations.
The result trains BERT-large 15% faster than the MLPerf speed record, GPT-2 up to 3x faster than the standard HuggingFace implementation, and cuts attention's memory footprint from quadratic to linear in sequence length, up to 20x less memory than a standard implementation - all while computing bit-for-bit the same attention output as before.

## Core idea

A GPU has a small pool of extremely fast on-chip memory (SRAM) and a much larger pool of much slower memory (HBM); standard attention implementations compute the full N-by-N score matrix and immediately write it out to the slow memory, then read it straight back in for the softmax step, then again for the final weighted sum - each of those full round trips to slow memory is the actual bottleneck, not the arithmetic.
FlashAttention's fix is tiling: split the query, key, and value matrices into small blocks that fit entirely in fast on-chip memory, and compute attention block by block, incrementally accumulating the correct softmax-weighted output using a running correction factor, so the full N-by-N matrix is never written to slow memory at all.
The catch is that softmax needs to normalize over every key at once, which seems to require seeing the whole row before finishing any of it; the paper's trick is an "online softmax" that keeps a running maximum and running sum as each new block arrives, then rescales everything computed so far by a correction factor whenever a new block's maximum turns out to be larger - mathematically exact, not an approximation, just computed incrementally.
For the backward pass, rather than storing the huge intermediate attention matrix from the forward pass (which is the whole thing this method is trying to avoid), FlashAttention simply recomputes it on the fly from the same small blocks - trading a modest amount of extra arithmetic for a large reduction in memory traffic, since arithmetic is cheap on a GPU and memory access is expensive.

## Why it endures

The paper's central lesson, that FLOP count is the wrong thing to optimize on modern hardware and memory-access pattern is what actually determines wall-clock speed, generalized far beyond attention: it's the reason essentially every subsequent LLM training and inference stack ships a fused, IO-aware attention kernel rather than a naive matrix-multiply implementation, and the same reasoning has since been applied to other memory-bound operations throughout the Transformer stack.
Because the algorithm computes the mathematically exact attention output with no approximation, it required no retraining or re-validation of existing models to adopt - it's a drop-in replacement, which is a large part of why it was adopted essentially universally within a couple of years.

## Limitations

The implementation requires hand-written CUDA kernels tuned to a specific GPU's memory hierarchy, a considerably lower-level engineering effort than writing ordinary PyTorch, and the paper's own future-work section names compiling from a higher-level specification to an IO-aware kernel automatically as unsolved.

## Relations

- Directly optimizes the scaled dot-product attention mechanism from [[Paper — Attention Is All You Need (2017)]], with no change to the mathematical operation being computed.
- See [[Method — FlashAttention]] for the mechanism as reused across later training and inference stacks, including the [[Repo — karpathy-nanoGPT]] reference implementation already in this wiki.

## Up

- [[Cluster — Foundational canon]]
