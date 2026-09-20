---
type: paper
status: mature
updated: 2026-09-19
summary: "Trains a sparse autoencoder on a one-layer transformer's MLP activations and extracts thousands of far-more-interpretable-than-neurons features, giving the first compelling proof that dictionary learning can recover real structure from superposition."
cluster: ["Mechanistic interpretability"]
sources: ["https://transformer-circuits.pub/2023/monosemantic-features/index.html"]
tags: [interpretability, mechanistic-interpretability, superposition, dictionary-learning, safety]
aliases: ["Bricken et al. 2023"]
---

# Paper — Towards Monosemanticity: Decomposing Language Models With Dictionary Learning (2023)

## Metadata

Bricken, Templeton, Batson, Chen, Jermyn, Conerly, Turner, Anil, Denison, Askell, Lasenby, Wu, Kravec, Schiefer, Maxwell, Joseph, Hatfield-Dodds, Tamkin, Nguyen, McLean, Burke, Hume, Carter, Henighan & Olah, Anthropic, published October 4, 2023 on the Transformer Circuits Thread (transformer-circuits.pub, no arXiv id).

## Impact

The first compelling, causally-validated demonstration that [[Concept — Superposition]] can actually be reversed in a real trained model, not just a hand-designed toy one.
Training a sparse autoencoder on the 512-neuron MLP layer of a tiny one-layer transformer recovers thousands of "features" - directions in activation space - that are dramatically more interpretable than the neurons themselves, causally steer generation when clamped, and recur across independently trained models.
This is the direct empirical execution of "Approach 2: finding an overcomplete basis" from [[Paper — Toy Models of Superposition (2022)]], carried out after that paper's own authors concluded, via a clean negative result described below, that the alternative strategy - engineering models to simply not use superposition - cannot work even in principle.

## Problem it solved

[[Paper — Toy Models of Superposition (2022)]] left three candidate strategies for "solving superposition" on the table, untested against a real model: build models without superposition, apply dictionary learning to recover an overcomplete feature basis after training, or some hybrid.
This paper's first contribution is closing off the first option with a clean counterexample.
Consider a single neuron trained on four mutually exclusive features (A/B/C/D), each predicting a distinct next token, with the neuron's output binary (fires or doesn't).
If it fires only on A, ignoring B/C/D, the model achieves a certain cross-entropy loss.
If instead it fires on *both* A and B, predicting a uniform mixture, the loss is strictly *lower* - even though the neuron is now polysemantic and there is no superposition at all (only one neuron, firing in only one binary state).
Cross-entropy loss, it turns out, actively prefers polysemantic ambiguity over monosemantic confidence in cases like this, so maximal activation sparsity does not guarantee monosemantic neurons.
This meant the only remaining path was to try dictionary learning on activations directly - which nobody had yet gotten to work convincingly on an actual language model rather than a synthetic toy.

## Core idea

Train a **sparse autoencoder** (a simple, one-hidden-layer, overcomplete autoencoder: ReLU encoder, linear decoder, MSE reconstruction loss plus an L1 penalty on the hidden activations) to reconstruct a trained transformer's MLP activations, using far more hidden units ("features") than there are neurons - up to 256x more in the largest runs.
If superposition is real, this setup should recover the individual, sparsely-activating "true features" the MLP is packing into its comparatively few neurons, the same way sparse coding recovers a signal from a compressed sensing measurement.

Several architectural choices, discovered empirically, turned out to matter: untying the encoder and decoder weights (unlike a standard autoencoder), since similar features need different encoder directions to avoid crosstalk between them even though their "true" dictionary directions are nearly identical; and periodically "resampling" dead hidden units by reinitializing them to fit whichever datapoints the autoencoder currently reconstructs worst, which substantially increases the number of live, useful features recovered.

