---
type: paper
status: in-progress
updated: 2026-09-18
summary: "Dropout - randomly zero each hidden unit's output with probability p during training, forcing units to stop co-adapting, which significantly reduces overfitting and improves state-of-the-art results across vision, speech, text, and genetics tasks with no architecture change."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Dropout (2014).pdf"]
tags: [regularization, training, foundational]
aliases: ["Srivastava et al. 2014"]
---

# Paper — Dropout (2014)

Full title: **Dropout: A Simple Way to Prevent Neural Networks from Overfitting**.

## Metadata

- **Venue:** Journal of Machine Learning Research 15 (2014), pages 1929-1958. Submitted 2013-11, published 2014-06.
- **Authors:** Nitish Srivastava, Geoffrey Hinton, Alex Krizhevsky, Ilya Sutskever, Ruslan Salakhutdinov (University of Toronto).
- Builds on a shorter 2012 technical note by an overlapping set of authors; this JMLR paper is the full, formal treatment with theoretical analysis and cross-domain experiments.

## Impact

Large neural networks with many more parameters than labeled examples overfit badly, and the standard fix - training many separate networks and averaging their predictions - is prohibitively expensive for large nets.
Dropout gets most of the benefit of that ensemble averaging from training a *single* network: on each training step, randomly zero out each hidden unit (and its connections) with some fixed probability, so every step effectively trains a different, randomly "thinned" sub-network sharing the same weights.
The paper demonstrates state-of-the-art results across five very different domains - vision (MNIST, SVHN, CIFAR-10/100, ImageNet), speech (TIMIT), text classification (Reuters-RCV1), and genetics (predicting alternative gene splicing) - all improved by adding dropout with no other architectural change, which is the strongest evidence in the paper that the technique is general rather than domain-specific.
It became one of the two or three most widely used regularizers in deep learning for the following decade.

## Core idea

At training time, each hidden unit's output is multiplied by a random Bernoulli variable that is 1 with probability *p* and 0 otherwise, so on any given step roughly `(1-p)` of the units are silently removed from the forward and backward pass entirely.
A network with `n` units can be viewed as a collection of `2^n` possible "thinned" sub-networks, all sharing the same underlying weights; training with dropout amounts to sampling and training a different one of these sub-networks on almost every step.
At test time, instead of the computationally infeasible step of averaging predictions across all `2^n` sub-networks, the paper uses a single, simple approximation: run the full, unthinned network, but scale each unit's outgoing weights by `p` - which the paper shows gives (under approximation) the same expected output as the true ensemble average, and empirically the paper finds the approximation nearly indistinguishable in accuracy from an expensive Monte Carlo average over 50+ sampled sub-networks.
The concrete intuition the authors give for *why* this reduces overfitting: no hidden unit can rely on any specific other unit being present on a given step, so each unit is forced to learn a feature that is independently useful across a wide variety of random contexts, rather than co-adapting into a fragile joint solution that only works when its usual partner units are also active - visibly confirmed in the paper's own comparison of first-layer autoencoder features learned with and without dropout, where the with-dropout features are individually interpretable edge/stroke detectors while the without-dropout features look like noise.

## Why it endures

The co-adaptation-breaking mechanism dropout formalizes - forcing a network to not depend on any single unit or specific combination of units - is architecture-agnostic by construction, which is exactly why it reappears essentially unchanged across convolutional networks, recurrent networks, and (in modified form, since Dropout doesn't interact well with residual/attention normalization the way it does with plain feedforward layers) as a design consideration in every architecture built since.

## Limitations

The authors note dropout roughly doubles the number of training iterations needed to converge, since a large fraction of each update is effectively noise from a different random sub-network.
On the largest data set tested (Reuters-RCV1, over 200,000 training examples), the improvement from dropout was much smaller than on the vision and speech data sets, suggesting the technique matters most when overfitting - not underlying data volume - is the binding constraint.
The paper's own comparison against Bayesian neural networks on a small genetics data set found dropout's equally-weighted model averaging clearly underperforms a properly weighted Bayesian model average, though at a small fraction of the computational cost.

## Relations

- Demonstrated at scale the same year (published slightly earlier) in [[Paper — ImageNet Classification with Deep CNNs (2012)]], applied to its first two fully-connected layers - among the reasons AlexNet's architecture worked as well as it did.
- Method hub: [[Method — Dropout]].
- The regularization problem this paper addresses (overfitting in large networks with limited labeled data) is the same problem [[Paper — Batch Normalization (2015)]] later shows its own normalization technique also incidentally helps with, reducing (but not eliminating) the need for dropout in some architectures.

## Up

- [[Cluster — Foundational canon]]
