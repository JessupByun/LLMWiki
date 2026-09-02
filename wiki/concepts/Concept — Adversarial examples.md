---
type: concept
status: in-progress
updated: 2026-08-07
summary: "Inputs perturbed imperceptibly but deliberately to cause misclassification, which transfer across models and so constitute a real attack."
cluster: ["Adversarial robustness & security"]
sources: []
tags: [adversarial-examples, security, robustness]
aliases: []
---

# Concept — Adversarial examples

## Definition

An input modified by a small, deliberately computed perturbation that causes a model to produce a wrong output, where "small" means below the threshold a human would notice.

The essential contrast is with random noise.
Noise of much *larger* magnitude typically leaves classification intact; the adversarial perturbation is effective precisely because it is chosen with respect to the model's geometry, not because it is large.
Named and first demonstrated in [[Paper — Intriguing Properties of Neural Networks (2013)]].

## Why they exist

The finding that made this alarming rather than merely odd: deep networks violate the **local smoothness assumption**.
It had been taken for granted that within a small enough radius of a correctly-classified training point, everything is also correctly classified. That is false, and it is false everywhere, not in rare spots.

Explanations have shifted over time, which is worth tracking rather than collapsing:

- **Dense low-probability pockets** (the original account, and the only one sourced here). Adversarial inputs occupy regions of vanishingly small measure - so you never hit them by sampling - yet are "dense (much like the rational numbers)," so one sits near virtually every real input. The authors of the 2013 paper offer this as a "Possible explanation" and concede their accompanying spectral analysis "doesn't attempt to explain why these examples generalize across different hyperparameters or training sets."
- **Excessive linearity.** Later work (Goodfellow et al. 2014, **not yet ingested**) is generally read as superseding the pockets account, arguing networks are locally near-linear so many small coordinated input changes sum to a large activation change, which would also explain transfer. Flagged as a reading lead; this page will get a proper Tension/update note on both sides once that paper is in the wiki.

## Transferability, which is the security-relevant property

A perturbation crafted against one model often fools *other* models - different architectures, different hyperparameters, even models trained on disjoint data.
This is what turns a curiosity into a threat model, because an attacker needs no access to the target's weights: craft against a surrogate, deploy against the real system (a "black-box transfer attack").

Calibration matters here and is often overstated in summaries. In the original paper, cross-training-set transfer at *native* distortion was weak (single-digit error rates against a low-single-digit noise baseline) and only became dramatic once the perturbation was amplified by ~40%. Transfer was also asymmetric between model pairs. The effect is real and the direction is right; the original evidence for the strongest version of the claim was modest.

## Why it endures

The phenomenon relocated rather than resolved.
Jailbreaks and prompt injection against language models are the same structure in token space: a deliberately optimized input that drives a model outside its intended behavior, frequently transferring across models.
Robustness also turned out to trade against accuracy, and adversarial training - originally proposed in the 2013 paper as a side benefit - became the main practical defense.

## Relations

- Origin: [[Paper — Intriguing Properties of Neural Networks (2013)]].
- First demonstrated against [[Concept — Convolutional neural networks (CNNs)]], including AlexNet.
- Compare [[Concept — Reward hacking]]: both are an optimizer exploiting a region where a learned function misbehaves, differing in whether the exploitation is adversarial by intent.

## Up

- [[Cluster — Adversarial robustness & security]]
