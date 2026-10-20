---
type: paper
status: mature
updated: 2026-10-20
summary: "Proves a tight guarantee for randomized smoothing - classify an input by taking the base classifier's majority vote over Gaussian-noised copies of it - showing the smoothed classifier's prediction provably cannot change within an L2 radius set by the noise level and how lopsided the vote is, and uses it to set the state of the art for certified robustness on full ImageNet (49% certified top-1 accuracy at radius 0.5)."
cluster: ["Adversarial robustness & security"]
sources: ["raw/papers/adversarial-robustness-and-security/Paper — Certified Adversarial Robustness via Randomized Smoothing (2019).pdf"]
tags: [adversarial-robustness, certified-defense, randomized-smoothing, provable-robustness]
aliases: ["arxiv:1902.02918", "Cohen et al. 2019", "randomized smoothing"]
---

# Paper — Certified Adversarial Robustness via Randomized Smoothing (2019)

## Metadata

arXiv:1902.02918 (v2, Jun 2019; originally Feb 2019); ICML 2019.
Jeremy Cohen, Elan Rosenfeld, and J. Zico Kolter (Carnegie Mellon University; Kolter also Bosch Center for AI).

## Impact

Adversarial defense had become a cycle of proposals and breaks: heuristic defenses kept falling to stronger attacks, and even the best empirical defense, adversarial training, can only say that a particular attack failed, not that no attack exists.
Certified defenses offered proofs instead, but none that certified the network itself scaled to networks large enough for real tasks like ImageNet.
This paper makes a simple, architecture-agnostic trick - averaging predictions over random noise - into a defense with a tight mathematical guarantee that works with any classifier, however large.
It became a widely used baseline for certified robustness and the clearest example of trading a little accuracy for a proof rather than a demonstration.

## Problem it solved

Certification methods either solved the verification problem exactly (SMT solvers or mixed-integer linear programming, which didn't scale beyond networks of around 100,000 activations) or bounded it conservatively (Lipschitz bounds, convex relaxations), which were loose on expressive networks and tied to specific architectures such as ReLU feed-forward nets.
Randomized smoothing had been proposed just before by Lecuyer et al. (as "PixelDP," via differential privacy) and Li et al., but both guarantees were loose - the smoothed classifier was provably more robust than either could certify.

## Core idea

Don't certify the network; certify a vote.
Given an image, add Gaussian noise to it thousands of times, run each noisy copy through any ordinary classifier, and output whichever class wins the most votes.
Now imagine an adversary nudging the image by a small amount.
The cloud of noisy copies shifts only slightly, so the vote shares can only shift slightly - and if one class was winning by a wide margin, a small nudge cannot flip the outcome.
The paper's theorem makes this exact: if the top class gets probability at least pA and the runner-up at most pB, the smoothed prediction is guaranteed constant within an L2 radius of σ/2 × (Φ⁻¹(pA) − Φ⁻¹(pB)), where σ is the noise level and Φ⁻¹ the inverse Gaussian CDF.
A second theorem shows this radius is the best possible given only those vote probabilities: Gaussian smoothing naturally certifies exactly an L2 ball, nothing larger.

The noise is far larger than the adversarial perturbation - one interpretation is that big random noise "drowns out" small adversarial noise.

## How it's built

**Base classifier.** Trained with Gaussian noise augmentation at the same σ, so it learns to classify noisy images; otherwise it would never see such inputs.
**Monte Carlo algorithms.** The vote probabilities can't be computed exactly for a neural network, so PREDICT estimates the winning class from a modest number of samples and abstains if the result is not statistically significant, and CERTIFY uses a separate, much larger sample to lower-bound pA and return a radius, wrong with probability at most α (0.1% in the experiments).
**Experiments.** A 110-layer ResNet base classifier on CIFAR-10 and a ResNet-50 on ImageNet, at several noise levels, compared against the bounds of Lecuyer et al. and Li et al. and against certified baselines including Wong et al.'s convex-relaxation approach.

## Results

- On ImageNet, smoothed classifiers reach 49% certified top-1 accuracy at L2 radius 0.5 (127/255), 37% at 1.0, 19% at 2.0, and 12% at 3.0, with clean accuracy of 67% at the smallest of those noise levels.
- Randomized smoothing, including the earlier PixelDP, is the only certified defense shown feasible on full-resolution ImageNet at the time.
- The tight bound certifies higher accuracies than the earlier smoothing guarantees on the same models.
- On CIFAR-10, smoothing with a large ResNet beats Wong et al.'s certified method at every radius, and even with Wong et al.'s own small architecture it wins at all but the smallest radii, which the authors attribute to that method over-regularizing its networks.
- The noise level σ sets a robustness-accuracy tradeoff: low σ certifies small radii at high accuracy, high σ certifies larger radii at lower accuracy.
- Certification is expensive (15 seconds per CIFAR-10 image, 110 seconds per ImageNet image), but prediction alone is cheap (0.15 seconds on ImageNet with 100 samples); it abstains 12% of the time at 100 samples, falling to 1% at 10,000.

## Why it endures

It made certified robustness practical at scale by moving the guarantee from the network's internals to a statistical property of its outputs, so any architecture, however large, can be certified.
The idea generalized: other noise distributions certify other threat models, smoothing has been applied beyond images, and later work on certifying language models against adversarial prompts borrows the same structure of voting over randomly perturbed copies of the input.
It also clarifies the honest price of a proof: certified accuracy stays well below clean accuracy, and only within the specific norm ball the noise distribution induces.

## Limitations

The authors' own:
- The guarantee covers only L2 perturbations; they suspect other noise distributions might certify robustness to other perturbation sets, such as general Lp norm balls, but leave it open.
- The prediction and radius can only be computed approximately, by Monte Carlo sampling, so the guarantee holds with high probability rather than deterministically, and PREDICT can abstain - and an adversary may be able to force abstention.
- Certification needs many samples per input and is computationally costly.
- Training the base classifier with plain Gaussian augmentation is likely not optimal; training to directly maximize the smoothed classifier's certified accuracy might do better.

## Relations

- The provable counterpart to the empirical adversarial training of [[Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)]]: that paper's robustness is measured against the strongest known attack, this one's is proved against any attack within the radius (see [[Concept — Adversarial examples]]).
- Answers the attack-defense cycle that began with [[Paper — Intriguing Properties of Neural Networks (2013)]], at the cost of certifying only one norm ball.
- Its predecessor, Lecuyer et al.'s PixelDP, derived the guarantee from [[Concept — Differential privacy]]; this paper's tighter analysis drops the privacy framing.
- A certified alternative to the adversarial training [[Paper — Explaining and Harnessing Adversarial Examples (2014)]] made practical.

## Up

- [[Cluster — Adversarial robustness & security]]
