---
type: paper
status: in-progress
updated: 2026-09-18
summary: "VAE - introduced the reparameterization trick, turning an intractable Bayesian inference problem into something trainable end-to-end with ordinary backpropagation, and founded one of the two dominant deep generative modeling paradigms."
cluster: ["Foundational canon"]
sources: ["raw/papers/Paper — Auto-Encoding Variational Bayes (2013).pdf"]
tags: [generative-models, variational-inference, representation-learning]
aliases: ["arxiv:1312.6114", "Kingma & Welling 2013", "VAE", "AEVB", "SGVB"]
---

# Paper — Auto-Encoding Variational Bayes (2013)

Commonly known as the **VAE** (Variational Autoencoder) paper.

## Metadata

- **arXiv:** 1312.6114 (latest revision v11, 2022-12-10; originally posted 2013-12-20)
- **Venue:** ICLR 2014; not independently verified against DBLP in this session.
- **Authors:** Diederik P. Kingma, Max Welling (Universiteit van Amsterdam)

## Impact

Introduced the **variational autoencoder** and, more consequentially, the **reparameterization trick**: a way to backpropagate through a random sampling step that had previously made a whole class of latent-variable models impossible to train efficiently with gradient descent.
Before this paper, fitting a generative model with continuous latent variables and an intractable posterior required expensive iterative schemes like MCMC, one datapoint at a time.
This paper turned that same problem into something trainable end-to-end with ordinary stochastic gradient ascent, at roughly the cost of training any other neural network.
The VAE became one of the two foundational deep generative modeling paradigms of the following decade (alongside GANs), and the reparameterization trick it introduced is now a standard tool used anywhere a network needs to sample and still be differentiable, well beyond generative modeling specifically.

## Core idea: push the randomness outside the computation graph

The problem: to train a model that maps data `x` to a latent code `z` and back, you need to sample `z` from a distribution `q(z|x)` whose parameters (mean, variance) are themselves outputs of a neural network - but a naive gradient estimator through that sampling step is extremely high-variance and impractical to optimize.
The **reparameterization trick** rewrites the sample as a deterministic function of the network's parameters plus independent external noise.
For a Gaussian posterior, instead of sampling `z ~ N(μ, σ²)` directly, the model samples `z = μ + σ·ε` where `ε ~ N(0,1)` is drawn independently of anything the network controls.
Concretely, this means the randomness itself never depends on the parameters being optimized, so gradients flow cleanly through `μ` and `σ` exactly the way they flow through any ordinary layer - the one thing that made the whole pipeline differentiable and therefore trainable with standard backpropagation.

The resulting objective (the evidence lower bound, or ELBO) has an intuitive auto-encoder reading: an encoder network compresses a datapoint into a distribution over latent codes, a KL-divergence term regularizes that distribution toward a simple prior, and a decoder network reconstructs the datapoint from a sampled code, with the reconstruction quality forming the other half of the loss.

## Why it endures

The reparameterization trick outlived the specific VAE architecture that introduced it and became a general-purpose gradient-estimation tool - anywhere a model needs a differentiable stochastic layer, this is very often the mechanism underneath it.
The encoder/decoder framing (compress to a latent code, reconstruct from it) also became the template that later generative and representation-learning systems, including latent-space diffusion models, build variations on.

## Relations

- The paper's own experiments directly compare AEVB against the **wake-sleep algorithm** and **Monte Carlo EM** as prior approaches to the same latent-variable inference problem, showing faster convergence and better final likelihoods on MNIST and Frey Face.
- A generative-modeling counterpart to two other 2013 papers extending deep learning into new territory in the same year: [[Paper — Playing Atari with Deep RL (2013)]] (control) and [[Paper — Efficient Estimation of Word Representations (2013)]] (representation learning).

## Up

- [[Cluster — Foundational canon]]
