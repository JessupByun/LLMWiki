---
type: paper
status: mature
updated: 2026-09-24
summary: "Introduces differentially private stochastic gradient descent (DP-SGD) - clip each per-example gradient's L2 norm, average, and add calibrated Gaussian noise before each parameter update - and the moments accountant, a tighter privacy-loss accounting method that lets deep, non-convex neural networks be trained under a modest, meaningful differential-privacy budget for the first time, reaching 97% accuracy on MNIST and 73% on CIFAR-10 at (8, 10^-5)-differential privacy."
cluster: ["ML security & privacy", "Foundational canon"]
sources: ["raw/papers/ml-security-and-privacy/Paper — Deep Learning with Differential Privacy (2016).pdf"]
tags: [privacy, differential-privacy, dp-sgd, ml-security]
aliases: ["arxiv:1607.00133", "Abadi et al. 2016", "DP-SGD"]
---

# Paper — Deep Learning with Differential Privacy (2016)

## Metadata

A preliminary version appeared in ACM CCS 2016; this is the full version, arXiv:1607.00133 (Oct 2016).
Martin Abadi, Andy Chu, Ian Goodfellow, H. Brendan McMahan, Ilya Mironov, Kunal Talwar, Li Zhang.
Google, with Ian Goodfellow's contribution made while at OpenAI.

## Impact

[[Paper — Membership Inference Attacks Against Machine Learning Models (2017)]] shows a trained model's own predictions can leak whether a specific record was in its training set; this paper, predating that one, supplies the principled fix.
It adapts differential privacy - a formal, decades-old guarantee that an algorithm's output distribution barely changes whether or not any single individual's data was included - to deep neural network training for the first time at a genuinely usable privacy budget, by combining per-example gradient clipping and calibrated noise addition (differentially private SGD) with a new, much tighter method for tracking accumulated privacy loss across many training steps, the **moments accountant**.
The result: a standard, non-convex deep network can be trained to 97% MNIST accuracy and 73% CIFAR-10 accuracy while satisfying a modest, single-digit-epsilon privacy guarantee, at only a modest cost in accuracy, training time, and code complexity.

## Problem it solved

Differential privacy composes cleanly in theory - if each step of a computation is (epsilon, delta)-differentially private, standard composition theorems bound the privacy cost of the whole sequence - but applying the *strong composition theorem*, the best previously available generic bound, to the thousands of gradient-descent steps a deep network needs produces a privacy budget so large it's essentially meaningless (the paper's own example: an epsilon around 9.34, versus 1.26 with their new method, for an identical training run).
Prior differentially private machine learning work therefore stayed confined to convex objectives with far fewer parameters, where a usable epsilon could still be reached; nobody had shown deep, non-convex networks with tens of thousands to millions of parameters could be trained under a genuinely tight privacy budget at all.

## Core idea

Ordinary SGD already estimates a loss gradient from a random batch of examples and averages it; the paper's differentially private version, **DP-SGD**, adds exactly two steps to this before each parameter update.
First, **clip**: bound the influence any single example can have by rescaling each individual example's gradient (computed per-example, not just per-batch) so its L2 norm never exceeds a threshold C - this is what makes the *sensitivity* of the averaged gradient to any one training example provably bounded.
Second, **add noise**: add Gaussian noise, scaled to that same clipping bound, to the summed clipped gradients before averaging and taking the descent step - the actual privacy-preserving randomization, following the standard differential-privacy recipe of calibrating additive noise to a function's worst-case sensitivity.
The harder problem is accounting for how much privacy is spent, cumulatively, across the full training run.
The paper's **moments accountant** tracks the log of the moment-generating function of the "privacy loss" random variable at each step, rather than tracking a bare (epsilon, delta) pair, and composes those moments by simple addition across steps - a strictly finer-grained object than the (epsilon, delta) pair itself, which loses information the moments retain.
Converting the final accumulated moment bound back into an (epsilon, delta) guarantee via a standard tail bound yields a substantially tighter final epsilon than composing (epsilon, delta) guarantees directly ever could, because information that composition would otherwise discard at each step is preserved and only converted to the coarser form once, at the very end.

