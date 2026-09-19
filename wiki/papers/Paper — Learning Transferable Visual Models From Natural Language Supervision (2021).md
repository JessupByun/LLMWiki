---
type: paper
status: mature
updated: 2026-09-18
summary: "Trains an image encoder and a text encoder together to predict which caption goes with which image, using 400M web (image, text) pairs, producing a model that classifies images zero-shot from natural-language class names alone."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Learning Transferable Visual Models From Natural Language Supervision (2021).pdf"]
tags: [multimodal, contrastive-learning, zero-shot, transformer]
aliases: ["arxiv:2103.00020", "Radford et al. 2021", "CLIP"]
---

# Paper — Learning Transferable Visual Models From Natural Language Supervision (2021)

Commonly known as **CLIP**.

## Metadata

arXiv:2103.00020.
Alec Radford, Jong Wook Kim, Chris Hallacy, Aditya Ramesh, Gabriel Goh, Sandhini Agarwal, Girish Sastry, Amanda Askell, Pamela Mishkin, Jack Clark, Gretchen Krueger, Ilya Sutskever.
OpenAI.
Submitted February 2021.

## Impact

Standard image classifiers are trained to predict a fixed, pre-specified set of categories, so adding a new visual concept means collecting new labeled data and retraining.
This paper trains an image encoder and a text encoder together on 400 million (image, text) pairs scraped from the internet, using a contrastive objective that just predicts which caption in a batch actually goes with which image, with no fixed category list at all.
Once trained, a new classifier for any set of categories can be built for free: embed each category's name as a short natural-language description ("a photo of a dog"), embed the image, and pick the class whose text embedding is closest.
The resulting model, CLIP, matches the original supervised ResNet-50's accuracy on ImageNet zero-shot, without training on any of its 1.28 million labeled images, and its zero-shot classifiers transfer non-trivially to over 30 other benchmarks spanning OCR, geo-localization, and action recognition.

## Core idea

Instead of training the image encoder to predict a discrete label, train it and a text encoder jointly to solve one task: given a batch of N images and N captions, pick out which of the N×N possible pairings are the true ones.
Concretely, embed every image and every caption in the batch into a shared vector space, then maximize the cosine similarity of each image with its own true caption while minimizing similarity with every other caption in the batch - a symmetric contrastive loss computed once over images and once over captions.
The image encoder is either a modified ResNet or a [[Paper — An Image is Worth 16x16 Words (2020)]] (ViT) Vision Transformer; the text encoder is a Transformer operating on [[Method — Byte Pair Encoding (BPE)|BPE]]-tokenized text.
Because the text encoder learns to map any natural-language description into that same embedding space, it doubles as what the paper calls a hypernetwork: feed it a dataset's class names, and it generates a zero-shot linear classifier for that dataset on the spot, with no gradient updates and no task-specific training data at all.

## Why it endures

CLIP's zero-shot classifiers are dramatically more robust to natural distribution shift than an equivalently accurate ImageNet-trained model - across seven distribution-shift benchmarks CLIP closes up to 75% of the accuracy gap that ordinary supervised training leaves open, evidence that training on one narrow distribution is itself what makes conventional classifiers brittle.
More consequentially, CLIP's joint image-text embedding space became the standard bridge component wiring vision into language models: it supplies the visual encoder that later vision-language and text-to-image systems condition on, and its embedding space is explicitly reused for downstream generation (the paper itself notes single-image CLIP-guided synthesis as an early application), a role picked up directly by [[Paper — High-Resolution Image Synthesis with Latent Diffusion Models (2021)]] and the broader multimodal LLM line that followed.

## Limitations

Zero-shot CLIP is weak on abstract or specialized tasks with no natural-language handle - counting objects, satellite image classification, distinguishing fine-grained car or aircraft variants - and the paper estimates roughly a 1000x compute increase would be needed for zero-shot CLIP to reach overall state-of-the-art performance, which it calls infeasible with current hardware.
It also generalizes poorly to inputs genuinely unlike its training distribution: CLIP gets only 88% on handwritten MNIST digits, below a simple logistic regression baseline on raw pixels, because near-duplicate-free search of its pretraining data turns up almost no images resembling MNIST at all.

## Broader impacts: bias and surveillance

CLIP's own Section 7 is unusually direct for a paper of its era, and load-bearing for this wiki's lens rather than background trivia.
On the FairFace bias probe, zero-shot CLIP misclassified Black-labeled images into non-human categories ('animal', 'chimpanzee', 'gorilla') at roughly 14%, versus under 8% for every other race category tested, and misclassified images of people under 20 into crime-related or non-human categories at the highest rate of any age group - a disparity that dropped sharply once the authors added an explicit 'child' category, demonstrating how much class-design choices alone can shift denigration-harm rates.
Labels also skewed by gender: appearance-related words ('blonde', 'brown hair') attached far more often to images of women, while high-status occupation words ('executive', 'doctor') attached more often to men.
The paper separately evaluates CLIP on CCTV-style surveillance imagery and zero-shot celebrity identification from names alone, explicitly to characterize a capability - cheap, retraining-free classification for arbitrary categories - that makes CLIP-like models easier to repurpose for surveillance than a conventional fixed-category classifier, and states this as a deliberate reason for including the analysis rather than an endorsement of the use case.

## Relations

- Uses [[Paper — An Image is Worth 16x16 Words (2020)]] (ViT) as one of its two image encoder architectures, alongside a modified ResNet.
- Tokenizes text with [[Method — Byte Pair Encoding (BPE)]], introduced in [[Paper — Neural Machine Translation of Rare Words with Subword Units (2015)]].
- Its scaling-with-compute finding for zero-shot performance parallels [[Paper — Scaling Laws for Neural Language Models (2020)]] and the GPT line's scaling behavior.
- Its embedding space is directly reused as a conditioning and guidance signal by later text-to-image systems, including [[Paper — High-Resolution Image Synthesis with Latent Diffusion Models (2021)]].

## Up

[[Cluster — Foundational canon]]
