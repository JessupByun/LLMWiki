---
type: concept
status: in-progress
updated: 2026-08-07
summary: "Architecture applying learned filters convolutionally to exploit locality and translation-invariance; the first proof deep nets beat hand-engineered features."
cluster: ["Foundational canon"]
sources: []
tags: [architecture, cnn, vision]
aliases: []
---

# Concept — Convolutional neural networks (CNNs)

## Definition

A neural architecture that applies learned filters (kernels) convolutionally across an input, exploiting locality and translation-invariance in the data (originally images).
Popularized at scale by [[Paper — ImageNet Classification with Deep CNNs (2012)]].

## Core mechanism

- **Convolutional layers:** small learned filters slide across the input, producing feature maps. Parameter sharing across spatial positions makes CNNs far cheaper than fully-connected layers of similar size.
- **Pooling layers:** downsample feature maps (e.g. max-pooling) to build translation tolerance and reduce computation.
- **Depth:** stacking many conv layers builds increasingly abstract features (edges → textures → parts → objects).
- **Non-linearity:** ReLU (`max(0,x)`) rather than tanh/sigmoid, which trains substantially faster since it doesn't saturate.

## Why it matters

CNNs were the architecture that first demonstrated deep learning could dramatically outperform hand-engineered features (SIFT, Fisher Vectors) given enough data and compute.
That recipe, more data plus more compute plus more depth, later generalized beyond vision to language ([[Concept — Transformer architecture]]) and beyond.

## Relations

- Origin-at-scale paper: [[Paper — ImageNet Classification with Deep CNNs (2012)]].
- Regularization technique commonly paired with CNNs: [[Method — Dropout]].
- Contrast with [[Concept — Transformer architecture]]. Attention-based models eventually displaced CNNs as the default for many vision tasks too, but the depth/data/compute recipe CNNs established still holds.

## Up

- [[Cluster — Foundational canon]]
