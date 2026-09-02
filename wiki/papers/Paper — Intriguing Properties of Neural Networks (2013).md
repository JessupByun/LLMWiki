---
type: paper
status: in-progress
updated: 2026-08-07
summary: "Discovered adversarial examples - imperceptible perturbations that reliably fool networks - and showed the smoothness assumption behind kernel methods fails for deep nets."
cluster: ["Adversarial robustness & security", "Foundational canon"]
sources: ["raw/papers/Paper — Intriguing Properties of Neural Networks (2013).pdf"]
tags: [adversarial-examples, robustness, security, interpretability, cnn]
aliases: ["arxiv:1312.6199", "Szegedy et al. 2013"]
---

# Paper — Intriguing Properties of Neural Networks (2013)

## Metadata

- **arXiv:** 1312.6199 (v4, 2014-02-19)
- **Venue:** ICLR 2014. No venue is printed in the PDF itself; confirmed externally via DBLP.
- **Authors:** Christian Szegedy, Wojciech Zaremba, Ilya Sutskever, Joan Bruna, Dumitru Erhan, Ian Goodfellow, Rob Fergus. Affiliations span Google, NYU, University of Montreal, and Facebook.

## Impact

This paper discovered **adversarial examples** and named them.
It showed that a perturbation too small for a human to see can be computed to make a network confidently output any class you choose, and that the same perturbation often transfers to *other* networks trained with different hyperparameters or even on disjoint data.
That second finding is what made this a security result rather than a curiosity: the vulnerability is a property of the learned function class, not of one unlucky model, so an attacker doesn't need your weights.
Every jailbreak, prompt-injection, and adversarial-robustness line of work descends from this observation.

## Problem it solved

It wasn't solving a problem so much as puncturing an assumption.
Deep nets were understood to encode a "non-local generalization prior," with the implicit companion belief that **local** generalization simply works: for a small enough radius around a training input, everything inside gets the right label.
The authors state their result as exactly the negation of that: "for deep neural networks, the smoothness assumption that underlies many kernel methods does not hold" (p4).

## Two distinct findings

These get conflated constantly. They are separate.

**1. Semantic information lives in the space, not in individual units.**
The standard interpretability move at the time was to find images maximizing a single hidden unit's activation and read off what that neuron "means."
The authors show that maximizing along a *random direction* in the same layer yields images that look just as semantically coherent.
So "the natural basis is not better than a random basis for inspecting the properties of φ(x)," which "puts into question the notion that neural networks disentangle variation factors across coordinates" (p3).
This is the direct ancestor of the superposition and feature-basis debates in mechanistic interpretability, and note the evidence here is qualitative - figure panels, no metric.

**2. Adversarial examples exist and transfer.**
Networks "learn input-output mappings that are fairly discontinuous," so an "imperceptible non-random perturbation" can "arbitrarily change the network's prediction" (p1, p2).

## Core idea: minimum-distortion targeted attack

Given an image `x` and a **target label** `l` you want the model to output, solve:

> minimize ‖r‖₂ subject to `f(x+r) = l` and `x+r ∈ [0,1]^m`

In words: find the smallest pixel change that lands the image in the class of your choosing, while keeping it a valid image.
That exact problem is intractable, so they relax it to minimizing `c·|r| + loss_f(x+r, l)` and **line-search over `c`** until the classification actually flips, solving each instance with **box-constrained L-BFGS** (p5).

How small is small: for AlexNet the "Average distortion based on 64 examples is 0.006508," and every perturbed image in the figure is classified as "ostrich, Struthio camelus" (p6).
The perturbation has to be magnified 10x to be visible in print at all.
The calibration that makes this land: Gaussian noise at stddev 1.0 leaves images "hardly readable" yet still correctly classified 51% of the time, while the adversarial examples are "never classified correctly" at a fraction of that magnitude (p7).

**This paper does not contain FGSM**, the linearity hypothesis, or ε-ball/L∞ framing.
Those arrive in the follow-up work; here the method is an L-BFGS optimization and the explanation is different (below).

## Results (highlights)

- Attacked MNIST models (linear softmax, sigmoid MLPs, an autoencoder-based classifier), **AlexNet** on ImageNet, and **QuocNet**. Minimum average distortion to drive MNIST models to 0% accuracy ranged 0.058-0.14 (Table 1, p7).
- **Cross-model transfer:** adversarial examples built for one MNIST architecture induce errors in others - e.g. examples from FC10(1) cause 71.9%/76.2%/48.1%/47%/34.4% error across the other models, against Gaussian-noise baselines of 0-18% (Table 2, p7).
- **Cross-training-set transfer**, with an important caveat. At native distortion, transfer between models trained on **disjoint** halves of MNIST is weak: 5.9%, 5.1%, 8.2% error against a matched-noise baseline of 2.2-2.6% (Table 4, p8). It becomes dramatic only after amplifying the perturbation by ~40%, reaching 43%, 22%, and 27%. Transfer is also **asymmetric** even between two models trained on the same half. The authors' own hedge is accurate: examples "remain hard for models trained even on a disjoint training set, although their effectiveness decreases considerably" (p8).
- **Adversarial training as a regularizer, preliminarily:** a 100-100-10 net reached **below 1.2% test error** vs 1.6% with weight decay alone and ~1.3% with tuned dropout (p6). Critically, this was **not** input-space adversarial training - it only worked "by generating adversarial examples for each layer outputs," and examples from higher layers "seemed to be significantly more useful than those on the input or lower layers" (p6).

## Authors' explanation

Two threads, both hedged.

The **dense-pockets** account: adversarial examples are "low-probability (high-dimensional) 'pockets' in the manifold, which are hard to efficiently find by simply randomly sampling" (p4).
Resolving the apparent paradox of good generalization alongside universal vulnerability: the adversarial set "is of extremely low probability, and thus is never (or rarely) observed in the test set, yet it is dense (much like the rational numbers), and so it is found near every virtually every test case" (p10).

The **spectral** account: bound each layer's Lipschitz constant by its operator norm, so the whole network's is the product.
Computing these for AlexNet shows "instabilities can appear as soon as in the first convolutional layer" (p9).
They are careful about the direction of the implication: "large bounds do not automatically translate into existence of adversarial examples; however, small bounds guarantee that no such examples can appear" (p9).

## Limitations

- The unit-vs-space finding is supported only by figure panels. No metric or number is reported for it anywhere.
- The spectral analysis "doesn't attempt to explain why these examples generalize across different hyperparameters or training sets" (p9), which is the security-relevant half.
- Adversarial-training results are explicitly "preliminary" and MNIST-only, on a non-standard per-layer scheme (p5, p6).
- No fooling *rate* is reported for AlexNet or QuocNet; all rate tables are MNIST-only.
- No convolutional MNIST results: "we do not have results for convolutional models yet" (p6).
- Frequency is unknown and flagged as future work: "we don't have a deep understanding of how often adversarial negatives appears" (p10), and the dense-pockets story is offered as a "Possible explanation."

## Relations

- Concept hub: [[Concept — Adversarial examples]].
- Attacks the architecture documented in [[Concept — Convolutional neural networks (CNNs)]], and AlexNet specifically - the model from [[Paper — ImageNet Classification with Deep CNNs (2012)]] is one of its three targets. Sutskever is an author on both.
- Finding 1 is an early precursor to the interpretability question of whether neurons are the right unit of analysis, which later becomes the superposition literature.

## Up

- [[Cluster — Adversarial robustness & security]]
- [[Cluster — Foundational canon]]
