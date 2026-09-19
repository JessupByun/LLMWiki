---
type: paper
status: mature
updated: 2026-09-18
summary: "Splits an image into fixed-size patches, treats them as tokens, and feeds them to an unmodified Transformer encoder, matching or beating state-of-the-art CNNs when pretrained on enough data."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — An Image is Worth 16x16 Words (2020).pdf"]
tags: [transformer, computer-vision, pretraining]
aliases: ["arxiv:2010.11929", "Dosovitskiy et al. 2020", "ViT", "Vision Transformer"]
---

# Paper — An Image is Worth 16x16 Words (2020)

Commonly known as **ViT** (Vision Transformer).

## Metadata

arXiv:2010.11929.
Alexey Dosovitskiy, Lucas Beyer, Alexander Kolesnikov, Dirk Weissenborn, Xiaohua Zhai, Thomas Unterthiner, Mostafa Dehghani, Matthias Minderer, Georg Heigold, Sylvain Gelly, Jakob Uszkoreit, Neil Houlsby.
Google Research, Brain Team.
ICLR 2021.

## Impact

Convolutional networks bake in locality and translation-equivariance as architectural assumptions, hard-coded into every layer; every prior attempt to bring self-attention into vision either combined it with convolutions or needed specialized, hardware-unfriendly attention patterns.
This paper strips those assumptions out entirely: cut an image into a grid of fixed-size patches (16x16 pixels each), treat each patch exactly like a word token, and feed the resulting sequence into an off-the-shelf Transformer encoder, the same architecture used for text, with no image-specific inductive bias beyond the initial patch-cutting step.
Trained on mid-sized datasets like ImageNet alone, this underperforms comparable ResNets by a few points, exactly as expected since the model has to learn spatial structure from scratch with no help from convolutional priors.
But pretrained on enough data (14M-300M images), it matches or beats state-of-the-art CNNs on ImageNet (88.55% top-1) and a 19-task transfer suite, while costing substantially less compute to train.

## Core idea

Reshape a 224x224 image into a sequence of 16x16 patches (196 patches total), flatten each patch's pixels into a vector, and linearly project it into the same embedding space a word token would occupy - that's the entire adaptation needed to make images speak the Transformer's native language.
A learnable position embedding is added to each patch embedding so the model can tell patches apart by location (the same trick every text Transformer uses for word order), and a BERT-style `[class]` token is prepended to the sequence, its final-layer output serving as the whole image's representation for classification.
From there, it's a completely standard Transformer encoder: alternating self-attention and MLP blocks, layer normalization before each, residual connections after.
The paper's headline finding is that this works at all, and works well: at large enough pretraining scale, learning spatial relationships directly from data beats hand-encoding them into the architecture.

## Why it endures

ViT's patch-as-token trick generalized far beyond image classification, becoming the standard way to feed images into any Transformer-based system, including every subsequent vision-language model that needs a shared representation space for images and text.
Its central empirical claim, that enough data and compute let a maximally generic architecture out-learn a hand-designed inductive bias, echoes the same lesson [[Paper — Language Models are Few-Shot Learners (2020)]] draws for language, and previewed the same pattern recurring across modalities as pretraining scale increased.

## Relations

- Applies the encoder half of [[Paper — Attention Is All You Need (2017)]]'s Transformer directly to images, with no architectural modification beyond patch embedding.
- Borrows the `[class]`-token classification-head design from [[Paper — BERT (2018)]].
- Its data-scale-trumps-inductive-bias finding parallels [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3)'s scaling story in language.
- Uses [[Method — Layer Normalization]] before every attention and MLP block, following the Transformer's standard recipe.
- Direct architectural ancestor of later vision-language models that need a shared image/text token space (CLIP, not yet ingested; queued next in `BACKLOG.md`).

## Up

[[Cluster — Foundational canon]]
