---
type: method
status: mature
updated: 2026-09-18
summary: "Train a network to reverse a fixed, gradual noising process one small step at a time; generate by starting from pure noise and running the learned reverse process."
cluster: ["Foundational canon"]
sources: []
tags: [generative-modeling, diffusion]
aliases: ["DDPM", "diffusion models", "denoising diffusion"]
---

# Method — Denoising Diffusion (DDPM)

## Definition

Fix a forward process that gradually destroys a data sample by mixing in a little Gaussian noise at each of many timesteps, until nothing but noise remains.
Train a neural network to run this in reverse: given a noisy sample at some timestep, predict the noise that was added so it can be subtracted, moving one step back toward a cleaner sample.
To generate new data, start from pure random noise and repeatedly apply the trained denoiser, one timestep at a time, until a clean sample emerges.

## Why it works

The forward process needs no learning at all, since adding noise is just arithmetic; all the model capacity goes toward the one well-defined, well-behaved task of predicting noise from a noisy input, trained with a simple mean-squared-error loss.
Because generation is broken into many small, easy steps rather than one hard leap from noise to data, the process is far more stable to train than an adversarial min-max game, at the cost of needing many network evaluations (one per timestep) to produce a single sample.

## Relations

- Origin: [[Paper — Denoising Diffusion Probabilistic Models (2020)]] (Ho, Jain & Abbeel), building on Sohl-Dickstein et al. 2015's original diffusion probabilistic models framework (not yet ingested).
- Competes with the other dominant deep generative paradigm founded by [[Paper — Auto-Encoding Variational Bayes (2013)]] (VAE) and with GANs.
- Runs in a compressed latent space rather than pixel space in Latent Diffusion Models (not yet ingested; queued next in `BACKLOG.md`), the direct basis of Stable Diffusion.

## Up

[[Cluster — Foundational canon]]
