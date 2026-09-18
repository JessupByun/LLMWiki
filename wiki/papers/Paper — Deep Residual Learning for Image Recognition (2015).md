---
type: paper
status: in-progress
updated: 2026-09-18
summary: "ResNet - shortcut connections let each block learn a residual function relative to its input rather than a full transformation, making networks over 100 layers deep trainable for the first time and winning ILSVRC 2015."
cluster: ["Foundational canon"]
sources: ["raw/papers/Paper — Deep Residual Learning for Image Recognition (2015).pdf"]
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

Introduced residual ("skip") connections that let neural networks be trained far deeper than previously possible, by reformulating each block to learn a residual function relative to its input rather than an entirely new representation.
An ensemble of residual networks up to 152 layers deep - eight times deeper than the previous best model, VGG - achieved 3.57% top-5 test error on ImageNet, winning the ILSVRC 2015 classification competition outright, and the same residual-learning principle produced a 28% relative improvement on COCO object detection purely from deeper learned features.
This is the architectural change that made "just make the network deeper" a viable strategy again, after the field had run into networks that got *worse*, not better, as more layers were stacked on.

## Problem it solved

Beyond a certain depth, plain stacked networks were observed to get *worse* training error as more layers were added - not from overfitting, but because the optimizer simply couldn't find good solutions in the larger parameter space, a phenomenon the paper calls the **degradation problem**.
This was a genuinely puzzling failure: a deeper network can always, in principle, match a shallower one exactly, just by making its extra layers learn the identity function and copying the shallow model's learned layers underneath.
The fact that real solvers couldn't find even this trivial solution meant the problem was optimization difficulty, not capacity - existing solvers struggled to learn an identity mapping through a stack of nonlinear layers.

## Core idea: learn the residual, not the full transformation

For a block meant to learn some target mapping `H(x)`, ResNet instead has the block learn the residual `F(x) = H(x) - x`, and reconstructs the original mapping by adding the input back in: `y = F(x) + x`.
The addition is implemented as a **shortcut connection**: the block's input skips ahead and is added directly to the block's output, before the final nonlinearity.
Concretely, this changes what "doing nothing" costs the optimizer: if the ideal transformation for a given block really is close to the identity, a residual block reaches it just by driving its learned weights toward zero, while a plain block has to learn to reconstruct the identity function exactly through multiple nonlinear layers - a much harder target to hit by gradient descent.
This single change is what let networks scale past 100 layers (and, in an extreme stress test, 1,202 layers) while still training successfully, something plain networks in this paper's own experiments could not do.

## How it's built

- **Residual blocks** of 2-3 convolutional layers with an identity shortcut (or, when the block changes the number of channels, a 1x1 convolution projection) added to the output before the final ReLU.
- **Bottleneck design** for the deeper 50/101/152-layer variants: a 1x1 convolution reduces the channel dimension, a 3x3 convolution does the main work, and another 1x1 convolution restores the original dimension - keeping computational cost comparable to the shallower blocks despite far greater depth.
- **Batch Normalization** ([[Paper — Batch Normalization (2015)]]) applied after every convolution, used throughout.
- **152-layer ResNet: 11.3 billion FLOPs**, still lower than VGG-16/19's 15.3/19.6 billion FLOPs despite being far deeper.

## Results (highlights)

- **34-layer ResNet cut top-1 error by 3.5%** relative to a plain network of identical depth and parameter count, isolating the shortcut connection as the entire source of the improvement.
- **152-layer ResNet reached 19.38% top-1 / 4.49% top-5** single-model error on ImageNet validation - the deepest network yet presented on ImageNet at the time, at lower computational cost than the much shallower VGG.
- **A 6-model ensemble reached 3.57% top-5 test error**, winning the ILSVRC 2015 classification competition.
- **Swapped into Faster R-CNN in place of VGG-16, ResNet-101 improved COCO mAP@[.5,.95] by 6.0 points** (a 28% relative improvement) - a gain the paper attributes entirely to better learned features, since every other part of the detection pipeline was held fixed.

## Why it endures

The residual/shortcut-connection idea generalized far beyond image classification: it is the mechanism that made training very deep networks tractable at all, and the same "learn a residual, not a full transformation" principle reappears throughout deep learning - most directly as a structural feature of the Transformer block in [[Paper — Attention Is All You Need (2017)]] (which wraps both its attention and feedforward sublayers in residual connections), and as the default backbone architecture inside [[Paper — Mastering the Game of Go with Deep Neural Networks and Tree Search (2016)]]'s later self-play successors.

## Limitations

- **The root cause of the degradation problem is explicitly left open.** The authors state plainly that "the reason for such optimization difficulties will be studied in the future" - the paper demonstrates a highly effective fix without a complete theoretical account of why plain deep networks fail to optimize in the first place.
- **The 1,202-layer stress test shows signs of overfitting** on the comparatively small CIFAR-10 dataset, which the authors attribute to the model being unnecessarily large for that data rather than to any flaw in the residual-learning approach itself; they deliberately withhold stronger regularization to keep the experiment focused on the optimization question.

## Relations

- Published the same year as [[Paper — Batch Normalization (2015)]], whose normalization technique this paper uses after every convolution.
- Directly follows [[Paper — ImageNet Classification with Deep CNNs (2012)]] and the deep CNN lineage as the next major architectural leap in image classification.
- The residual-connection principle reappears structurally inside [[Paper — Attention Is All You Need (2017)]]'s Transformer block and as the default backbone for [[Paper — Mastering the Game of Go with Deep Neural Networks and Tree Search (2016)]]'s self-play successors.

## Up

- [[Cluster — Foundational canon]]
