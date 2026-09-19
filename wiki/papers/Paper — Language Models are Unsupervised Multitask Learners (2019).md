---
type: paper
status: mature
updated: 2026-09-18
summary: "GPT-2 - a 1.5B-parameter Transformer trained on 40GB of curated web text achieves state-of-the-art results on 7 of 8 language modeling benchmarks in a zero-shot setting, showing a big enough language model starts performing tasks like translation and summarization from a natural-language prompt with no fine-tuning at all."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Language Models are Unsupervised Multitask Learners (2019).pdf"]
tags: [pretraining, zero-shot, transformer, nlp, scaling]
aliases: ["Radford et al. 2019", "GPT-2"]
---

# Paper — Language Models are Unsupervised Multitask Learners (2019)

Commonly known as **GPT-2**.

## Metadata

- **arXiv:** none; released as an OpenAI technical report / blog paper, February 2019.
- **Venue:** unpublished technical report, not independently verified against a peer-reviewed venue in this session.
- **Authors:** Alec Radford, Jeffrey Wu, Rewon Child, David Luan, Dario Amodei, Ilya Sutskever.
- **Affiliation:** OpenAI.

## Impact

[[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1) and [[Paper — BERT (2018)]] had shown pretraining plus fine-tuning beats task-specific architectures - but both still needed a labeled dataset and a fine-tuning pass per task.
This paper shows that once a language model and its training corpus are large enough, it starts doing many of those tasks anyway, with no fine-tuning step and no task-specific parameters at all: GPT-2, the largest of four models trained here (1.5B parameters), sets a new zero-shot state of the art on 7 of 8 tested language modeling benchmarks, and on the CoQA reading comprehension dataset matches or beats 3 of 4 supervised baseline systems without ever training on its 127,000+ labeled question-answer pairs.
It is the direct empirical ancestor of [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3), which turns this paper's zero-shot finding into a headline result at 100x the scale.

## Core idea

The paper's central bet is that a sufficiently large and diverse training corpus of natural text already contains implicit demonstrations of many tasks, so a language model trained only to predict the next token will, in the process, start learning to perform those tasks - without anyone ever writing a labeled dataset for them.
The concrete example the paper gives: ordinary web text naturally contains sentences like "he says in French: Je ne suis pas un imbecile," and enough repetitions of that `english = french` pattern across millions of documents let a language model infer the pattern.
So instead of fine-tuning on a labeled translation dataset, you can just show the model a few example pairs formatted as `english sentence = french sentence`, then an unfinished pair, and let it complete the pattern - the task is specified in natural language inside the prompt itself, not in the model's weights.
The same trick is used to elicit summarization (append the literal string `TL;DR:` after an article and let the model continue) and question answering (seed the context with example question-answer pairs before the real question).
To make this work at scale, the authors built WebText: rather than scraping the web indiscriminately, they scraped only outbound links from Reddit posts with 3+ karma, using upvotes as a cheap human-curation signal for document quality, yielding 40GB across 8 million documents (Wikipedia deliberately excluded, since it's a common component of the very evaluation sets being tested against).
The model itself is architecturally almost identical to GPT-1's decoder-only Transformer, with a few training-stability tweaks (moving [[Method — Layer Normalization|layer normalization]] to the start of each sub-block, an extra layer norm after the final attention block, and a residual-path initialization that scales down by the square root of the number of residual layers) plus a much larger 50,257-token byte-level [[Method — Byte Pair Encoding (BPE)|BPE]] vocabulary and a longer 1024-token context window.

Zero-shot performance improves log-linearly with model capacity across nearly every task tested, and the smallest model here (117M parameters) is architecturally identical to the original GPT, while the second-smallest (345M) matches BERT-large's size - letting the paper directly attribute gains to scale rather than architecture.
A companion analysis using Bloom filters over 8-grams checks how much of WebText's improvement is just memorized test-set overlap, and finds the overlap is small and comparable to the overlap standard benchmarks already have with their own training splits - the gains are not primarily explained by leakage.

## Why it endures

This paper is the missing link between GPT-1's still-supervised fine-tuning paradigm and GPT-3's fully prompting-based paradigm: it is the first place the field observes, empirically and at real scale, that scaling a language model and its training data is itself a form of unsupervised multitask learning, which GPT-3 later reframes as "in-context learning" and turns into the entire interface for using an LLM.

## Relations

- Architecturally builds directly on [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1); the paper's own smallest model is explicitly sized to match GPT-1, and its second-smallest to match [[Paper — BERT (2018)]]'s largest model, as a deliberate scale comparison.
- Built on the Transformer architecture from [[Paper — Attention Is All You Need (2017)]].
- Tokenizes with [[Method — Byte Pair Encoding (BPE)]], introduced in [[Paper — Neural Machine Translation of Rare Words with Subword Units (2015)]].
- Directly extended by [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3), which keeps this paper's zero-shot, prompting-based framing but scales the model and corpus roughly 100x and formalizes the few-shot in-context-learning story this paper only glimpses.

## Up

[[Cluster — Foundational canon]]
