---
type: concept
status: in-progress
updated: 2026-09-24
summary: "A formal guarantee that a randomized algorithm's output distribution changes only slightly whether or not any single individual's data is included in its input - bounding, by construction, how much any one training example can influence a model's learned behavior."
cluster: ["ML security & privacy"]
sources: []
tags: [privacy, differential-privacy, ml-security]
aliases: ["(epsilon, delta)-differential privacy"]
---

# Concept — Differential privacy

## Definition

A randomized mechanism M satisfies (epsilon, delta)-differential privacy if, for any two "adjacent" inputs d and d' (datasets differing in exactly one record) and any set of possible outputs S: `Pr[M(d) in S] <= e^epsilon * Pr[M(d') in S] + delta`.
Informally: whether or not any single person's data is included barely changes what the mechanism is likely to output, so an observer of the output learns almost nothing about whether that person's data was used at all.
Smaller epsilon means a stronger guarantee (the two probabilities are forced closer together); delta is a small slack term allowing the strict bound to fail with very low probability.

## Why it matters

[[Paper — Deep Learning with Differential Privacy (2016)]] applies this guarantee to deep neural network training via [[Method — Differentially Private SGD (DP-SGD)]], making it the principled defense against exactly the kind of attack [[Paper — Membership Inference Attacks Against Machine Learning Models (2017)]] demonstrates empirically: a model trained under a genuine differential-privacy guarantee cannot leak much about any single training record's membership, no matter how an attacker queries it, because the guarantee bounds the model's *output distribution* directly rather than relying on any specific attack failing to work.
This is a categorically different kind of defense from the empirical mitigations [[Paper — Membership Inference Attacks Against Machine Learning Models (2017)]] itself evaluates (restricting prediction-vector granularity, regularization): those reduce a specific attack's measured success rate without any guarantee against attacks not yet invented, while differential privacy bounds every possible attack's success at once, as a mathematical consequence of the guarantee rather than an empirical observation.

## Relations

- Formalized by Dwork et al. (not yet ingested); applied to deep, non-convex neural network training for the first time at a usable privacy budget by [[Paper — Deep Learning with Differential Privacy (2016)]].
- Directly defeats [[Concept — Membership inference]] by construction, bounding the success of any attack that relies solely on a model's own output behavior.
- Operationalized for deep learning via [[Method — Differentially Private SGD (DP-SGD)]].

## Up

- [[Cluster — ML security & privacy]]
