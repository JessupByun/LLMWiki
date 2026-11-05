---
type: paper
status: mature
updated: 2026-11-04
summary: "Dropout - randomly drop each hidden unit during training (keeping it with probability p), forcing units to stop co-adapting, which significantly reduces overfitting and gives state-of-the-art results on many vision, speech, text, and genetics benchmarks with no architecture change."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Dropout - A Simple Way to Prevent Neural Networks from Overfitting (2014).pdf"]
tags: [regularization, training, foundational]
aliases: ["Srivastava et al. 2014", "Dropout"]
---

# Paper — Dropout - A Simple Way to Prevent Neural Networks from Overfitting (2014)

## Metadata

- **Venue:** Journal of Machine Learning Research 15 (2014), pages 1929-1958.
  Submitted 2013-11, published 2014-06.
- **Authors:** Nitish Srivastava, Geoffrey Hinton, Alex Krizhevsky, Ilya Sutskever, Ruslan Salakhutdinov (University of Toronto).
- Builds on a shorter 2012 technical note by an overlapping set of authors; this JMLR paper is the full, formal treatment with theoretical analysis and cross-domain experiments.

## Impact

Large neural networks with many more parameters than labeled examples overfit badly, and the standard fix - training many separate networks and averaging their predictions - is prohibitively expensive for large nets.
Dropout gets most of the benefit of that ensemble averaging from training a *single* network: on each training step, randomly zero out each hidden unit (and its connections) with some fixed probability, so every step effectively trains a different, randomly "thinned" sub-network sharing the same weights.
The paper demonstrates state-of-the-art results across five very different domains - vision (MNIST, SVHN, CIFAR-10/100, ImageNet), speech (TIMIT), text classification (Reuters-RCV1), and genetics (predicting alternative gene splicing) - all improved by adding dropout with no other architectural change, which is the strongest evidence in the paper that the technique is general rather than domain-specific.
It became one of the two or three most widely used regularizers in deep learning for the following decade.

## Core idea

At training time, each unit is retained with probability *p* and otherwise removed, along with all its incoming and outgoing connections, for that step - so roughly a fraction `(1-p)` of units drop out of both the forward and backward pass.
In the paper's convention *p* is the retention probability: about 0.5 works well for hidden units across many networks and tasks, while input units are usually kept with probability closer to 1 (0.8 in the MNIST experiments).
A network with `n` units can be viewed as a collection of `2^n` possible "thinned" sub-networks, all sharing the same underlying weights; training with dropout amounts to sampling and training a different one of these sub-networks on almost every step.
At test time, instead of the computationally infeasible step of averaging predictions across all `2^n` sub-networks, the paper uses a single, simple approximation: run the full, unthinned network, but scale each unit's outgoing weights by its retention probability `p`, which makes each unit's expected output at training time equal its actual output at test time.
Empirically, on MNIST a Monte Carlo average over sampled sub-networks only matches this weight-scaling approximation at around 50 samples, and is within one standard deviation of it beyond that.
The concrete intuition the authors give for *why* this reduces overfitting: no hidden unit can rely on any specific other unit being present on a given step, so each unit is forced to learn a feature that is independently useful across a wide variety of random contexts, rather than co-adapting into a fragile joint solution that only works when its usual partner units are also active - visibly confirmed in the paper's own comparison of single-hidden-layer MNIST autoencoder features: with dropout, hidden units detect edges, strokes, and spots, while without it the units have co-adapted and none seems to detect a meaningful feature on its own.

## Why it endures

The co-adaptation-breaking mechanism dropout formalizes - forcing a network not to depend on any single unit or specific combination of units - is architecture-agnostic by construction, which is why it reappears across convolutional and recurrent networks and inside the Transformer, which applies dropout to every sublayer output in [[Paper — Attention Is All You Need (2017)]].

## Limitations

The authors note a dropout network typically takes 2-3 times longer to train than a standard network of the same architecture, because each update trains a different random sub-network and the gradients are very noisy.
On the Reuters-RCV1 text classification task, dropout cut error only from 31.05% to 29.62%, a much smaller improvement than on the vision and speech data sets.
On a small alternative-splicing genetics data set, dropout's equally-weighted model averaging underperforms a properly weighted Bayesian neural network (code quality 567 vs 623), though it beats every other method tested and is far cheaper to run.

## Relations

- Demonstrated at scale two years earlier in [[Paper — ImageNet Classification with Deep Convolutional Neural Networks (2012)]], which used the 2012 technical note's version on its first two fully-connected layers to stop substantial overfitting.
- Method hub: [[Method — Dropout]].
- The regularization problem this paper addresses (overfitting in large networks with limited labeled data) is the same problem [[Paper — Batch Normalization - Accelerating Deep Network Training by Reducing Internal Covariate Shift (2015)]] later shows its own normalization technique also incidentally helps with, reducing (but not eliminating) the need for dropout in some architectures.

## Up

- [[Cluster — Foundational canon]]
