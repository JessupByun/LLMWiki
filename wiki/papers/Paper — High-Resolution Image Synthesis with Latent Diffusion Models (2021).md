---
type: paper
status: mature
updated: 2026-09-18
summary: "Trains an autoencoder to compress images into a small latent space, then runs the diffusion process there instead of on raw pixels, cutting training and sampling cost while barely losing sample quality."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — High-Resolution Image Synthesis with Latent Diffusion Models (2021).pdf"]
tags: [generative-modeling, diffusion, multimodal]
aliases: ["arxiv:2112.10752", "Rombach et al. 2021", "LDM", "Latent Diffusion Models"]
---

# Paper — High-Resolution Image Synthesis with Latent Diffusion Models (2021)

## Metadata

arXiv:2112.10752.
Robin Rombach, Andreas Blattmann, Dominik Lorenz, Patrick Esser, Bjorn Ommer.
Ludwig Maximilian University of Munich & IWR, Heidelberg University; Runway ML.
Submitted December 2021 (CVPR 2022).

## Impact

[[Paper — Denoising Diffusion Probabilistic Models (2020)]] (DDPM) established that diffusion produces excellent image samples, but training and sampling both run the full denoising network directly on raw pixels, which the paper's own analysis puts at 150-1000 V100-days to train and about 5 days to generate 50,000 samples on a single A100.
This paper's fix: train an autoencoder once to compress images into a much smaller latent space that is perceptually equivalent to pixel space, then run the entire diffusion process there instead.
The result, Latent Diffusion Models (LDMs), sets a new state of the art on unconditional CelebA-HQ (FID 5.11) and class-conditional ImageNet generation while using roughly a quarter of ADM's training compute and half its parameters, and reaches competitive text-to-image results on MS-COCO at a fraction of the parameter count of GLIDE or Make-A-Scene.
Because the compression step and the generative step are decoupled and the autoencoder is reusable, LDM's architecture became the direct basis for Stable Diffusion, the first widely deployed open-weight text-to-image model.

## Core idea

Most of a natural image's raw pixel data is imperceptible high-frequency detail; a diffusion model trained directly on pixels still has to spend compute modeling that detail even though a reweighted loss lets it mostly ignore it perceptually.
This paper's move is to separate those two jobs entirely: first train an autoencoder (encoder E, decoder D) purely to compress an image into a smaller latent grid and reconstruct it faithfully, using a perceptual plus adversarial loss so reconstructions stay sharp rather than blurry.
Once that autoencoder is fixed, train the actual diffusion model - the DDPM noise-prediction objective from [[Paper — Denoising Diffusion Probabilistic Models (2020)]], unchanged in form - entirely on the compressed latent codes instead of pixels, with a UNet backbone that still exploits 2D spatial structure since the latent grid keeps the image's spatial layout, just at lower resolution.
A single decoder pass turns any generated latent back into a full-resolution image.
To make the diffusion process controllable, the paper adds a cross-attention mechanism borrowed from the Transformer's query-key-value formulation: a domain-specific encoder turns a conditioning input (a text prompt, a semantic map, a class label) into a token sequence, and cross-attention layers inside the UNet let the denoising process attend to those tokens at every step, which is what lets one architecture handle text-to-image, layout-to-image, super-resolution, and inpainting with no task-specific redesign.

## Why it endures

The autoencoder is trained once and reused across many different diffusion models and tasks, which is the property that made this architecture practical to deploy widely rather than staying a research result: Stable Diffusion is this exact recipe (a KL-regularized latent autoencoder plus a cross-attention-conditioned UNet diffusion model) trained at larger scale on web image-text pairs, and its release is what took diffusion-based text-to-image generation from a small number of closed, expensive systems to something runnable on consumer hardware.

## Broader impacts: misuse and bias

The paper names the same double edge as [[Paper — Denoising Diffusion Probabilistic Models (2020)]]'s Broader Impact section, more pointedly given text-to-image control: cheaper, more accessible generation lowers the barrier to creating and spreading manipulated images and deepfakes, a harm the authors note falls disproportionately on women, and inherits whatever biases are present in uncurated training data.
It also raises a distinct concern DDPM's smaller-scale training didn't: generative models can memorize and reveal specific training examples, which matters when that data was collected without consent, and the paper states this risk for diffusion models specifically is not yet well understood.

## Limitations

The sequential sampling process is still slower than a GAN's single forward pass, just cheaper than running that same sampling loop in pixel space.
Compression is lossy: the paper's own f=4 autoencoder loses very little quality for general images, but that reconstruction bottleneck becomes a real constraint for tasks needing fine-grained pixel accuracy, such as the paper's own super-resolution models.

## Relations

- Runs the exact DDPM noise-prediction objective from [[Paper — Denoising Diffusion Probabilistic Models (2020)]] on compressed latents instead of pixels.
- Autoencoder regularization borrows the KL-toward-standard-normal penalty from [[Paper — Auto-Encoding Variational Bayes (2013)]] (VAE), as one of its two regularization options.
- Conditioning mechanism reuses the query-key-value cross-attention formulation from [[Paper — Attention Is All You Need (2017)]].
- Mentions single-image CLIP-guided synthesis as a downstream application of the reusable latent space, pointing to [[Paper — Learning Transferable Visual Models From Natural Language Supervision (2021)]] (CLIP).
- See [[Method — Latent Diffusion]] for the compress-then-diffuse mechanism as reused in Stable Diffusion and later text-to-image systems.

## Up

[[Cluster — Foundational canon]]
