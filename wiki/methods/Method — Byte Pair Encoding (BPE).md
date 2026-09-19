---
type: method
status: in-progress
updated: 2026-09-18
summary: "Iterative subword tokenization algorithm that merges the most frequent adjacent symbol pairs into new symbols, producing a fixed, compact vocabulary that can represent any word - including ones never seen in training."
cluster: ["Foundational canon"]
sources: []
tags: [tokenization, nlp]
aliases: ["BPE"]
---

# Method — Byte Pair Encoding (BPE)

## Definition

A subword tokenization algorithm that starts with a vocabulary of individual characters and repeatedly merges the most frequent adjacent pair of symbols in the training corpus into a new symbol, for a fixed number of merge operations - the algorithm's only hyperparameter.
Adapted from a data-compression algorithm to word segmentation in [[Paper — Neural Machine Translation of Rare Words with Subword Units (2015)]].

At inference time, the learned sequence of merge operations is applied to segment any word - including ones never seen during training - into a sequence of known subword symbols, guaranteeing a fixed, compact vocabulary with no unknown-token fallback.

## Why it works

Frequent whole words and frequent morphemes end up merged into single symbols, while rare or unseen words fall back to smaller, still-meaningful subword pieces instead of a generic "unknown" placeholder.
This means a model built on BPE never truly hits an out-of-vocabulary input, and can productively compose or recognize novel words (compounds, inflections, transliterations) from familiar pieces, the way a reader parses an unfamiliar compound word from its parts.

## Relations

- Origin: [[Paper — Neural Machine Translation of Rare Words with Subword Units (2015)]].
- Adopted at scale as the tokenizer for [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] (GPT-2), which uses a 50,257-token byte-level variant, and by essentially every large language model since.

## Up

- [[Cluster — Foundational canon]]
