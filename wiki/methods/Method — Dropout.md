---
type: method
status: mature
updated: 2026-11-04
summary: "Randomly zero hidden units during training to block co-adaptation; architecture-agnostic regularizer that reappears nearly everywhere."
cluster: ["Foundational canon"]
sources: []
tags: [regularization, training]
aliases: []
---

# Method — Dropout

## Definition

A regularization technique.
During training, each unit is independently retained with probability *p* and otherwise dropped - removed, with all its incoming and outgoing connections, from both the forward and backward pass for that training case.
A common default is *p* = 0.5 for hidden units, with input units kept at a higher rate (closer to 1).
At test time every unit is kept, and each unit's outgoing weights are multiplied by its retention probability *p*, so its expected training-time output equals its actual test-time output.
Most modern frameworks implement the equivalent "inverted" form, rescaling the kept activations by `1/p` during training so that nothing needs to change at test time.

Formally introduced and analyzed in [[Paper — Dropout - A Simple Way to Prevent Neural Networks from Overfitting (2014)]] (Srivastava et al.), building on an earlier, shorter 2012 note by an overlapping set of authors.
Used at scale two years earlier in [[Paper — ImageNet Classification with Deep Convolutional Neural Networks (2012)]], on its first two fully-connected layers.

## Why it works

A network with *n* units contains `2^n` possible "thinned" sub-networks that all share weights; training with dropout samples a different one almost every step, and test-time weight scaling approximates averaging all of them.
That gives most of the benefit of a large ensemble from a single network - on MNIST, an explicit Monte Carlo average needs about 50 sampled sub-networks just to match the weight-scaling shortcut.
The mechanism the authors emphasize is breaking co-adaptation: a unit can't rely on any particular other units being present, so it has to learn a feature that is useful on its own across many random contexts.
The cost is slower training, since every update trains a different random architecture with noisy gradients - the paper reports 2-3 times longer training than an equivalent standard network.

## Relations

- Origin: [[Paper — Dropout - A Simple Way to Prevent Neural Networks from Overfitting (2014)]].
- Used in [[Paper — ImageNet Classification with Deep Convolutional Neural Networks (2012)]] in the context of [[Concept — Convolutional neural networks (CNNs)]], but architecture-agnostic.
- Applied to every sublayer output and the embedding sums in [[Paper — Attention Is All You Need (2017)]]'s Transformer.
- [[Paper — Batch Normalization - Accelerating Deep Network Training by Reducing Internal Covariate Shift (2015)]] reports that its normalization also regularizes, reducing the need for dropout in some networks.

## Up

- [[Cluster — Foundational canon]]
