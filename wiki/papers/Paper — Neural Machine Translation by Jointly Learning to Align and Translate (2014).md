---
type: paper
status: in-progress
updated: 2026-09-18
summary: "Bahdanau attention - replaced the single fixed-length context vector in encoder-decoder translation with a learned, per-step weighted lookup over every source word, the direct ancestor of the Query/Key/Value attention used in every modern Transformer."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Neural Machine Translation by Jointly Learning to Align and Translate (2014).pdf"]
tags: [architecture, attention, rnn, machine-translation]
aliases: ["arxiv:1409.0473", "Bahdanau et al. 2014", "Bahdanau attention", "RNNsearch"]
---

# Paper — Neural Machine Translation by Jointly Learning to Align and Translate (2014)

Commonly known as **Bahdanau attention**.

## Metadata

- **arXiv:** 1409.0473 (v7, 2016-05-19)
- **Venue:** ICLR 2015; printed on the paper itself.
- **Authors:** Dzmitry Bahdanau (Jacobs University Bremen), KyungHyun Cho, Yoshua Bengio (Universite de Montreal)

## Impact

Diagnosed and fixed the single fixed-length-vector bottleneck in the encoder-decoder translation framework used by [[Paper — Sequence to Sequence Learning with Neural Networks (2014)]] and Cho et al.'s RNN Encoder-Decoder, by letting the decoder look back at every position in the source sentence and learn which ones matter most for each word it generates, instead of compressing the entire source into one vector up front.
This is the paper that introduced what the field now just calls **attention** - the mechanism [[Paper — Attention Is All You Need (2017)]] would, three years later, strip every surrounding recurrent network away from and build an entire architecture around.
On WMT'14 English-to-French, the resulting model (RNNsearch) matched the performance of a strong phrase-based SMT baseline (Moses) on sentences with no unknown words, and unlike the plain encoder-decoder, showed no degradation as sentences got longer.

## Core idea: a learned, per-word soft lookup over the source sentence

Instead of encoding a whole sentence into one fixed-length vector, the encoder (a bidirectional RNN) keeps a separate annotation vector for every source word, each one summarizing that word in the context of its neighbors on both sides.
For every word the decoder is about to generate, a small feedforward "alignment model" scores how relevant each of those annotation vectors is to the current decoding step, turns those scores into a probability distribution with a softmax, and takes a weighted sum of the annotations as the context fed into that decoding step - a different weighted mixture of the source sentence for every single output word.

The paper's own example makes the payoff concrete: translating "the man" into French requires choosing between "le," "la," "les," or "l'" for "the," a choice that depends on the gender of "man."
A hard, one-to-one word alignment can't represent that dependency, but this soft, learned weighting lets the model look at both "the" and "man" together when deciding, and the paper shows it gets exactly this case right - a small but precise demonstration of why a *soft*, differentiable alignment beats a hard, fixed one.

## Why it endures

This is the direct conceptual ancestor of the Query/Key/Value attention used in every modern Transformer: strip away the surrounding recurrent encoder and decoder, keep only "compute an alignment score against a set of positions, softmax it, take a weighted sum," and let that same lookup be computed at every position rather than only the decoder's, and the result is self-attention.
The core computational move introduced here - a learned, differentiable soft lookup over a variable-sized set of positions - reappears throughout deep learning well beyond machine translation, but this is where it started.

## Relations

- Directly extends [[Paper — Sequence to Sequence Learning with Neural Networks (2014)]] and Cho et al.'s RNN Encoder-Decoder (not yet ingested) by replacing their single fixed-length context vector with this learned, per-step weighted combination of source annotations.
- The direct conceptual ancestor of self-attention in [[Paper — Attention Is All You Need (2017)]], which generalizes this alignment mechanism into the Query/Key/Value formulation and removes the surrounding recurrence entirely.

## Up

- [[Cluster — Foundational canon]]
