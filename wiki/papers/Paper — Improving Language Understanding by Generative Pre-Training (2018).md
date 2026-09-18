---
type: paper
status: mature
updated: 2026-09-18
summary: "GPT-1 - pretrain a Transformer decoder as a plain left-to-right language model on unlabeled text, then fine-tune it with minimal added parameters on each downstream task, beating architectures purpose-built for 9 of 12 NLP benchmarks."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Improving Language Understanding by Generative Pre-Training (2018).pdf"]
tags: [pretraining, transfer-learning, transformer, nlp]
aliases: ["Radford et al. 2018", "GPT-1", "GPT", "OpenAI GPT"]
---

# Paper — Improving Language Understanding by Generative Pre-Training (2018)

Commonly known as **GPT-1** (or simply GPT, before later GPT models made the version number necessary).

## Metadata

- **arXiv:** none; released as an OpenAI technical report / preprint, June 2018.
- **Venue:** unpublished preprint ("Work in progress"), not independently verified against a peer-reviewed venue in this session.
- **Authors:** Alec Radford, Karthik Narasimhan, Tim Salimans, Ilya Sutskever.
- **Affiliation:** OpenAI.

## Impact

Before this paper, getting a neural network to do well on a new NLP task usually meant designing a new architecture for that task and training it from scratch on whatever labeled data existed, since labeled data is scarce relative to raw text.
This paper showed that a single, task-agnostic Transformer, pretrained once as a language model on unlabeled text and then lightly fine-tuned per task, beats those bespoke architectures outright: new state-of-the-art results on 9 of 12 benchmarks studied, including +8.9% on the Stories Cloze Test, +5.7% on RACE question answering, and an overall GLUE score of 72.8 versus the previous best of 68.9.
Establishes the pretrain-then-fine-tune recipe that GPT-2, GPT-3, and [[Paper — BERT (2018)]] all inherit, and is one of the two 2018 papers filling the gap in [[Cluster — Foundational canon]] between [[Paper — Attention Is All You Need (2017)]] and [[Paper — Language Models are Few-Shot Learners (2020)]].

## Core idea

Split training into two stages that use the same model.
First, unsupervised pre-training: a 12-layer decoder-only Transformer (masked self-attention, so each position can only see earlier tokens) is trained with an ordinary next-token language modeling objective on a large corpus of unlabeled text (BooksCorpus, chosen specifically because its long, contiguous chapters let the model learn long-range dependencies that a shuffled sentence-level corpus like the 1B Word Benchmark cannot).
Second, supervised fine-tuning: for each target task, the pretrained model's final hidden state is fed into one new linear output layer, and all parameters (pretrained plus the new layer) are fine-tuned jointly on that task's labeled data.
The trick that makes this work across wildly different task shapes without a new architecture per task is a set of task-specific *input transformations*: a natural language inference pair becomes `premise $ hypothesis`, a question-answering example becomes `document $ question $ answer_k` run once per candidate answer, and so on, all as a single flat token sequence the same pretrained Transformer can read.
Concretely, the only genuinely new parameters introduced per task are the output layer and a couple of delimiter-token embeddings - everything else transfers directly.
An auxiliary language-modeling loss is kept alongside the task loss during fine-tuning, which the authors found both speeds up convergence and improves generalization on larger fine-tuning datasets.

## Why it endures

The two-stage pretrain-then-fine-tune structure, decoder-only Transformer choice, and the finding that transferring more pretrained layers helps monotonically, are exactly the recipe [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3) scales up two years later, minus the fine-tuning step itself, which GPT-3 replaces with pure prompting.
The paper's zero-shot analysis - heuristics that read task answers directly out of the untuned language model's predictions, and which improve steadily over the course of pretraining - is the first documented hint of the capability GPT-3 would later call in-context learning.

## Relations

- Built directly on the Transformer architecture from [[Paper — Attention Is All You Need (2017)]], using its decoder half with masked self-attention.
- Trained with [[Paper — Adam - A Method for Stochastic Optimization (2014)]].
- [[Paper — BERT (2018)]], published four months later, directly targets this paper's main limitation - its use of a strictly left-to-right (unidirectional) Transformer - by making the pretraining objective bidirectional instead.
- Directly extended by GPT-2 (Radford et al. 2019, not yet ingested) and ultimately [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3), which keeps this paper's architecture and pretraining objective but drops per-task fine-tuning entirely.

## Up

[[Cluster — Foundational canon]]
