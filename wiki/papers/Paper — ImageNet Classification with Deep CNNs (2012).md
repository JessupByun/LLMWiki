---
type: paper
status: in-progress
updated: 2026-08-07
summary: "AlexNet - the ILSVRC-2012 landslide that convinced the field deep networks plus data plus compute beat hand-engineered features."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — ImageNet Classification with Deep CNNs (2012).pdf"]
tags: [architecture, cnn, vision, dropout]
aliases: ["NeurIPS 2012 — Krizhevsky, Sutskever, Hinton"]
---

# Paper — ImageNet Classification with Deep CNNs (2012)

Commonly known as **AlexNet**.

## Metadata

- **Venue:** NeurIPS (NIPS) 2012. No arXiv id (predates arXiv's dominance in ML); venue+year+author used as canonical identity.
- **Authors:** Alex Krizhevsky, Ilya Sutskever, Geoffrey E. Hinton (University of Toronto)

## Impact

The result widely credited with kicking off the deep learning era.
A deep convolutional neural network won the ILSVRC-2012 image recognition competition by a landslide: **15.3% top-5 error vs. 26.2%** for the best non-neural competitor (hand-engineered features).
That gap convinced the field almost overnight that "more data + more compute + more depth," trained with the right engineering, could beat decades of hand-crafted feature engineering.
This is the recipe that later generalized from vision to language and everywhere else.

## Core idea

Stack many layers of learned convolutional filters (rather than hand-designed ones) and let backpropagation figure out what features matter, at a scale (8 layers, 60M parameters) nobody had trained end-to-end on natural images before.
Three choices made this actually trainable:

- **ReLU instead of tanh/sigmoid:** `f(x) = max(0, x)`. Because it doesn't saturate, it trains several times faster. Without it, a network this size wasn't practical to train at all.
- **Dropout:** randomly turn off half the neurons in a layer during each training step, forcing the network to not rely on any single neuron being present. Think of it like training a huge committee of overlapping sub-networks that share weights, then averaging their votes at test time. See [[Method — Dropout]].
- **Splitting the model across two GPUs**, with a hand-written, heavily optimized convolution implementation. Engineering that made the scale possible, not just the algorithm.

## Why it endures

This paper is the true origin point of the deep-learning-at-scale playbook, the one that vision adopted first, and that language ([[Paper — Attention Is All You Need (2017)]]) and everything after followed.
Throw more data and compute at a deep network, engineer the training to actually work, and it beats hand-designed alternatives.

## Relations

- See [[Concept — Convolutional neural networks (CNNs)]] for the reusable architectural mechanism.
- See [[Method — Dropout]], a technique this paper popularized that reappears across nearly every later architecture regardless of domain.
- Alongside [[Paper — Attention Is All You Need (2017)]] as one of the two archetypal architecture papers in [[Cluster — Foundational canon]].

## Up

- [[Cluster — Foundational canon]]
