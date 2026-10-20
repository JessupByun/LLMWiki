---
type: paper
status: mature
updated: 2026-10-20
summary: "OpenAI's recipe for training very large sparse autoencoders: replace the L1 sparsity penalty with a TopK activation that keeps exactly k features per token, add tricks that nearly eliminate dead features, and find clean scaling laws - demonstrated with a 16-million-feature SAE on GPT-4 - plus new metrics that judge SAEs by whether their features are useful rather than just how well they reconstruct."
cluster: ["Mechanistic interpretability"]
sources: ["raw/papers/mechanistic-interpretability/Paper — Scaling and Evaluating Sparse Autoencoders (2024).pdf"]
tags: [interpretability, sparse-autoencoders, scaling-laws, dictionary-learning, evaluation]
aliases: ["arxiv:2406.04093", "Gao et al. 2024", "TopK SAE"]
---

# Paper — Scaling and Evaluating Sparse Autoencoders (2024)

## Metadata

arXiv:2406.04093 (v1, Jun 2024).
Leo Gao, Tom Dupré la Tour, Henk Tillman, Gabriel Goh, Rajan Troll, Alec Radford, Ilya Sutskever, Jan Leike, and Jeffrey Wu.
OpenAI's Superalignment interpretability team.

## Impact

A frontier model plausibly knows millions of concepts, so an SAE that captures them must be enormous - but large SAEs were hard to train, with many features going permanently dead and a sparsity penalty that needed constant retuning.
This paper fixes both problems with simple changes, shows SAE quality then improves predictably with size like language models do, and trains one with 16 million features on GPT-4.
It also pushed the field to ask a harder question than "how well does it reconstruct?": are the features actually the ones we want, and are they explainable precisely rather than just loosely?

## Problem it solved

Standard SAEs, as in [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]], trade reconstruction against an L1 penalty whose strength must be tuned, and which systematically shrinks feature activations toward zero.
At scale, many features stop firing entirely - the paper notes that Anthropic's 34-million-feature SAE had only 12 million alive.
And SAEs were judged mainly on reconstruction error and sparsity, which can be gamed: an infinitely wide SAE could reconstruct perfectly while learning nothing meaningful.

## Core idea

Instead of penalizing activations and hoping few survive, let only the k largest features fire on each token and zero the rest - a **TopK** activation.
Sparsity is now set directly (choose k) rather than tuned indirectly, and since there is no penalty, there is no shrinkage.
Two more tricks keep features alive: start the encoder as the transpose of the decoder, and add an auxiliary loss that asks dead features to model whatever the live ones fail to reconstruct.

With training stable, reconstruction error follows power laws (plus an irreducible term) in the number of features, and a joint law in features and k for small k, so SAE size can be planned the way language-model size is.

## How it's built

SAEs are trained on a late-layer residual stream of GPT-2 small and of a series of models sharing GPT-4's architecture, up to GPT-4 itself, with 64-token contexts.
Four quality metrics go beyond reconstruction:
- **Downstream loss:** how much worse the language model predicts when the SAE's reconstruction replaces the real activation.
- **Probe loss:** whether any single feature predicts each of 61 binary concepts (sentiment, language, and so on).
- **Explainability:** whether simple n-gram explanations (Neuron to Graph) predict a feature's firing with both good recall and good precision.
- **Ablation sparsity:** whether removing one feature changes only a few output tokens.

## Results

- TopK beats ReLU-with-L1 on the reconstruction-sparsity tradeoff, with the gap growing at scale, and outperforms other proposed fixes (gated SAEs, ProLU) overall.
- The 16-million-feature GPT-4 SAE, trained on 40 billion tokens, has only 7% dead features; in smaller-scale ablations without the mitigations, up to 90% died.
- Substituting it into GPT-4 still costs performance - the resulting loss matches a model trained with about 10% of GPT-4's pretraining compute.
- Larger subject models need larger SAEs for the same error, with a worse scaling exponent.
- Bigger SAEs generally score better on all four metrics; increasing k helps probes and ablation sparsity but hurts explainability, and everything degrades as k approaches the model's dimension.
- TopK features get better explanation recall (over 1.5x) at slightly worse precision (over 0.9x) than ReLU features of the same size.

## Why it endures

TopK-style SAEs (reviving Makhzani and Frey's 2013 k-sparse autoencoder) became one of the widely used SAE architectures, alongside JumpReLU, and the paper's scaling laws made SAE sizing a planning question rather than guesswork.
Its precision-and-recall framing targets a real hazard, the "illusion of interpretability" (Bolukbasi et al. 2021), where a loose explanation sounds right because it covers every activation, while also firing in many places the feature doesn't.
The 10%-of-compute result is a sobering benchmark: even a 16-million-feature dictionary leaves much of GPT-4's computation unexplained.

## Limitations

The authors' own:
- TopK forces exactly k features on every token, which is likely suboptimal.
- Optimization could be greatly improved.
- It's unclear which metrics best track usefulness for real applications such as steering, anomaly detection, and circuit finding.
- A large fraction of features (especially in GPT-4) are not yet adequately monosemantic.
- The probe metric is noisy, and N2G explanations capture only simple patterns.
- 64-token contexts may be too short to show the most interesting behaviors.

## Relations

- Scales the SAE of [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]] with a different architecture than the L1-based [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]], published weeks apart; see [[Method — Sparse Autoencoder (SAE)]].
- Cites [[Paper — Sparse Feature Circuits - Discovering and Editing Interpretable Causal Graphs in Language Models (2024)]] as evidence that SAE features can be used to find circuits, one of the downstream applications its metrics try to anticipate.
- Adapts the compute-frontier methodology of [[Paper — Scaling Laws for Neural Language Models (2020)]] (via Lindsey et al. 2024) to SAEs.

## Up

- [[Cluster — Mechanistic interpretability]]
