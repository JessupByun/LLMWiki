---
type: paper
status: mature
updated: 2026-11-04
summary: "ResNet - shortcut connections let each block learn a residual function relative to its input rather than a full transformation, the first networks whose accuracy keeps improving past 100 layers of depth, winning ILSVRC 2015."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Deep Residual Learning for Image Recognition (2015).pdf"]
tags: [architecture, cnn, vision, residual-connections]
aliases: ["arxiv:1512.03385", "He et al. 2015", "ResNet", "Deep Residual Learning"]
---

# Paper — Deep Residual Learning for Image Recognition (2015)

Commonly known as **ResNet**.

## Metadata

- **arXiv:** 1512.03385 (v1, 2015-12-10)
- **Venue:** CVPR 2016; not independently verified against DBLP in this session.
- **Authors:** Kaiming He, Xiangyu Zhang, Shaoqing Ren, Jian Sun (Microsoft Research)

## Impact

Beyond a certain depth, plain stacked networks got *worse* training error as layers were added - not overfitting, but an optimization failure the paper calls the **degradation problem**.
Residual ("shortcut") connections fixed it, and made "just make the network deeper" a working strategy again.
An ensemble of residual networks up to 152 layers deep - 8x deeper than VGG, yet with lower complexity - reached **3.57% top-5 test error** on ImageNet and won ILSVRC 2015 classification.
The same backbone gave a **28% relative improvement** on COCO object detection, purely from better learned features.
Concurrent highway networks also used shortcut connections, but with learned gates, and the paper notes they had not shown accuracy gains past 100 layers; ResNet is the paper that did.

## Core idea

A deeper network can always match a shallower one in principle, by making its extra layers compute the identity - so the degradation problem meant solvers were failing to find even that trivial solution through a stack of nonlinear layers.
ResNet changes what the block has to learn: instead of a target mapping `H(x)`, the block learns the residual `F(x) = H(x) - x`, and a parameter-free identity shortcut adds the input back, `y = F(x) + x`.
Concretely, this changes what "doing nothing" costs: if the ideal block is close to the identity, a residual block gets there by driving its weights toward zero, while a plain block has to reconstruct the identity exactly through several nonlinear layers.
Deeper variants use a 1x1-3x3-1x1 "bottleneck" block to keep compute down, so the 152-layer model still needs fewer FLOPs (11.3 billion) than VGG-16/19 (15.3/19.6 billion).
On ImageNet, a 34-layer ResNet beats a plain network of identical depth and parameter count by 3.5 points of top-1 error, isolating the shortcut as the source of the gain, and a 1,202-layer CIFAR-10 stress test still trains (though it overfits that small dataset).
The paper is explicit that it fixes the degradation problem without explaining its root cause, which it leaves to future study.

## Why it endures

"Learn a residual, not a full transformation" became the mechanism that makes very deep networks trainable at all, far beyond image classification.
Every Transformer block in [[Paper — Attention Is All You Need (2017)]] wraps both its attention and feed-forward sublayers in residual connections, and the self-play Go network of [[Paper — Mastering the Game of Go without Human Knowledge (2017)]] is built from residual blocks.
The residual stream that mechanistic interpretability reads features from is this paper's shortcut path, carried into the Transformer.

## Relations

- Uses [[Paper — Batch Normalization (2015)]] after every convolution.
- Follows [[Paper — ImageNet Classification with Deep CNNs (2012)]] as the next major architectural leap in image classification; see [[Concept — Convolutional neural networks (CNNs)]].
- Residual connections reappear structurally in [[Paper — Attention Is All You Need (2017)]] and as the backbone of [[Paper — Mastering the Game of Go without Human Knowledge (2017)]].

## Up

- [[Cluster — Foundational canon]]
