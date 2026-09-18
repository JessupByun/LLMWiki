---
type: paper
status: mature
updated: 2026-09-18
summary: "Scaling Laws - Transformer language model loss follows smooth, predictable power laws in model size, dataset size, and compute, almost independent of architecture shape, and the compute-optimal response to more compute is overwhelmingly to train a much bigger model rather than more data or more steps."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Scaling Laws for Neural Language Models (2020).pdf"]
tags: [scaling-laws, transformer, compute, pretraining]
aliases: ["arxiv:2001.08361", "Kaplan et al. 2020", "Scaling Laws"]
---

# Paper — Scaling Laws for Neural Language Models (2020)

## Metadata

- **arXiv:** 2001.08361 (v1, 2020-01-23)
- **Venue:** arXiv preprint; not independently verified against a peer-reviewed proceedings listing in this session.
- **Authors:** Jared Kaplan, Sam McCandlish, Tom Henighan, Tom B. Brown, Benjamin Chess, Rewon Child, Scott Gray, Alec Radford, Jeffrey Wu, Dario Amodei.
- **Affiliation:** OpenAI (Kaplan also Johns Hopkins University).

## Impact

Before this paper, "bigger models tend to do better" was an informally observed trend.
This paper turns it into a precise, predictive science: language model test loss follows a smooth power law in model size, dataset size, and training compute individually, holding across more than seven orders of magnitude, while depending only weakly on architectural choices like depth versus width.
It supplies the equations that determine, for a given compute budget, exactly how much of that budget should go toward a bigger model versus more training data versus more training steps - and the answer is overwhelmingly "bigger model."
Published four months before [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3) by an overlapping set of OpenAI authors, this paper's compute-optimal-allocation prescription is close to a direct blueprint for GPT-3's design: a very large model trained on comparatively modest data, stopped well short of convergence.

## Core idea

Train a family of Transformer language models varying independently in size, dataset size, training compute, and architectural shape, and measure the cross-entropy loss against each factor.
The result is three clean power laws: loss scales as `(N_c/N)^0.076` in the number of non-embedding parameters `N`, as `(D_c/D)^0.095` in dataset size `D` (tokens), and as `(C_c/C)^0.050` in training compute `C`, each holding whenever the other two factors aren't the bottleneck.
Concretely, doubling the model's parameter count reduces the loss by a factor of `2^-0.076`, about 5%, regardless of whether that doubling comes from a deeper or a wider network - architecture shape (depth-to-width ratio, number of attention heads, feed-forward dimension) barely matters once total non-embedding parameter count is fixed, with aspect ratio varying by 40x while changing loss by only a few percent.
Scaling N and D together avoids overfitting, but the paper shows the two scale sub-linearly: every 8x increase in model size only needs about a 5x increase in dataset size to avoid a performance penalty (the overfitting penalty follows `N^0.74/D`).
The most consequential result is the optimal-compute-allocation finding: given a fixed training compute budget with no other constraints, the loss-minimizing strategy is to spend nearly all of the extra compute on a bigger model, only a little on a larger batch size, and almost none on more serial training steps - for a billion-fold increase in compute, the optimal model size grows by more than a million-fold while dataset size grows only a couple of hundred-fold and the number of training steps barely increases at all.
In practice this means the compute-optimal recipe is to train a very large model and deliberately stop well short of convergence, rather than the conventional approach of training smaller models to completion.

## Why it endures

This paper supplied the theoretical and empirical backbone for the entire "scale is what matters" strategy that produced GPT-3 and set off the broader race toward ever-larger LLMs: its specific compute-allocation math (optimal model size scaling roughly as `C^0.73`) directly shaped how frontier labs split training budgets between model size and data for the next several years.

## Relations

- Builds on the Transformer architecture from [[Paper — Attention Is All You Need (2017)]].
- Trains on WebText2, an extended version of the WebText corpus introduced in [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] (GPT-2), and is trained with [[Paper — Adam - A Method for Stochastic Optimization (2014)]] (Adafactor for the largest models).
- Directly precedes and informs [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3), published four months later by an overlapping author list; GPT-3's very-large-model, comparatively-modest-data design closely follows this paper's compute-optimal prescription.
- Its specific model-size-versus-data tradeoff is later revised by Chinchilla (Hoffmann et al. 2022, not yet ingested), which argues that GPT-3 and similarly-scaled models trained under this paper's prescription were substantially undertrained relative to their parameter count - a natural Tension/update note to add once that paper is ingested.

## Up

[[Cluster — Foundational canon]]
