---
type: method
status: mature
updated: 2026-09-18
summary: "Normalize a layer's summed inputs using the mean and variance across that layer's own hidden units, computed separately per training example."
cluster: ["Foundational canon"]
sources: []
tags: [normalization, architecture]
aliases: ["LayerNorm", "LN"]
---

# Method — Layer Normalization

## Definition

For a layer with hidden units producing summed inputs a_1...a_H on a single example, compute mu = mean(a_1...a_H) and sigma = stddev(a_1...a_H) across those H units, then rescale each unit to (a_i - mu) / sigma before applying a learned per-unit gain and bias.
Unlike batch normalization, the statistics come from a single training case's own activations, not from a mini-batch, so there's nothing batch-size-dependent and nothing to track as a running average for test time.

## Why it works

Normalizing the distribution of summed inputs to a layer keeps gradients well-scaled regardless of how deep the network is or how the previous layer's outputs happen to be distributed for this particular example.
Because the statistics are per-example, it applies unchanged to recurrent networks (each time step normalizes its own inputs) and to variable or tiny batch sizes, both of which are awkward for batch normalization.
This is the specific property that made it the normalization of choice inside the Transformer block: LLM training and inference routinely use varying batch sizes and sequence lengths, and per-example statistics sidestep that entirely.

## Relations

- Origin: [[Paper — Layer Normalization (2016)]] (Ba, Kiros & Hinton), introduced as an alternative to [[Paper — Batch Normalization (2015)]] for recurrent networks and small batches.
- Used inside every block of [[Paper — Attention Is All You Need (2017)]]'s Transformer.
- Inherited from there by [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1) and [[Paper — BERT (2018)]].
- [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] (GPT-2) moves it to the start of each sub-block (pre-norm) rather than after, and adds an extra layer norm after the final attention block.
- [[Paper — LoRA (2021)]] leaves LayerNorm weights frozen and untouched, alongside the MLP layers, when applying low-rank adapters.

## Up

[[Cluster — Foundational canon]]
