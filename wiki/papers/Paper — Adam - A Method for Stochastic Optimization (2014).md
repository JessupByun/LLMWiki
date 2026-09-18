---
type: paper
status: in-progress
updated: 2026-09-18
summary: "Adam - an adaptive first-order optimizer that tracks running estimates of the gradient's first and second moments with bias correction, combining AdaGrad's sparse-gradient handling with RMSProp's non-stationary-objective handling, and became the default optimizer for training neural networks."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Adam - A Method for Stochastic Optimization (2014).pdf"]
tags: [optimization, sgd, deep-learning]
aliases: ["arxiv:1412.6980", "Kingma & Ba 2014", "Adam"]
---

# Paper — Adam - A Method for Stochastic Optimization (2014)

Full title: **Adam: A Method for Stochastic Optimization**.

## Metadata

- **arXiv:** 1412.6980 (v9, 2017-01-30)
- **Venue:** ICLR 2015; printed on the paper itself.
- **Authors:** Diederik P. Kingma (University of Amsterdam, OpenAI), Jimmy Lei Ba (University of Toronto)

## Impact

Introduced Adam, an adaptive first-order gradient optimization algorithm that computes an individual, adaptive learning rate for every parameter from running estimates of the gradient's first moment (mean) and second moment (uncentered variance).
It combined the sparse-gradient handling of AdaGrad with the non-stationary-objective handling of RMSProp, and added a bias-correction term neither predecessor had.
Adam became, almost immediately, the default optimizer for training neural networks - a decade later it (or its close variant AdamW) remains the default choice underlying the overwhelming majority of deep learning training runs, including essentially every large model in this wiki.

## Core idea: adaptive, bias-corrected per-parameter steps

For every parameter, Adam keeps two running exponential moving averages of its gradient: the first moment (an estimate of the mean gradient direction, similar to classical momentum) and the second moment (an estimate of the mean squared gradient magnitude, similar to RMSProp/AdaGrad).
The update divides the first by the square root of the second, which means a parameter whose gradient has been small but consistent gets a comparatively large effective step, while a parameter whose gradient has been large but erratic gets a comparatively small one - exactly the behavior a loss surface with wildly different curvature across parameters calls for.

The paper's own derivation motivates why bias correction matters: both moving averages are initialized at zero, so early estimates are biased toward zero, especially with the recommended decay rates close to 1.
Adam explicitly rescales by a `(1 - β^t)` factor to correct for this.
The paper shows empirically (section 6.4, using a VAE as the test model) that omitting this correction - which is exactly what RMSProp does - causes instability specifically in the sparse-gradient regime Adam is designed to handle well, a concrete demonstration that the correction isn't a minor detail but the fix for a real failure mode.

## Why it endures

Adam's effective step size is approximately bounded by the stepsize hyperparameter regardless of how the gradient's raw magnitude varies, and it shrinks automatically as training approaches an optimum (the paper calls this a form of "automatic annealing") - properties that made it unusually robust across a wide range of architectures and problem types with comparatively little hyperparameter tuning.
That robustness is exactly why it displaced hand-tuned SGD-with-momentum as the near-universal default, and why it sits underneath the training recipe of essentially every large-scale model that appears later in this wiki.

## Relations

- Directly combines and improves on AdaGrad (Duchi et al. 2011, not yet ingested) and RMSProp (Tieleman & Hinton, unpublished lecture notes, not yet ingested), adding the bias-correction term neither has.
- Used directly to train [[Paper — Auto-Encoding Variational Bayes (2013)]]'s architecture in this paper's own bias-correction ablation experiment.
- The default or near-default optimizer underlying the training recipe of nearly every later paper in this wiki, from [[Paper — Attention Is All You Need (2017)]] onward.

## Up

- [[Cluster — Foundational canon]]
