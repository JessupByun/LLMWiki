---
type: paper
status: mature
updated: 2026-11-04
summary: "Introduced the Transformer, replacing recurrence with self-attention - the architecture nearly every modern LLM is built on."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Attention Is All You Need (2017).pdf"]
tags: [architecture, attention, transformer]
aliases: ["arxiv:1706.03762", "Transformer", "Vaswani et al. 2017"]
---

# Paper — Attention Is All You Need (2017)

Commonly known as the **Transformer** paper.

## Metadata

- **arXiv:** 1706.03762 (v7, revised 2023-08-02; originally NeurIPS 2017)
- **Authors:** Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N. Gomez, Łukasz Kaiser, Illia Polosukhin (Google Brain / Google Research / U. Toronto)

## Impact

Introduced the **Transformer**, the architecture underlying nearly every modern LLM.
Recurrent encoder-decoders (seq2seq, Bahdanau attention) process a sentence one position at a time, and that sequential dependency blocks parallelism within a training example.
The Transformer drops recurrence and convolution entirely and builds the whole model from attention, so every position is computed in parallel and any two positions are connected in a constant number of steps.
The big model set a new state of the art on WMT 2014 English-German at **28.4 BLEU**, more than 2 BLEU above the best previous results including ensembles, and a new single-model state of the art of **41.8 BLEU** on English-French after 3.5 days on eight P100 GPUs - a small fraction of the training cost of the best prior models.
It also transferred to English constituency parsing with little task-specific tuning, a first hint that the architecture was general rather than a translation trick.

## Core idea

Each attention layer lets every position look up information from every other position, in one step.
Each token is projected into a **query** (what it is looking for), a **key** (what it offers), and a **value** (its content); a token's output is the softmax-weighted sum of all values, with weights from its query's dot product with every key: `Attention(Q,K,V) = softmax(QK^T / √d_k) V`.
The `1/√d_k` scaling is there because large dot products push the softmax into regions with vanishingly small gradients.
**Multi-head attention** runs 8 such lookups in parallel over different learned 64-dimensional projections, so different heads can track different relationships at the same total cost as one full-width head - in ablation, a single head is 0.9 BLEU worse, and too many heads also hurts.

The paper's own attention visualizations show what that buys.
In "The Law will never be perfect, but its application should be just", two heads in layer 5 attend from "its" sharply to "Law" - anaphora resolution, a dependency spanning several words that a recurrent model would have to carry forward step by step.
Another head links "making" to the distant "more difficult" to complete the phrase "making ... more difficult".

The rest of the block is what makes the stack trainable at depth:

- **Encoder and decoder stacks** of 6 layers each; every encoder layer is self-attention followed by a position-wise feed-forward network (512 -> 2048 -> 512), and each decoder layer adds attention over the encoder's output.
- **Residual connection plus layer normalization** around every sublayer, `LayerNorm(x + Sublayer(x))`, borrowed from [[Paper — Deep Residual Learning for Image Recognition (2015)]] and [[Paper — Layer Normalization (2016)]].
- **Causal masking** in the decoder's self-attention, setting attention to future positions to minus infinity so generation stays autoregressive.
- **Sinusoidal positional encodings** added to the embeddings, since attention itself has no notion of order; learned position embeddings gave nearly identical results, and the sinusoids were kept because they might extrapolate to longer sequences.
- **Regularization:** dropout of 0.1 on every sublayer output and on the embedding sums, plus label smoothing of 0.1.

## Why it endures

The paper was framed around machine translation, but the architecture generalized completely.
Encoder-only variants became [[Paper — BERT - Pre-training of Deep Bidirectional Transformers for Language Understanding (2018)]], decoder-only variants became the GPT lineage from [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] onward, and the same block was later applied to image patches in [[Paper — An Image is Worth 16x16 Words - Transformers for Image Recognition at Scale (2020)]].
Its parallelism over sequence positions is what let training scale to the model and data sizes that [[Paper — Scaling Laws for Neural Language Models (2020)]] later measured.
The paper also flags, almost in passing, that attention "could yield more interpretable models" because individual heads visibly specialize - the starting point that mechanistic interpretability later turned into a research program.

## Relations

- See [[Concept — Transformer architecture]] for the reusable mechanism referenced by later papers.
- Directly descended from [[Paper — Neural Machine Translation by Jointly Learning to Align and Translate (2014)]], whose alignment mechanism is generalized here into query/key/value self-attention, and [[Paper — Sequence to Sequence Learning with Neural Networks (2014)]], whose encoder-decoder split this paper keeps while replacing both LSTMs with attention.
- Displaces [[Paper — Long Short-Term Memory (1997)]] as the default sequence architecture.
- Later refinements of its pieces: [[Method — Rotary Position Embedding (RoPE)]] for position, [[Method — FlashAttention]] for the attention kernel, and pre-norm placement of [[Method — Layer Normalization]] from GPT-2.

## Up

- [[Cluster — Foundational canon]]
