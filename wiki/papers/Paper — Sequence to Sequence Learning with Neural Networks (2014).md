---
type: paper
status: in-progress
updated: 2026-09-18
summary: "seq2seq - two LSTMs (one encodes, one decodes) that map a variable-length input sequence to a variable-length output sequence through a single fixed-length vector, the first pure neural system to beat a phrase-based SMT baseline on machine translation."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Sequence to Sequence Learning with Neural Networks (2014).pdf"]
tags: [architecture, rnn, lstm, machine-translation, seq2seq]
aliases: ["arxiv:1409.3215", "Sutskever et al. 2014", "seq2seq", "RNNencdec"]
---

# Paper — Sequence to Sequence Learning with Neural Networks (2014)

Commonly known as **seq2seq**.

## Metadata

- **arXiv:** 1409.3215 (v3, 2014-12-14)
- **Venue:** NeurIPS (NIPS) 2014; not independently verified against DBLP in this session.
- **Authors:** Ilya Sutskever, Oriol Vinyals, Quoc V. Le (Google)

## Impact

Introduced the general encoder-decoder framework for mapping a variable-length input sequence to a variable-length output sequence with two separate LSTMs - one reads the input and compresses it into a single fixed-dimensional vector, the other reads that vector and generates the output one token at a time.
On the WMT'14 English-to-French task, an ensemble of 5 such models scored 34.8 BLEU by direct translation alone, beating a strong phrase-based statistical machine translation (SMT) baseline (33.3) - the first time a pure neural system outperformed a phrase-based SMT pipeline on a large-scale translation task, without any hand-built sub-components.
This is the paper that established "read everything into a vector, then generate from it" as a general-purpose recipe for turning any sequence into any other sequence with a single trained network, rather than a pipeline of separately-tuned pieces.

## Core idea: one LSTM encodes, another decodes

The encoder LSTM reads the input sequence one token at a time and, after the final token, its hidden state is a single fixed-length vector meant to capture the meaning of the entire input.
The decoder LSTM then generates the output sequence token by token, conditioned on that vector exactly the way a language model is conditioned on the tokens it has generated so far - the source sentence is effectively baked into the decoder's initial state.
The paper's most striking empirical finding was almost accidental: **reversing the order of words in the source sentence** (leaving the target sentence unreversed) dropped test perplexity from 5.8 to 4.7 and raised BLEU from 25.9 to 30.6.
The explanation the authors give is about optimization, not meaning: reversing the source doesn't change the average distance between corresponding source and target words, but it does put the *first* words of each sentence close together, shortening the "minimal time lag" gradient descent has to bridge early in training - a concrete demonstration that how information is ordered for a sequential model can matter as much as what information is present.

## Why it endures

The single fixed-length vector this paper popularized is exactly the bottleneck [[Paper — Neural Machine Translation by Jointly Learning to Align and Translate (2014)]] diagnosed and fixed the same year, but the broader encoder/decoder split - one network that reads, one that writes, trained jointly end to end on the same objective - survived that fix and remains the shape of essentially every sequence-to-sequence system since, including the Transformer's own encoder-decoder configuration.

## Relations

- Directly critiqued and extended by [[Paper — Neural Machine Translation by Jointly Learning to Align and Translate (2014)]], which keeps the encoder/decoder split but replaces the single fixed-length context vector with a learned, per-step weighted combination of source annotations.
- Uses LSTMs (Hochreiter & Schmidhuber, 1997, not yet ingested) as the recurrent unit in both encoder and decoder.
- The encoder/decoder split this paper established is the direct ancestor of the Transformer's own encoder-decoder structure in [[Paper — Attention Is All You Need (2017)]], though Attention replaces both LSTMs with self-attention.

## Up

- [[Cluster — Foundational canon]]