For a handful of cleanly-identifiable features (responding to Arabic script, DNA sequences, base64 strings, Hebrew script), the paper establishes five things: the feature activates with high **specificity** for its hypothesized context; it activates with high **sensitivity** across that context; it has the causally-appropriate **downstream effect**, confirmed by directly ablating the feature (removing its contribution and watching predictions get worse in exactly the expected way) and by "pinned feature sampling" (artificially clamping the feature to a high value and watching the model actually start generating Arabic, DNA-like, or base64 text); it is **not secretly one neuron** in disguise (the most-correlated single neuron for each feature is a weak, diffuse match at best); and it is **universal** - an independently trained sister model, differing only in random seed, develops an almost identical feature (activation correlations of 0.72-0.98).

## How it's built

Two matched one-layer transformers (512-neuron MLP, ReLU) are trained on 100 billion tokens of the Pile, differing only in random seed.
Sparse autoencoders of increasing size (512 up to 131,072 hidden units, i.e. 1x to 256x the neuron count) are trained on 8 billion sampled MLP activation vectors from one of the transformers, with the sister transformer used specifically to test whether recovered features are universal rather than an artifact of one particular training run.

## Results

Both blinded human scoring and Claude-based automated interpretability find learned features dramatically more interpretable than neurons: the median human interpretability-rubric score is 12 (out of 14) for features versus 0 for neurons - the median neuron scored so low the human annotator could not even form a hypothesis about what it represented.
The learned features recover 79% of the MLP layer's contribution to the model's loss in a mid-sized run, rising to 94.5% in the largest (131,072-feature) run.
As dictionary size grows, single coarse features reliably **split** into families of more specific siblings (one base64 feature becomes three: one for base64 letters, one for base64 digits, one specifically for base64-encoded ASCII text) whose directions cluster tightly together geometrically - evidence that "true" features aren't isolated points but come in correlated families that superposition packs unusually densely, updating the simpler "evenly-spaced, isotropic" picture of superposition geometry from the toy-model paper.
Neighboring features chain into small, interpretable **"finite state automata"**: a four-feature loop that generates well-formed HTML tags, or a chain that reproduces open-source license boilerplate language almost verbatim - an instance of memorization living inside superposition.

## Why it endures

This paper is what opens the sparse-autoencoder / dictionary-learning research program that comes to dominate later mechanistic interpretability, including its own much larger-scale sequel, [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]].
The paper is explicit that it was part of a close race of independent discovery rather than a lone breakthrough, citing a parallel line of interim reports and a concurrent manuscript reaching very similar conclusions, and an external replication (by Neel Nanda) appended to the paper itself finding the core results hold on an independently trained open-source model.
Its own explicitly flagged "most important" open question - whether this approach can be scaled to frontier-size models, given that a highly overcomplete autoencoder on a real model's MLP width could itself have tens of billions of parameters - is the exact question its sequel exists to answer.

## Limitations

Every result comes from a single, deliberately tiny one-layer, 512-neuron transformer, explicitly chosen as a testbed rather than a claim about frontier models.
Even the largest, most overcomplete dictionary only recovers 94.5% (not 100%) of the MLP's loss contribution, and the paper is candid that whether "the correct number of true features" is even a well-posed question remains open, given the feature-splitting phenomenon.
A substantial fraction of learned hidden units in many runs form an "ultralow density cluster" that the authors believe is a training artifact rather than real model structure, but which they admit they do not fully understand or know how to reliably filter.
The paper also raises, without resolving, a puzzle its own results surface: why the model learns hundreds of narrow "token-in-context" features (many different features for the word "the," one per topic) rather than more economical, compositional features - leaving open whether this reflects the model's real computation or an artifact of the sparsity penalty pushing dictionary learning toward an unnecessarily local code.

## Relations

- Directly executes "Approach 2: finding an overcomplete basis," proposed but not attempted in [[Paper — Toy Models of Superposition (2022)]], after ruling out the architectural alternative with a clean counterexample.
- Gives [[Concept — Superposition]] its first empirical, causally-validated reversal in a real (if tiny) trained model.
- Introduces [[Method — Sparse Autoencoder (SAE)]] as the field's standard tool for recovering interpretable features from superposition.
- Its explicitly flagged scaling question is answered directly by its own sequel, [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]].

## Up

- [[Cluster — Mechanistic interpretability]]
