---
type: paper
status: mature
updated: 2026-09-19
summary: "Trains a family of openly-released foundation models (7B-65B) exclusively on public data, deliberately past the Chinchilla compute-optimal point, and shows the 13B model beats GPT-3 (175B) on most benchmarks - the open-weights release that catalyzed the ecosystem of fine-tunes and derivatives built on top of it."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — LLaMA - Open and Efficient Foundation Language Models (2023).pdf"]
tags: [foundation-models, open-weights, scaling, transformer]
aliases: ["arxiv:2302.13971", "Touvron et al. 2023"]
---

# Paper — LLaMA: Open and Efficient Foundation Language Models (2023)

## Metadata

arXiv:2302.13971 (v1, Feb 2023).
Hugo Touvron, Thibaut Lavril, Gautier Izacard, Xavier Martinet, and collaborators.
Meta AI.

## Impact

LLaMA-13B outperforms GPT-3 (175B) on most benchmarks despite being more than 10x smaller, and LLaMA-65B is competitive with Chinchilla-70B and PaLM-540B - all achieved training exclusively on publicly available data, with no proprietary or undocumented datasets, and released openly to the research community.
This is the open-weights release that catalyzed the modern open-weights LLM ecosystem: within weeks of release, LLaMA became the base model for a wave of independently fine-tuned derivatives (Alpaca, Vicuna, and dozens more), and its architecture and successor releases (Llama 2, Llama 3) remain a default substrate for open-weights research to this day, including the Llama-3.1-405B replication used in [[Paper — Alignment Faking in Large Language Models (2024)]]'s own appendix.

## Core idea

Chinchilla scaling laws optimize for training compute alone, recommending a compute-optimal split between model size and training tokens - for example, a 10B-parameter model trained on 200B tokens.
But training compute is a one-time cost while inference compute is paid every time the model is served, so the model that is cheapest to train to a given performance level is not the model that is cheapest to run at scale.
LLaMA instead trains smaller models for far longer than Chinchilla-optimal: its 7B model is trained on over 1T tokens - roughly 5x the Chinchilla-recommended token count for that size - and the paper reports that performance keeps improving even past 1T tokens, well beyond where Chinchilla's own compute-optimal frontier would call the model "done."
The trade is deliberate: spend more total training compute than is technically optimal, in exchange for a smaller model that is dramatically cheaper and easier to run at inference time, including on a single GPU for the smaller sizes.

## Why it endures

The "train small, train long, optimize for inference cost rather than training cost" strategy this paper popularized became the standard playbook nearly every subsequent open-weight model family (Llama 2/3, Mistral, and beyond) has followed, reshaping how open-source labs think about the training-compute-versus-inference-cost trade-off that Chinchilla's own framing had left out.
Its architecture choices - pre-normalization, RoPE positional embeddings, SwiGLU activations - became a template widely reused across later open models.

## Relations

- Directly reacts against [[Paper — Training Compute-Optimal Large Language Models (2022)]] (Chinchilla): Chinchilla optimizes the model-size-versus-tokens split purely for training compute, while LLaMA deliberately trains smaller models on more tokens than that split recommends, reallocating compute toward cheaper inference instead.
- Reuses [[Method — Rotary Position Embedding (RoPE)]] and pre-normalization (built on the same RMSNorm-style idea as [[Method — Layer Normalization]]) rather than introducing new architectural primitives.
- Supplies the base model lineage behind later open-weights safety experiments in this wiki, including the Llama-3.1-405B minimal replication in [[Paper — Alignment Faking in Large Language Models (2024)]].

## Up

- [[Cluster — Foundational canon]]
