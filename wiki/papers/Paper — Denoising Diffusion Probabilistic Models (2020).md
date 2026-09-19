---
type: paper
status: mature
updated: 2026-09-18
summary: "Trains a Markov chain to reverse a fixed noising process step by step, turning random noise into a high-quality image and setting a new state-of-the-art FID on CIFAR10."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Denoising Diffusion Probabilistic Models (2020).pdf"]
tags: [generative-modeling, diffusion]
aliases: ["arxiv:2006.11239", "Ho, Jain & Abbeel 2020", "DDPM"]
---

# Paper — Denoising Diffusion Probabilistic Models (2020)

## Metadata

arXiv:2006.11239.
Jonathan Ho, Ajay Jain, Pieter Abbeel.
UC Berkeley.
NeurIPS 2020.

## Impact

Prior deep generative models each had a real weakness: GANs need an adversarial min-max game that's notoriously unstable to train, VAEs blur samples because their decoder optimizes a lower bound on likelihood rather than the likelihood itself, and autoregressive models generate pixel by pixel, which is slow and imposes an arbitrary ordering on the data.
This paper shows that a much simpler idea, gradually add Gaussian noise to an image until it's pure static, then train a network to reverse that process one small step at a time, produces samples that beat the published results of most other generative model families on unconditional CIFAR10 (Inception score 9.46, FID 3.17, a new state of the art at the time) and produce convincing 256x256 samples on CelebA-HQ and LSUN.
This paper (alongside the concurrent score-based generative modeling line of work it connects to) is the direct technical ancestor of the diffusion models behind essentially all major image and video generation systems since.

## Core idea

Picture slowly dissolving a photograph into TV static over 1000 tiny steps, each step adding just a little Gaussian noise; that's the fixed, no-learning-required forward process.
The model's entire job is to learn the reverse: given a noisy image at step t, predict what noise was added so it can be subtracted, moving the image one step back toward something less noisy.
Concretely, the network is trained to predict the exact noise vector that was mixed into a clean image at a random timestep, using a simple mean-squared-error loss between the true noise and the network's guess, at every one of the 1000 noise levels.
To generate a new image, start from pure random noise and repeatedly apply the trained network's noise prediction to step backward 1000 times, each step removing a little more noise until a clean image remains.
The paper's central technical contribution is showing this specific "predict the noise" parameterization (rather than the more obvious "predict the previous image directly") is mathematically equivalent to a technique called denoising score matching, and empirically produces far better sample quality than the alternative.

## Why it endures

The paper's own likelihood numbers are mediocre next to other likelihood-based models, and its own analysis explains why: diffusion models spend most of their reconstruction budget describing imperceptible pixel-level detail rather than the perceptually meaningful structure that determines sample quality, which is exactly why they generate compelling images despite unremarkable bits-per-dimension scores.
That combination, simple training objective, a training procedure that never goes unstable the way GAN training can, and comfortably better sample quality, is what over the following years made diffusion the dominant paradigm for image, audio, and video generation, superseding GANs everywhere sample fidelity mattered most.

## Broader impacts: misuse and bias

The paper's own Broader Impact section names the same two-sided pattern every generative-model advance surfaces: better sample quality directly improves the realism of deepfakes and fabricated media of real people, while the model also straightforwardly reproduces and can amplify biases already present in its training data, since large web-scraped image datasets are collected largely without curation for that.
It also notes a mitigating angle: at the time of publication, CNN-generated images still carried subtle detectable artifacts, though the same paper acknowledges better generative models will erode that safety margin over time.

## Limitations

Sampling requires running the full reverse chain (1000 network evaluations per image in this paper's setup), making generation far slower than a single GAN forward pass; this cost is the direct motivation for essentially every diffusion speedup technique developed afterward.

## Relations

- Directly extends Sohl-Dickstein et al. 2015's original diffusion probabilistic models framework (not yet ingested) with a simplified training objective and a U-Net-based architecture.
- Establishes a formal equivalence with denoising score matching and Langevin dynamics (Song & Ermon, not yet ingested).
- The reparameterization-trick lineage traces back to [[Paper — Auto-Encoding Variational Bayes (2013)]] (VAE), the other dominant deep generative paradigm this paper's results directly compete against.
- Architecture borrows the U-Net backbone and the Transformer's sinusoidal position embedding from [[Paper — Attention Is All You Need (2017)]] to condition the network on the diffusion timestep.
- Direct technical ancestor of Latent Diffusion Models (not yet ingested; queued next in `BACKLOG.md`), which run this same process in a compressed latent space rather than pixel space.

## Up

[[Cluster — Foundational canon]]
