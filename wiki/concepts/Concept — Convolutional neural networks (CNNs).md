---
type: concept
status: mature
updated: 2026-11-04
summary: "Architecture applying small learned filters across an input with shared weights, building in locality and translation-invariance; the architecture with which deep nets first decisively beat hand-engineered features at ImageNet scale."
cluster: ["Foundational canon"]
sources: []
tags: [architecture, cnn, vision]
aliases: ["CNN", "ConvNet"]
---

# Concept — Convolutional neural networks (CNNs)

## Definition

A neural architecture that slides small learned filters (kernels) across an input, applying the same weights at every position, so each layer produces feature maps that respond to a local pattern wherever it occurs.
The building blocks:

- **Convolutional layers:** each output unit sees only a small local neighborhood of the layer below, and every position shares the same filter weights.
- **Pooling layers:** summarize neighboring outputs (e.g. by taking their maximum), downsampling the feature map and adding tolerance to small shifts.
- **Depth:** stacking many such layers builds increasingly abstract features (edges, then textures, parts, and objects), with fully-connected layers typically on top for classification.

CNNs trained with backpropagation date back to LeCun et al.'s handwritten-digit networks around 1990 (not yet ingested); they were scaled up to high-resolution natural images in [[Paper — ImageNet Classification with Deep Convolutional Neural Networks (2012)]] (AlexNet).

## Why it works

Convolution hard-codes two assumptions that are mostly true of images: statistics are the same everywhere in the image (stationarity), and nearby pixels depend on each other far more than distant ones (locality).
Building those assumptions in means a CNN has far fewer connections and parameters than a fully-connected network with layers of similar size, so it is easier to train while, as the AlexNet paper puts it, its best achievable performance is likely only slightly worse.
That prior is exactly what made it feasible to train a large network on ImageNet in 2012, given efficient GPU convolution code, with training tricks like ReLU activations and [[Method — Dropout]] doing the rest.
The same assumptions are also a ceiling: [[Paper — An Image is Worth 16x16 Words - Transformers for Image Recognition at Scale (2020)]] (ViT) shows that with enough pretraining data, a Transformer with almost no built-in locality matches or beats state-of-the-art CNNs.

## Relations

- Scaled to ImageNet in [[Paper — ImageNet Classification with Deep Convolutional Neural Networks (2012)]]; made trainable past 100 layers by [[Paper — Deep Residual Learning for Image Recognition (2015)]] with [[Paper — Batch Normalization - Accelerating Deep Network Training by Reducing Internal Covariate Shift (2015)]].
- Regularization technique commonly paired with CNNs: [[Method — Dropout]].
- Contrast with [[Concept — Transformer architecture]], which replaced built-in locality with learned attention and eventually displaced CNNs as the default for many vision tasks too.

## Up

- [[Cluster — Foundational canon]]
