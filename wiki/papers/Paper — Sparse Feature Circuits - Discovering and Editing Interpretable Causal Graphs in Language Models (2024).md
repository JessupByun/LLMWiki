---
type: paper
status: mature
updated: 2026-10-20
summary: "Builds circuits out of sparse autoencoder features instead of neurons or attention heads, using fast gradient-based approximations to find which features causally drive a behavior - small, readable circuits that explain most of a behavior - and uses them to remove a classifier's reliance on gender by having a human delete the features that look irrelevant, with no extra labeled data."
cluster: ["Mechanistic interpretability"]
sources: ["raw/papers/mechanistic-interpretability/Paper — Sparse Feature Circuits - Discovering and Editing Interpretable Causal Graphs in Language Models (2024).pdf"]
tags: [interpretability, circuits, sparse-autoencoders, attribution-patching, spurious-correlations]
aliases: ["arxiv:2403.19647", "Marks et al. 2024", "SFC", "SHIFT"]
---

# Paper — Sparse Feature Circuits - Discovering and Editing Interpretable Causal Graphs in Language Models (2024)

## Metadata

arXiv:2403.19647 (v3, Mar 2025; originally Mar 2024); ICLR 2025.
Samuel Marks, Can Rager, Eric J. Michaud, Yonatan Belinkov, David Bau, and Aaron Mueller (Marks and Mueller corresponding).
Northeastern University, MIT, Technion, and an independent researcher.

## Impact

Dictionary learning had produced a vocabulary of interpretable features, and circuit analysis had produced wiring diagrams of how behaviors are computed - but the circuits were made of neurons and attention heads too coarse and polysemantic to read at scale.
This paper joins the two: circuits whose nodes are SAE features, found automatically and cheaply enough to run on thousands of behaviors.
Its headline application matters for alignment: when a classifier secretly relies on a signal its trainers didn't intend, a human can read the circuit, spot the culprit features, and delete them - even when no available labeled data could reveal the problem.

## Problem it solved

Circuit discovery had worked with coarse units - attention heads, whole MLP layers - which are polysemantic, so the resulting circuits were hard to interpret or use.
Methods with fine-grained, interpretable units, like linear probing, required the researcher to hypothesize the mechanism in advance.
And removing a model's reliance on a spurious signal normally required labeled data where that signal stops predicting the label - data that doesn't exist when the unintended signal is perfectly correlated with the intended one.

## Core idea

Treat every SAE feature, plus the SAE's leftover reconstruction error, as a node in the model's computation graph.
To find which nodes matter for a behavior - say, choosing "is" over "are" after "The teacher" - you would ideally swap each node's value one at a time and measure the effect, which is far too slow for millions of features.
Instead, approximate every node's effect at once with a gradient (attribution patching), or a more accurate averaged version (integrated gradients), and keep the nodes and edges above a threshold.
The result is a small graph of named features: "detects a plural subject," "marks the start of a relative clause," "promotes plural verbs."

**SHIFT** (Sparse Human-interpretable Feature Trimming, also expanded as Spurious in the paper) uses such a circuit to edit a model.
Train a profession classifier on biographies where every professor is male and every nurse female, so gender predicts the label perfectly.
Build the classifier's circuit, have a human read each feature, and ablate the ones that are about gender rather than profession - "promotes female-associated language," for instance.
After the ablation, and retraining the classification head on the same biased data, the classifier generalizes to male nurses and female professors without ever seeing a counterexample.

## How it's built

Pythia-70M with SAEs the authors trained on every sublayer, and Gemma-2-2B with the open Gemma Scope SAEs.
Circuits are evaluated on four subject-verb agreement tasks for **faithfulness** (how much of the behavior the circuit alone recovers when everything else is mean-ablated) and **completeness** (how much is lost when the circuit is removed), against circuits made of neurons.
SHIFT is tested on Bias in Bios against baselines including concept-bottleneck probing, random feature ablation, and "skylines" that pick features or neurons using the balanced labeled data SHIFT never sees.
A fully unsupervised pipeline clusters Pythia-70M's predictions on The Pile into behaviors and builds a circuit for each.

## Results

- Small feature circuits explain most of a behavior: circuits of around 100 (Pythia) or 500 (Gemma) nodes, counting SAE error nodes, recover the majority of performance, versus roughly 1,500 or 50,000 neurons to recover half; ablating a few feature nodes eliminates the behavior, where hundreds or thousands of neurons are needed.
- The agreement circuits are readable: both models detect the subject's number and either promote matching verbs directly or carry the number past a relative clause using clause-boundary features.
- SHIFT on Pythia-70M raises balanced-set profession accuracy from 61.9% to 88.5% and drops gender accuracy from 87.4% to 54.0% (near chance); retraining the head brings profession accuracy to 93.1%, matching an oracle trained on balanced data.
  Gemma-2-2B shows the same pattern after retraining (95.0% profession, 52.4% gender).
- Ablating random features does nothing; neurons were too uninterpretable to do SHIFT by hand, and even selecting neurons using the balanced data underperforms SHIFT.
- The unsupervised pipeline produces thousands of circuits, rediscovering induction and succession features and showing that clusters which look like one behavior are often several distinct mechanisms.

## Why it endures

It made SAE features usable for causal explanation, not just description, and is a precursor to the feature-level attribution graphs that [[Paper — Circuit Tracing - Revealing Computational Graphs in Language Models (2025)]] builds at much larger scale.
SHIFT is a template for an alignment use of interpretability the authors explicitly motivate: if "what the overseer approves of" and "what's actually good" predict training reward equally well, no behavioral data can tell them apart - but a human reading the model's features might.

## Limitations

The authors' own:
- The method needs trained SAEs, an expensive upfront cost, and parts of the model the SAEs don't capture stay uninterpretable.
- Much of the evaluation is qualitative; evaluating dictionaries and circuits without downstream tasks is hard.
- Feature labeling is subjective and may vary between annotators and tasks.

## Relations

- Builds on the SAE features of [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]] (see [[Method — Sparse Autoencoder (SAE)]]) and the circuit methodology of [[Paper — Interpretability in the Wild - a Circuit for Indirect Object Identification in GPT-2 small (2022)]], replacing heads with features.
- Rediscovers at the feature level the induction mechanism of [[Paper — In-context Learning and Induction Heads (2022)]] (see [[Concept — Induction heads]]).
- Cited by [[Paper — Scaling and Evaluating Sparse Autoencoders (2024)]] as showing SAE features can find sparse circuits.
- First author Marks later co-led (with Treutlein) [[Paper — Auditing Language Models for Hidden Objectives (2025)]], which puts SAE features to work in a blind alignment audit.
- SHIFT's motivating worry - overseer approval and desirable behavior being indistinguishable in training - is drawn from [[Paper — The Alignment Problem from a Deep Learning Perspective (2022)]] and Casper et al.'s *Open Problems and Fundamental Limitations of RLHF*.

## Up

- [[Cluster — Mechanistic interpretability]]
