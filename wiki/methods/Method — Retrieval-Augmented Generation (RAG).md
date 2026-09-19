---
type: method
status: mature
updated: 2026-09-18
summary: "Query an external document index for relevant text at generation time and condition the generator's output on the retrieved passages, treating retrieval as a latent variable marginalized during training."
cluster: ["Foundational canon"]
sources: []
tags: [retrieval, generation, knowledge]
aliases: ["RAG", "Retrieval-Augmented Generation"]
---

# Method — Retrieval-Augmented Generation (RAG)

## Definition

Pair a retriever, which returns the top-k most relevant passages from a document index given an input query, with a generator that conditions its output on both the input and the retrieved passages.
Because which passage is correct is never directly labeled, the retrieved passage is treated as a latent variable and marginalized: the generator's output probability is summed across the top-k retrieved passages, weighted by how relevant the retriever judged each one.

## Why it works

Splitting "what the model knows" into a fixed parametric component (the generator's weights) and an updatable non-parametric component (the document index) lets factual knowledge be revised, expanded, or inspected without retraining the generator at all - swap the index and the model's answers about the world change accordingly.
It also gives generation a form of provenance: the retrieved passages that drove a given output can be inspected directly, unlike knowledge implicitly baked into a language model's parameters.

## Relations

- Origin: [[Paper — Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks (2020)]] (Lewis et al.), combining a [[Paper — BERT (2018)]]-style dense retriever with a BART generator built on [[Paper — Attention Is All You Need (2017)]]'s Transformer.

## Up

[[Cluster — Foundational canon]]
