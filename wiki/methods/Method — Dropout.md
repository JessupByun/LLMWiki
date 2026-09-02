---
type: method
status: in-progress
updated: 2026-08-07
summary: "Randomly zero hidden units during training to block co-adaptation; architecture-agnostic regularizer that reappears nearly everywhere."
cluster: ["Foundational canon"]
sources: []
tags: [regularization, overfitting]
aliases: []
---

# Method — Dropout

## Definition

A regularization technique.
During training, randomly zero out each hidden unit's output with probability *p* (commonly 0.5); dropped units don't contribute to the forward pass or backprop for that step.
At test time all units are used, with outputs scaled (e.g. multiplied by *p*) to approximate averaging over the exponentially-many "thinned" sub-networks sampled during training.

Popularized at scale by [[Paper — ImageNet Classification with Deep CNNs (2012)]], applied to its first two fully-connected layers.

## Why it works

Prevents complex co-adaptation between neurons.
A unit can't rely on any specific set of other units being present, so it's forced to learn features that are useful in combination with many random subsets of the network.
Roughly doubles the number of iterations needed to converge, but substantially reduces overfitting on models with many more parameters than labeled examples.

## Relations

- Introduced in the context of [[Concept — Convolutional neural networks (CNNs)]], but the technique is architecture-agnostic. Expect later papers across domains (language, RL, etc.) to reuse it.

## Up

- [[Cluster — Foundational canon]]
