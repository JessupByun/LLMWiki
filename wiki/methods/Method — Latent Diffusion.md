---
type: method
status: mature
updated: 2026-09-18
summary: "Train an autoencoder once to compress images into a small latent space, then run the diffusion training and sampling process there instead of on raw pixels."
cluster: ["Foundational canon"]
sources: []
tags: [generative-modeling, diffusion, multimodal]
aliases: ["LDM", "Latent Diffusion"]
---

# Method — Latent Diffusion

## Definition

Train an encoder/decoder pair once, purely to compress an image into a smaller latent grid and reconstruct it faithfully (using a perceptual plus adversarial loss so reconstructions stay sharp).
Then train a [[Method — Denoising Diffusion (DDPM)|denoising diffusion]] model entirely on the compressed latent codes rather than on pixels, using a UNet that still exploits 2D spatial structure since the latent grid preserves the image's spatial layout at lower resolution.
A conditioning signal (text, a semantic map, a class label) can be injected via cross-attention layers inside the UNet, letting one architecture handle text-to-image, layout-to-image, super-resolution, and inpainting without task-specific redesign.

## Why it works

Most of a raw image's pixel data is imperceptible high-frequency detail that a pixel-space diffusion model still has to spend compute modeling even though it mostly ignores it perceptually.
Compressing to a latent space first removes that redundant detail before diffusion training ever starts, so the same denoising process runs on a much smaller input, cutting both training and sampling cost with little loss in perceptual quality.
Because the autoencoder is trained once and decoupled from any particular diffusion model, it can be reused across many downstream generative tasks without retraining the compression stage.

## Relations

- Origin: [[Paper — High-Resolution Image Synthesis with Latent Diffusion Models (2021)]] (Rombach et al.), which runs the [[Paper — Denoising Diffusion Probabilistic Models (2020)]] objective on latents instead of pixels.
- Autoencoder regularization borrows from [[Paper — Auto-Encoding Variational Bayes (2013)]] (VAE).
- Cross-attention conditioning reuses the mechanism from [[Paper — Attention Is All You Need (2017)]].
- Direct architectural basis of Stable Diffusion, the first widely deployed open-weight text-to-image model.

## Up

[[Cluster — Foundational canon]]
