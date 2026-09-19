---
type: paper
status: mature
updated: 2026-09-18
summary: "Fine-tunes a pretrained seq2seq generator jointly with a neural retriever over a dense Wikipedia index, so the model conditions its output on retrieved passages instead of relying only on facts memorized in its parameters."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks (2020).pdf"]
tags: [retrieval, generation, knowledge]
aliases: ["arxiv:2005.11401", "Lewis et al. 2020", "RAG"]
---

# Paper — Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks (2020)

## Metadata

arXiv:2005.11401.
Patrick Lewis, Ethan Perez, Aleksandra Piktus, Fabio Petroni, Vladimir Karpukhin, Naman Goyal, Heinrich Kuttler, Mike Lewis, Wen-tau Yih, Tim Rocktaschel, Sebastian Riedel, Douwe Kiela.
Facebook AI Research; University College London; New York University.
Submitted May 2020.

## Impact

A pretrained language model stores facts implicitly in its parameters, which makes those facts hard to revise, hard to inspect, and prone to "hallucination" when the model states something confidently wrong.
This paper combines a pretrained seq2seq generator ([[Paper — BERT (2018)]]-derived dense retriever plus a BART decoder) with an explicit, non-parametric memory: a dense vector index of Wikipedia that the model queries at generation time and conditions its output on.
Both the retriever and generator are fine-tuned jointly with no supervision on which document should be retrieved, and the whole system sets a new state of the art on three open-domain question-answering benchmarks, beating both pure extractive-QA pipelines and pure parametric closed-book models like T5, while generating text that human evaluators judge more specific and factual than a parametric-only baseline.

## Core idea

Split the model into two cooperating pieces: a retriever that, given the input, returns a probability distribution over the top-k most relevant Wikipedia passages, and a generator that produces the output conditioned on both the input and a retrieved passage.
Because which passage is "correct" is never labeled during training, the retrieved passage is treated as a latent variable and marginalized out: the model considers each of the top-k retrieved passages, generates (or scores) an output under each one, and sums the results weighted by how relevant the retriever judged that passage to be.
Two variants differ in how coarse-grained that marginalization is: RAG-Sequence commits to the same single retrieved passage for the entire generated output, while RAG-Token can draw a different passage for each individual token, letting the model stitch together content from multiple sources within one answer.
A concrete illustration from the paper: asked to generate a Jeopardy-style clue about Hemingway, the model's attention shifts to a passage about "A Farewell to Arms" while generating that title, then shifts to a different passage about "The Sun Also Rises" partway through the same output - visible, inspectable evidence of which document is driving which part of the answer.

## Why it endures

Because the retrieval index is just a database of raw text, it can be swapped out at test time with no retraining at all: the paper demonstrates this directly by replacing a 2016 Wikipedia snapshot with a 2018 one and showing the model's answers about "who is the current president of X" update accordingly.
That decoupling of "what the model knows" from "what the model learned during training" is the property that made retrieval-augmented generation the standard architecture for grounding language models in an updatable, inspectable knowledge source, well beyond the open-domain QA tasks this paper evaluates on.

## Limitations

The paper's own analysis found that in some settings (long-form generation like story writing) the retrieval component can "collapse" and learn to retrieve the same passages regardless of the input, at which point the generator learns to ignore retrieval entirely and the system degrades to its parametric-only baseline.

## Relations

- Retriever is Dense Passage Retriever, built on a [[Paper — BERT (2018)]]-style bi-encoder.
- Generator is BART, a pretrained encoder-decoder in the same family as [[Paper — Attention Is All You Need (2017)]]'s Transformer.
- See [[Method — Retrieval-Augmented Generation (RAG)]] for the mechanism as reused across later knowledge-grounded LLM systems.

## Up

[[Cluster — Foundational canon]]