## How it's built

Implemented in TensorFlow: a `PrivacyAccountant` component tracks cumulative spending via the moments accountant, and a `Sanitizer` component performs the per-example gradient clipping and noise addition, wrapped together in a `DPSGD_Optimizer` that plugs into ordinary TensorFlow training loops with only modest code changes.
Per-example gradients (rather than only per-batch averages) are computed via a dedicated TensorFlow operator.
The paper also introduces differentially private PCA as an optional input-dimensionality-reduction preprocessing step (projecting inputs onto principal directions computed from a noised covariance matrix), which both improves accuracy and cuts training time roughly tenfold on MNIST by shrinking the input from 784 to 60 dimensions.
Evaluated on MNIST (a simple feedforward network with an optional PCA layer) and CIFAR-10 (a convolutional network whose convolutional layers are pretrained on the public CIFAR-100 dataset, with only the fully-connected layers trained under differential privacy).

## Results

On MNIST, the moments accountant achieves (2.55, 10^-5)-differential privacy for a training run the strong composition theorem could only certify at the much weaker (24.22, 10^-5) - a difference the paper highlights as evidence the moments accountant is doing real, not merely incremental, work.
At that same modest privacy budget, the model reaches 90%, 95%, and 97% test accuracy for epsilon values of 0.5, 2, and 8 respectively (all at delta=10^-5), with the private-versus-non-private accuracy gap only about 1.3 percentage points at the largest budget tested.
On CIFAR-10, accuracy reaches 67%, 70%, and 73% for epsilon values of 2, 4, and 8 respectively (a different set of epsilon values than the MNIST experiment), with the private-versus-non-private gap now a much larger 7 percentage points - a gap the paper explicitly leaves open as future work rather than resolving.
A counterintuitive finding: adding more hidden units to the network does not decrease accuracy despite the added noise, evidence that larger networks are more tolerant of the added noise, encouraging given that large networks are the practical norm.

## Why it endures

The moments accountant becomes this paper's most durable individual contribution, later generalized into Renyi differential privacy (cited in the paper's own related-work discussion as concurrent, related work) and adopted as the default privacy-accounting method in essentially every subsequent differentially-private deep learning system.
DP-SGD itself - clip per-example gradients, add calibrated noise, track privacy with a tight composition method - becomes the field's standard recipe for training any deep model under a formal privacy guarantee, essentially unchanged in later frameworks (TensorFlow Privacy, Opacus) beyond accounting refinements.

## Limitations

The CIFAR-10 accuracy gap (7 percentage points) is left as an open problem the paper does not resolve, in contrast to the much smaller MNIST gap.
The convolutional layers used for CIFAR-10 are pretrained non-privately on the related CIFAR-100 dataset and never trained under differential privacy at all - only the fully-connected layers get the private treatment - so the paper's own headline CIFAR-10 result does not demonstrate end-to-end private training of every layer.
The moments accountant's numerical integration is specific to the Gaussian noise mechanism and the particular random-sampling composition studied; the paper does not claim the technique generalizes to arbitrary noise mechanisms without further derivation.

## Relations

- Directly answers the threat [[Paper — Membership Inference Attacks Against Machine Learning Models (2017)]] demonstrates empirically: a model trained by this paper's method is secure against membership inference (and any other attack relying solely on the model's own behavior) by construction, rather than by the ad hoc, only-partially-effective mitigations that paper evaluates instead.
- Builds on the formal definition of (epsilon, delta)-differential privacy from Dwork et al. (not yet ingested), extending it from convex learning problems and simpler statistical queries to non-convex deep neural network training for the first time at a usable privacy budget.
- Introduces [[Concept — Differential privacy]] to this wiki and its own [[Method — Differentially Private SGD (DP-SGD)]], both anchoring [[Cluster — ML security & privacy]].

## Up

- [[Cluster — ML security & privacy]]
- [[Cluster — Foundational canon]]
