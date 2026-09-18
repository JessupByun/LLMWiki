---
type: paper
status: in-progress
updated: 2026-09-18
summary: "GANs - trains a generator and a discriminator against each other in a minimax game until the generator's samples are indistinguishable from real data, with no explicit likelihood and no Markov chains needed."
cluster: ["Foundational canon"]
sources: ["raw/papers/Paper — Generative Adversarial Networks (2014).pdf"]
tags: [generative-models, gan, adversarial-training]
aliases: ["arxiv:1406.2661", "Goodfellow et al. 2014", "GAN", "GANs", "Generative Adversarial Nets"]
---

# Paper — Generative Adversarial Networks (2014)

Full title: **Generative Adversarial Nets**.
Commonly known as **GANs**.

## Metadata

- **arXiv:** 1406.2661 (v1, 2014-06-10)
- **Venue:** NeurIPS (NIPS) 2014; not independently verified against DBLP in this session.
- **Authors:** Ian J. Goodfellow, Jean Pouget-Abadie, Mehdi Mirza, Bing Xu, David Warde-Farley, Sherjil Ozair, Aaron Courville, Yoshua Bengio (Universite de Montreal)

## Impact

Proposed training a generative model by pitting it against an adversary in a minimax game: a generator network learns to produce samples that look like real data, while a discriminator network learns to tell generated samples from real ones apart, and the two are trained simultaneously against each other.
The whole system trains with ordinary backpropagation - no Markov chains, no approximate inference network, and no need to specify the data distribution's likelihood explicitly, all of which prior deep generative models required in some form.
Published the same year as [[Paper — Auto-Encoding Variational Bayes (2013)]], this became the other of the two dominant deep generative modeling paradigms of the following decade, and for years the standard approach for producing sharp, realistic synthetic images.

## Core idea: a counterfeiter and a detective, trained together

The paper's own analogy is the clearest way to hold this: the generator is like a team of counterfeiters trying to produce fake currency that passes as real, and the discriminator is like the police trying to detect the counterfeits.
Competition between them drives both to improve - the counterfeits get better because the police keep catching the obvious fakes, and the police get better because the counterfeits keep improving - until the fakes are indistinguishable from genuine currency.

Formally, the discriminator `D` is trained to maximize `log D(x) + log(1 - D(G(z)))`, correctly labeling real data `x` as real and generated samples `G(z)` as fake, while the generator `G` is trained to fool it.
In practice the paper trains `G` to maximize `log D(G(z))` rather than literally minimizing the mirror-image objective, because early in training - when `G` is still poor - the original objective provides very weak gradients (the discriminator rejects bad fakes with high confidence, and the loss saturates).
The paper proves that if both networks have unlimited capacity, the game has a unique global optimum: the generator's distribution exactly matches the true data distribution, and the discriminator can no longer do better than random guessing, outputting exactly 1/2 everywhere.

## Why it endures

The idea of training one network to judge another differentiably, rather than hand-designing a loss function, turned out to generalize well beyond generative modeling and reappears across deep learning as a general adversarial-training technique.
Within generative modeling specifically, GANs held the state of the art for sharp image synthesis for years - the samples this framework produces are typically much crisper than a VAE's, at the cost of the notoriously unstable, hard-to-diagnose training dynamics that come from optimizing two networks against each other rather than a single well-behaved loss.

## Relations

- Published the same year as [[Paper — Auto-Encoding Variational Bayes (2013)]] as the other foundational deep generative modeling paradigm; contrasted directly by the field ever since - GANs need no explicit likelihood and no separate inference network, while VAEs optimize an explicit (if approximate) likelihood bound and come with a usable encoder for free.
- Relies on the same backpropagation and dropout machinery ([[Method — Dropout]]) already standard by this point in deep learning.

## Up

- [[Cluster — Foundational canon]]
