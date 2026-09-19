---
type: paper
status: mature
updated: 2026-09-18
summary: "Encodes token position by rotating the query/key vectors by an angle proportional to position, so the dot product between any two positions depends only on their relative distance."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — RoFormer - Enhanced Transformer with Rotary Position Embedding (2021).pdf"]
tags: [positional-encoding, architecture]
aliases: ["arxiv:2104.09864", "Su et al. 2021", "RoPE", "RoFormer"]
---

# Paper — RoFormer - Enhanced Transformer with Rotary Position Embedding (2021)

## Metadata

arXiv:2104.09864.
Jianlin Su, Yu Lu, Shengfeng Pan, Ahmed Murtadha, Bo Wen, Yunfeng Liu.
Zhuiyi Technology Co., Ltd.
Submitted April 2021.

## Impact

The original Transformer adds a fixed or learned position vector to each token's embedding before attention, which encodes absolute position but doesn't cleanly give attention a notion of relative distance, and is incompatible with linear-attention variants that need queries and keys kept factored apart.
This paper introduces Rotary Position Embedding (RoPE): instead of adding a position vector, rotate the query and key vectors by an angle proportional to their position before the dot product, so the attention score between two tokens depends only on their relative distance.
Substituted into BERT's pretraining, the resulting RoFormer architecture converges faster and beats BERT on three of six GLUE tasks; on long Chinese documents beyond 512 characters its advantage over word-based baselines widens with sequence length, direct evidence that rotation-based relative encoding generalizes better to lengths not fixed at training time.
RoPE went on to become the positional encoding scheme used by most modern open-weight LLMs, replacing both sinusoidal absolute encodings and learned relative-position tables.

## Core idea

Picture a 2D vector: rotating it by angle m*theta and then taking its dot product with another vector rotated by n*theta gives a result that depends only on the angle difference (m-n)*theta, not on m and n individually.
RoPE applies exactly this to query and key vectors: split each d-dimensional vector into d/2 coordinate pairs, and rotate the m-th token's pair i by angle m*theta_i, where theta_i shrinks geometrically with i using the same frequency schedule as the original Transformer's sinusoidal encoding.
When the rotated query at position m is dotted with the rotated key at position n, the result depends only on (m-n) times each theta_i - relative position falls straight out of an operation defined entirely in terms of each token's own absolute position, with no explicit relative-position lookup table or added term required.
Because rotation preserves vector norms, this also keeps queries and keys factored apart, so RoPE composes with linear-attention mechanisms the way additive position schemes cannot.

## Why it endures

RoPE's length-flexibility and clean relative-distance behavior are exactly the properties later work exploits to extend a model's context window after pretraining, by interpolating or rescaling the rotation frequencies - which is why RoPE, not sinusoidal or learned absolute schemes, became the default in most subsequent open-weight LLM architectures.

## Limitations

The paper's own honest gap: it proves RoPE has the mathematically desirable properties (relative-distance dependence, long-term decay of the attention score with distance) but doesn't explain why those properties translate into faster convergence or better long-text performance in practice.

## Relations

- Directly responds to the additive absolute encoding of [[Paper — Attention Is All You Need (2017)]] and the learned absolute encoding of [[Paper — BERT (2018)]].
- Inherits its frequency schedule from the sinusoidal encoding in [[Paper — Attention Is All You Need (2017)]].
- See [[Method — Rotary Position Embedding (RoPE)]] for the mechanism as reused across later LLMs.

## Up

[[Cluster — Foundational canon]]
