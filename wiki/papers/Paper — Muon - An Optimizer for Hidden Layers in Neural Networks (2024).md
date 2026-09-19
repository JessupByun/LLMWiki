---
type: paper
status: mature
updated: 2026-09-18
summary: "Muon - orthogonalizes the momentum update for 2D hidden-layer weight matrices via a cheap Newton-Schulz iteration instead of AdamW's per-coordinate scaling, cutting NanoGPT speedrun times 35% and reaching GPT-2 XL performance 25% faster at 1.5B parameters."
cluster: []
sources: ["https://kellerjordan.github.io/posts/muon/"]
tags: [optimizer, curiosity, orthogonalization]
aliases: ["Jordan et al. 2024", "Muon"]
---

# Paper — Muon - An Optimizer for Hidden Layers in Neural Networks (2024)

Curiosity ingest: not tied to the safety/security north star, but an interesting optimizer result in its own right.

## Metadata

- **arXiv:** none; published as a technical blog post, 2024-12-08.
- **Venue:** Keller Jordan's blog (kellerjordan.github.io); not a peer-reviewed venue, not independently verified against a proceedings listing in this session.
- **Authors:** Keller Jordan (primary), with contributions from Jeremy Bernstein, Laker Newhouse, Yuchen Jin, Vlado Boza, Jiacheng You, Franz Cesista.
- **Affiliation:** independent / open-source research community effort; no single institutional affiliation stated.

## Impact

Adam has been the near-universal default optimizer for training neural networks since [[Paper — Adam - A Method for Stochastic Optimization (2014)]], and genuinely evidenced improvements over it are rare.
Muon is one: swapped in as a drop-in replacement for the hidden-layer weight matrices in the NanoGPT speedrunning competition, it improved on the prior AdamW-based record by 35%, and stayed the optimizer of choice through 12 subsequent speed records set by 7 different researchers.
It also improved the CIFAR-10 training speed record from 3.3 to 2.6 A100-seconds to reach 94% accuracy, and at 1.5B parameters reached GPT-2 XL-level HellaSwag performance in 10 hours versus 13.3 hours for an AdamW-trained baseline - a real, benchmarked speed win rather than a purely theoretical one.

## Core idea

The starting observation: the update matrices that SGD-momentum or Adam produce for a transformer's 2D hidden-layer weight matrices tend to have a very high condition number - most of the update's "energy" concentrates along a few dominant directions rather than spreading evenly, effectively making the update close to low-rank even though the weight matrix itself is not.
Muon's fix is to replace that update with the nearest matrix that has all singular values equal to 1 (an orthogonalization step) before applying it, on the hypothesis that this amplifies the smaller, underrepresented directions that also matter for learning but get drowned out in the raw update.
Computing that orthogonal matrix exactly requires an SVD, which is too slow to run every training step; instead Muon approximates it with a Newton-Schulz iteration - a fixed quintic polynomial map (tuned coefficients a=3.4445, b=-4.7750, c=2.0315) applied 5 times, which pushes the update's singular values toward 1 without ever computing the SVD directly, runs stably even in low-precision bfloat16, and adds under 1% FLOP overhead in practice (0.7% for NanoGPT, 0.5% projected for a 405B-parameter Llama-scale model).
Concretely, Muon is scoped narrowly on purpose: it's applied only to the 2D weight matrices inside hidden layers, while scalar and vector parameters (biases, gains) and the input/output embedding layers are still optimized with ordinary AdamW, since those don't exhibit the same near-low-rank update pathology.

## Why it endures

Unlike most proposed Adam replacements, Muon ships with concrete, reproducible wall-clock speed records rather than only isolated benchmark numbers, which is what makes it worth a page on curiosity merits alone even with no direct tie to this wiki's safety/security north star.

## Limitations

The authors are explicit about what remains untested: how Muon behaves at scale beyond 1.5B parameters, how it performs in a distributed multi-GPU training setup, and whether it transfers to fine-tuning or reinforcement learning settings rather than the pretraining-from-scratch regime it was benchmarked on.

## Relations

- Built to improve on and directly benchmarked against [[Paper — Adam - A Method for Stochastic Optimization (2014)]] (specifically AdamW, the decoupled-weight-decay variant), which Muon still relies on for every parameter it doesn't itself optimize.
- Positioned by the authors as a cheaper approximation of Shampoo (Gupta et al., not yet ingested), which achieves a similar preconditioning effect via full inverse-fourth-root matrix roots rather than Newton-Schulz orthogonalization.
- Builds on older orthogonalized-descent ideas (Carlson et al. 2015-2016; Tuddenham et al. 2022's Orthogonal-SGDM), not yet ingested, which the authors credit as pioneering but less computationally efficient predecessors.
- Set its NanoGPT speedrunning records on a heavily modified fork of [[Repo — karpathy-nanoGPT]], the reference GPT-2 training codebase.

## Up

No cluster yet - a standalone curiosity ingest. If a second paper on optimizer internals or training-efficiency research is ingested, a topical cluster may form around this theme.
