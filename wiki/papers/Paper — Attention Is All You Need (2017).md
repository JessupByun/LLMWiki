---
type: paper
status: in-progress
updated: 2026-08-07
summary: "Introduced the Transformer, replacing recurrence with self-attention - the architecture nearly every modern LLM is built on."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Attention Is All You Need (2017).pdf"]
tags: [architecture, attention, transformer]
aliases: ["arxiv:1706.03762"]
---

# Paper — Attention Is All You Need (2017)

## Metadata

- **arXiv:** 1706.03762 (v7, revised 2023-08-02; originally NeurIPS 2017)
- **Authors:** Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N. Gomez, Łukasz Kaiser, Illia Polosukhin (Google Brain / Google Research / U. Toronto)

## Impact

Introduced the **Transformer**, the architecture underlying nearly every modern LLM (GPT, Claude, Gemini, BERT).
It replaced sequential processing (RNNs/LSTMs) with self-attention, letting a model look at an entire sequence at once instead of one word at a time.
The result set a new state-of-the-art BLEU on WMT14 English-German (28.4) and English-French (41.8) translation, beating prior best models, including ensembles, at a fraction of their training compute.
This is arguably the single most consequential architecture decision in modern generative AI - nearly everything downstream is a variant of one half of this design.

## Core idea: self-attention

For every word, the model computes three vectors: a **Query** (what this word is looking for), a **Key** (what information this word offers), and a **Value** (the word's actual content).
Comparing one word's Query against every other word's Key produces attention weights, meaning how much each word should focus on every other word, regardless of how far apart they are.

Concrete example: in "The animal didn't cross the street because it was too tired," self-attention lets the model connect "it" directly to "animal" rather than "street."
That's a dependency an RNN would have to carry across several intervening steps, and might lose along the way.

**Multi-head attention** runs 8 of these attention computations in parallel over different learned projections, so the model can track several kinds of relationships (e.g. syntactic structure, coreference) simultaneously instead of averaging them into one blurred signal.

## Why it endures

The paper was framed entirely around machine translation, but the architecture generalized completely.
Encoder-only variants became models like BERT, decoder-only variants became the GPT lineage, and virtually every LLM and agentic system since is built on one half of this design.

## Relations

- See [[Concept — Transformer architecture]] for the reusable mechanism referenced by later papers.
- Alongside [[Paper — ImageNet Classification with Deep CNNs (2012)]] as one of the two archetypal architecture papers in [[Cluster — Foundational canon]].

## Up

- [[Cluster — Foundational canon]]
