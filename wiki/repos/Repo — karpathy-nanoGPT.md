---
type: repo
status: mature
updated: 2026-09-18
summary: "nanoGPT - a ~600-line, readable PyTorch reimplementation of GPT-2 (train.py + model.py) that reproduces GPT-2 (124M) on OpenWebText from scratch, used throughout this wiki's canon as the reference codebase for what the papers actually describe in working code."
cluster: []
sources: ["raw/repos/nanoGPT", "https://github.com/karpathy/nanoGPT"]
tags: [repo, gpt, transformer, reference-implementation, curiosity]
aliases: ["nanoGPT"]
---

# Repo — karpathy/nanoGPT

Curiosity ingest: not itself a primary research source, but the clearest working reference implementation of the decoder-only Transformer / GPT-2 recipe this wiki's canon spine (Attention Is All You Need, GPT-1, GPT-2, GPT-3, Scaling Laws) describes only in prose and equations.

## Commit pinned

`3adf61e154c3fe3fca428ad6bc3818b27a3b8291` (cloned 2026-09-18, shallow clone at `raw/repos/nanoGPT/`).

As of the pinned commit, the README flags the repo as superseded: Andrej Karpathy's newer [nanochat](https://github.com/karpathy/nanochat) is the maintained successor, and nanoGPT is kept up "for posterity" rather than actively developed.

## What it is

A deliberately minimal PyTorch training and sampling codebase for GPT-style language models, by Andrej Karpathy (a rewrite of his earlier minGPT, prioritizing readability and hackability over feature completeness).
The entire model definition (`model.py`) is about 300 lines and implements exactly the decoder-only Transformer architecture from [[Paper — Attention Is All You Need (2017)]] as used in [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1) and its successors: causal self-attention with [[Method — FlashAttention|flash-attention]] support when available, [[Method — Layer Normalization|LayerNorm]] with optional bias, and standard learned position embeddings.
The training loop (`train.py`) is a similarly compact ~300 lines and supports both single-GPU and multi-node distributed training (`torchrun` + DDP).
`train.py` reproduces GPT-2 (124M) on OpenWebText (an open reconstruction of GPT-2's private WebText training data) in about 4 days on a single 8xA100 40GB node, reaching a validation loss (~2.85 after brief finetuning from the OpenAI checkpoint) matching the original GPT-2 (124M)'s own reported numbers.

## Why it matters

Every canon paper this wiki covers from [[Paper — Attention Is All You Need (2017)]] onward describes the decoder-only Transformer training recipe in equations and prose; nanoGPT is the same recipe in ~600 lines of runnable code, small enough to read end to end in one sitting.
It's a useful cross-check when a paper's description leaves architectural details ambiguous (e.g. exactly where [[Method — Layer Normalization|LayerNorm]] goes relative to the residual connections, how causal masking is implemented, how learning-rate warmup and cosine decay are actually scheduled), and its baseline table (reproducing GPT-2's four released sizes' train/val loss on OpenWebText) is a concrete, checkable number to compare any paper's own reported figures against.

## Relations

- Implements the Transformer decoder from [[Paper — Attention Is All You Need (2017)]], following the specific architecture choices used in [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1) and [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] (GPT-2).
- Trains with [[Paper — Adam - A Method for Stochastic Optimization (2014)]] by default; [[Paper — Muon - An Optimizer for Hidden Layers in Neural Networks (2024)]] set its speedrunning records on a heavily modified fork of this exact codebase (`modded-nanogpt`, not cloned here).
- Superseded by nanochat (Karpathy, not ingested), the actively maintained successor referenced in this repo's own README.

## Up

No cluster yet - a standalone curiosity/reference ingest, cross-linked to the canon papers it implements rather than formally a member of [[Cluster — Foundational canon]].
