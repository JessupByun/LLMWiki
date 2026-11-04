---
type: paper
status: mature
updated: 2026-11-04
summary: "AlexNet - the ILSVRC-2012 landslide that convinced the field deep networks plus data plus compute beat hand-engineered features."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — ImageNet Classification with Deep CNNs (2012).pdf"]
tags: [architecture, cnn, vision, dropout]
aliases: ["AlexNet", "Krizhevsky et al. 2012"]
---

# Paper — ImageNet Classification with Deep CNNs (2012)

Full title: **ImageNet Classification with Deep Convolutional Neural Networks**.
Commonly known as **AlexNet**.

## Metadata

- **Venue:** NeurIPS (NIPS) 2012.
  No arXiv id; venue+year+author used as canonical identity.
- **Authors:** Alex Krizhevsky, Ilya Sutskever, Geoffrey E. Hinton (University of Toronto)

## Impact

The result widely credited with kicking off the deep learning era.
An ensemble of deep convolutional networks won the ILSVRC-2012 image recognition competition by a landslide: **15.3% top-5 test error vs. 26.2%** for the second-best entry, which averaged classifiers built on hand-engineered Fisher Vector features.
On the earlier ILSVRC-2010 test set a single network reached 37.5% top-1 and 17.0% top-5 error, against 45.7% / 25.7% for the best prior SIFT + Fisher Vector system.
That gap convinced the field almost overnight that a large network trained end to end on enough labeled data, with the engineering to make training feasible, could beat decades of hand-crafted feature design.
The paper's own closing claim is the scaling bet in miniature: its results kept improving as the network got bigger and trained longer, and "can be improved simply by waiting for faster GPUs and bigger datasets."

## Core idea

Stack many layers of learned convolutional filters instead of hand-designed features, and let backpropagation decide what features matter, at a scale nobody had trained end to end on natural images: five convolutional layers plus three fully-connected layers, 60 million parameters, 650,000 neurons, trained on 1.2 million labeled images.
Depth itself is load-bearing - removing any one middle convolutional layer costs about 2% top-1 accuracy, even though each holds no more than 1% of the parameters.
The rest of the paper is the engineering that made a network that size trainable without overfitting:

- **ReLU instead of tanh:** `f(x) = max(0, x)` doesn't saturate, so a four-layer CNN reaches 25% training error on CIFAR-10 six times faster than the same network with tanh units - without it, experimenting at this size would not have been practical.
- **Two GPUs:** the network is split across two GTX 580 3GB GPUs with a hand-optimized convolution implementation, taking five to six days to train.
- **Local response normalization and overlapping pooling:** smaller tricks that cut top-5 error by 1.2% and 0.3% respectively.
- **Data augmentation:** random 224x224 crops and horizontal reflections of the 256x256 images (plus a PCA-based color perturbation that cuts top-1 error by over 1%), without which the network overfits substantially.
- **Dropout** in the first two fully-connected layers, from the then-recent 2012 technical note by Hinton and colleagues: without it the network overfits substantially, at the cost of roughly doubling the iterations to converge (see [[Method — Dropout]]).

A concrete sign the network learned semantics rather than pixels: test images whose last-layer 4096-dimensional activations are close to a training image's retrieve dogs and elephants in quite different poses, images that are not close to each other at the pixel level at all.

## Why it endures

This is the origin point of the deep-learning-at-scale playbook: vision adopted it first, and language ([[Paper — Attention Is All You Need (2017)]]) and everything after followed.
Throw more data and compute at a deep network, engineer the training to actually work, and it beats hand-designed alternatives.

## Relations

- See [[Concept — Convolutional neural networks (CNNs)]] for the reusable architectural mechanism.
- Uses [[Method — Dropout]], formalized in full two years later in [[Paper — Dropout (2014)]].
- Its depth limits are what [[Paper — Deep Residual Learning for Image Recognition (2015)]] later breaks through, going from 8 layers to 152.

## Up

- [[Cluster — Foundational canon]]
