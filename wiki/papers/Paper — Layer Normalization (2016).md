---
type: paper
status: mature
updated: 2026-09-18
summary: "Normalizes each training case's own activations across a layer's hidden units instead of across a mini-batch, making normalization work for recurrent networks and small or single-example batches."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Layer Normalization (2016).pdf"]
tags: [normalization, architecture]
aliases: ["arxiv:1607.06450", "Ba, Kiros & Hinton 2016", "LN"]
---

# Paper — Layer Normalization (2016)

## Metadata

arXiv:1607.06450.
Jimmy Lei Ba, Jamie Ryan Kiros, Geoffrey E. Hinton.
University of Toronto (Hinton also at Google).
Submitted July 2016.

## Impact

[[Paper — Batch Normalization (2015)]] normalizes each neuron's summed input using statistics computed across a mini-batch, which works well for feedforward image classifiers but breaks down for recurrent networks and for training with very small or single-example batches.
This paper introduces layer normalization: compute the mean and variance from all the hidden units within a single layer, for a single training case, instead of across the batch.
That one change is why every Transformer block trains stably regardless of batch size: LayerNorm is the normalization actually used inside the Transformer, GPT, and BERT, not batch normalization.

## Core idea

Instead of asking "what's the mean and variance of this one neuron's output, across every example in the mini-batch," layer normalization asks "what's the mean and variance across every neuron in this one layer, for this one example."
Concretely: for a hidden layer with H units and summed inputs a_1...a_H on a single training example, compute mu = mean(a_1...a_H) and sigma = stddev(a_1...a_H), then normalize each a_i to (a_i - mu) / sigma before applying a learned per-unit gain and bias.
Every training case gets its own mu and sigma, computed from its own activations, with no dependency on other examples in the batch and no running statistics to track at test time.
This makes it trivially applicable to recurrent networks (each time step just normalizes its own summed inputs) and to online learning with a batch size of 1, neither of which batch normalization handles cleanly.

## Why it endures

The paper's own experiments are RNN-focused (image-sentence retrieval, question answering, skip-thoughts, handwriting generation) and it explicitly reports that layer normalization underperforms batch normalization on convolutional networks, where different spatial locations in a feature map have genuinely different statistics.
Its lasting impact came from an application the authors didn't test: the Transformer, published the following year, adopted layer normalization inside every block, and every major decoder-only and encoder-only language model since (GPT, BERT, and beyond) inherited that choice.
Because it normalizes per-example rather than per-batch, it also composes cleanly with the variable batch sizes and sequence lengths that large-scale LLM training and inference actually use.

## Limitations

The paper reports that layer normalization does not outperform batch normalization on convolutional networks, and notes further research is needed to make it work well there - a gap RMSNorm and other later variants partially address, but which the original paper leaves open.

## Relations

- Direct response to [[Paper — Batch Normalization (2015)]]'s batch-size and recurrent-network limitations.
- Adopted inside every block of [[Paper — Attention Is All You Need (2017)]]'s Transformer, and inherited from there by [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1), [[Paper — BERT (2018)]], and [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] (GPT-2).
- See [[Method — Layer Normalization]] for the mechanism as reused across these later papers.

## Up

[[Cluster — Foundational canon]]
