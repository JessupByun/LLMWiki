---
type: paper
status: mature
updated: 2026-09-18
summary: "MoE - a trainable gating network sparsely activates a handful of thousands of expert sub-networks per example, decoupling model capacity from computation cost and hitting 137 billion parameters at only a minor efficiency loss."
cluster: ["Foundational canon"]
sources: ["raw/papers/Paper — Outrageously Large Neural Networks (2017).pdf"]
tags: [conditional-computation, mixture-of-experts, sparsity, scaling]
aliases: ["arxiv:1701.06538", "Shazeer et al. 2017", "MoE", "Mixture of Experts", "Sparsely-Gated Mixture-of-Experts"]
---

# Paper — Outrageously Large Neural Networks (2017)

Commonly known as **MoE** (Mixture of Experts).

## Metadata

- **arXiv:** 1701.06538 (v1, 2017-01-23)
- **Venue:** submitted as "under review as a conference paper at ICLR 2017"; commonly cited simply as Shazeer et al. 2017, not independently verified against a final proceedings listing in this session.
- **Authors:** Noam Shazeer, Azalia Mirhoseini, Krzysztof Maziarz, Andy Davis, Quoc Le, Geoffrey Hinton, Jeff Dean.
- **Affiliation:** Google Brain (Maziarz was a Google Brain Residency intern from Jagiellonian University).

## Impact

Model capacity had been limited by the rule that every parameter costs compute on every example.
This paper is the first to demonstrate a large win from breaking that rule: a layer of up to 131,072 expert sub-networks, of which only a handful are evaluated per example, reaches 137 billion parameters while adding only minor computational overhead.
On language modeling it drops test perplexity 39% below a compute-matched baseline, and on WMT'14 En-Fr and En-De translation it beats Google's own GNMT production system on BLEU while using a fraction of the effective compute.
Fills the gap in [[Cluster — Foundational canon]]'s reading order right before [[Paper — Attention Is All You Need (2017)]] - the two 2017 papers arrive within months of each other, one showing sparsity can decouple capacity from compute, the other replacing recurrence with attention.

## Core idea

A Mixture-of-Experts (MoE) layer holds a large bank of identical-shaped feed-forward sub-networks ("experts") plus a small trainable gating network.
For each input, the gating network scores every expert, keeps only the top *k* scores (Noisy Top-K Gating: tunable Gaussian noise is added before the top-k cutoff, mainly to help load-balance which experts get picked), and the layer's output is just the weighted sum of those *k* experts' outputs - every other expert costs zero compute for that example, since a zero gate value means its forward pass is skipped entirely.
Concretely: a layer with 4,096 experts but k=4 active per token has roughly 1,000x the parameters of a single dense layer of the same size, for barely more compute than evaluating 4 of them.
The gating network is trained by ordinary backpropagation right alongside the experts, with no reinforcement learning needed, because a nonzero top-k gate value has a nonzero gradient with respect to the gating weights.
The layer is dropped convolutionally between two stacked LSTM layers, called fresh at every token position, so different tokens can route to entirely different experts and specialize on syntax or semantics (Appendix E shows one expert firing specifically on "a" introducing a leadership-related noun phrase).

Left alone, the gating network collapses onto favoring the same few experts, which get more gradient signal and are then favored even more - a self-reinforcing rich-get-richer failure.
The fix is two auxiliary losses added to the training objective: one penalizing high variance in each expert's total gate-weight ("importance") across a batch, one penalizing high variance in how many examples get routed to each expert ("load", using a smooth probabilistic estimator so it stays differentiable through the noise term).

## Why it endures

This is the direct ancestor of every sparse-MoE architecture used inside modern large language models: swap the paper's stacked-LSTM host model for a Transformer block and the recipe is unchanged, decouple total parameter count from per-token compute, gate sparsely, add a load-balancing loss.
Its own multilingual translation experiment - a single MoE model beating 8 of 12 separately-trained per-language-pair GNMT models on BLEU - previews the now-common finding that one sufficiently large sparse model can beat a fleet of dense specialists.

## Relations

- Builds on classical mixture-of-experts theory (Jacobs et al. 1991, Jordan & Jacobs 1994) and Eigen et al. 2013's deep, stacked MoE, but is the first to make the gating sparse and demonstrate a real capacity win from it rather than just a theoretical one.
- Trained with [[Paper — Adam - A Method for Stochastic Optimization (2014)]], with a memory-saving modification (dropping the first-moment term, factoring the second-moment estimator) to fit expert parameters within GPU memory at this scale.
- Uses residual ("shortcut") connections around every LSTM and MoE layer, the same mechanism [[Paper — Deep Residual Learning for Image Recognition (2015)]] introduced for CNNs, here credited for encouraging gradient flow through a much deeper stack.
- Arrives the same year as [[Paper — Attention Is All You Need (2017)]]; the two ideas - sparse conditional computation and attention-based architecture - are largely orthogonal at publication but get combined in later sparse-Transformer work (Switch Transformer, GShard) not yet ingested.

## Up

[[Cluster — Foundational canon]]
