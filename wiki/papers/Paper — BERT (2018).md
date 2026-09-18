---
type: paper
status: mature
updated: 2026-09-18
summary: "BERT - pretrains a bidirectional Transformer by predicting randomly masked tokens from both left and right context at once, advancing state of the art on 11 NLP tasks and founding the masked-language-model recipe behind every encoder-only representation model since."
cluster: ["Foundational canon"]
sources: ["raw/papers/Paper — BERT (2018).pdf"]
tags: [pretraining, transfer-learning, transformer, nlp, masked-language-modeling]
aliases: ["arxiv:1810.04805", "Devlin et al. 2018", "BERT"]
---

# Paper — BERT (2018)

Full title: **BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding**.

## Metadata

- **arXiv:** 1810.04805 (v1 2018-10-11, v2 2019-05-24)
- **Venue:** NAACL 2019.
- **Authors:** Jacob Devlin, Ming-Wei Chang, Kenton Lee, Kristina Toutanova.
- **Affiliation:** Google AI Language.

## Impact

[[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1) had shown pretrain-then-fine-tune works, but its Transformer could only attend to the left context, which is a real handicap for tasks like question answering where the answer needs both sides of a sentence at once.
BERT removes that constraint by pretraining a bidirectional Transformer to predict randomly masked-out words from context on both sides, and the result is a clean sweep: new state of the art on 11 NLP tasks, pushing the GLUE score to 80.5 (a 7.7-point jump), SQuAD v1.1 Test F1 to 93.2, and SQuAD v2.0 Test F1 to 83.1.
The second of the two 2018 papers filling the gap in [[Cluster — Foundational canon]] between [[Paper — Attention Is All You Need (2017)]] and [[Paper — Language Models are Few-Shot Learners (2020)]], and still the direct ancestor of the encoder-only models used for embeddings, retrieval, and classification even after decoder-only generative models became dominant for open-ended generation.

## Core idea

Ordinary language modeling can only be trained left-to-right or right-to-left, because letting a word see both directions of context at every layer during training would let it trivially "see itself" and just copy the answer.
BERT sidesteps this with a masked language model (MLM) objective, inspired by the Cloze test: randomly mask 15% of input tokens and train the model to predict the original token from the surrounding context on both sides.
To avoid the model overfitting to a special `[MASK]` token that never appears at fine-tuning time, the masking is randomized further: a chosen token is replaced with `[MASK]` 80% of the time, a random other token 10% of the time, and left unchanged 10% of the time, and the model still has to predict the original token in every case.
Concretely, given "my dog is hairy" with "hairy" chosen for masking, the model might see "my dog is [MASK]", "my dog is apple", or even the original "my dog is hairy" unchanged, and in all three cases the training signal is "predict hairy here."
A second, cheaper pretraining task, Next Sentence Prediction (NSP), has the model predict whether one span of text genuinely follows another or is a random pairing, since many downstream tasks (QA, NLI) hinge on relationships between two spans that pure word-level language modeling doesn't capture.
Both tasks train jointly on the same architecture - a multi-layer bidirectional Transformer encoder, unchanged in shape between pretraining and every downstream task - so fine-tuning still means plugging in task-specific inputs and outputs and updating all parameters, exactly as in GPT-1, just with a bidirectional model underneath.

Ablations isolate exactly where the gains come from: removing NSP alone hurts QNLI, MNLI, and SQuAD; further replacing the MLM objective with a plain left-to-right language model (reproducing something close to GPT-1's setup, but with BERT's larger training corpus and fine-tuning scheme) hurts every task, with the largest drops on SQuAD and MRPC - confirming that bidirectionality itself, not just more data or a fine-tuning-time trick, is what's driving the improvement.
A separate scaling ablation shows larger models improve accuracy on every GLUE task tried, including MRPC's mere 3,600 labeled examples, which the authors present as the first convincing evidence that scaling up model size helps even very small downstream tasks, provided the model has been sufficiently pretrained.

## Why it endures

Masked-language-model pretraining on a bidirectional Transformer became the default recipe for encoder-only representation models (RoBERTa, ALBERT, DistilBERT, and the broader "BERT-style" family), and BERT-derived encoders remain the standard backbone for embeddings, retrieval, and classification pipelines even in a landscape now dominated by decoder-only generative LLMs for open-ended text generation.

## Relations

- Built on the Transformer architecture from [[Paper — Attention Is All You Need (2017)]], using its encoder half with unrestricted (bidirectional) self-attention, in contrast to GPT-1's decoder-only, left-to-right restriction.
- Directly responds to and ablates against [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1); the paper's own ablation study reconstructs a GPT-1-like unidirectional baseline on BERT's data to isolate bidirectionality's specific contribution.
- Trained with [[Paper — Adam - A Method for Stochastic Optimization (2014)]].

## Up

[[Cluster — Foundational canon]]
