---
type: paper
status: in-progress
updated: 2026-09-18
summary: "Backpropagation - a general, efficient procedure for training multi-layer networks by propagating error gradients backward through the network layer by layer, letting hidden units learn internal representations of a task no one hand-designed."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Learning representations by back-propagating errors (1986).pdf"]
tags: [training, optimization, foundational]
aliases: ["Rumelhart, Hinton & Williams 1986", "Backpropagation", "Backprop"]
---

# Paper — Learning representations by back-propagating errors (1986)

## Metadata

- **Venue:** Nature 323, pages 533-536, published 1986-10-09.
- **Authors:** David E. Rumelhart (UC San Diego), Geoffrey E. Hinton (Carnegie Mellon), Ronald J. Williams (UC San Diego).

## Impact

Gave the field a general, efficient way to train networks with hidden units: repeatedly adjust every weight in the network by the gradient of the output error with respect to that weight, computed by propagating the error backward from the output layer to the input layer using the chain rule.
Before this, it was easy to train a network with no hidden layer (a perceptron), but Minsky and Papert's *Perceptrons* (1969) had shown such networks are fundamentally limited in what they can represent, and there was no known general procedure for training the hidden units that would fix this.
Backpropagation demonstrated that hidden units can learn to represent whatever internal features the task actually requires - not because anyone specified what those features should be, but because gradient descent discovers them on its own.
This is the training procedure underneath essentially every neural network trained since, including every paper on this wiki's canon.

## Core idea

The paper frames training as gradient descent on a single scalar: the total squared error between the network's actual output and the desired output, summed over all training cases.
Computing that gradient for the weights feeding directly into the output layer is straightforward calculus.
The harder problem is the weights feeding into *hidden* units, which have no directly specified target - what should a hidden unit's activation have been?
Backpropagation answers this by running the chain rule backward: the error gradient at a hidden unit is computed from the gradients of the units in the layer above it, weighted by the connections between them, which is exactly the mechanical reverse of how activations flowed forward.
The paper's own illustration is a network trained to detect mirror symmetry in a 6-bit input vector using just two hidden units - a task that literally cannot be solved without a hidden layer, since no linear combination of individual input bits carries any information about symmetry of the whole vector.
Backpropagation finds a solution where the two hidden units develop weights that are exact mirror images of each other, without ever being told that symmetry-detection was the right internal strategy.
A second experiment goes further: trained on family-tree relationships (e.g. "Colin's aunt is Jennifer"), the network's hidden units spontaneously organize themselves along axes corresponding to nationality and generation - distributed internal representations that were never specified as targets, only discovered because they were useful for reducing the output error.

## Why it endures

Backpropagation is not merely one training method among many; it is the mechanism by which every gradient-based deep network on this wiki learns, from the LSTMs and Transformers built on top of it to the optimizers (Adam) that refine how its gradient updates are applied.
The paper does not solve every problem with training deep networks - it notes its own main limitation, that the error surface can contain poor local minima - but the two-pass forward/backward procedure it describes is unchanged in essence in every modern deep learning framework's autodiff engine.

## Limitations

The paper is explicit that its learning procedure is not intended as a plausible model of learning in biological brains, and that gradient descent's chief practical risk is getting stuck in local minima, though the authors note this was rare in their own experiments except in networks with barely enough capacity to solve the task.

## Relations

- Backpropagation as described here computes gradients through arbitrarily many layers in a single forward/backward pass, but the paper's own recurrent-network extension (mapping an iterative net onto an equivalent layered net) sets up exactly the vanishing/exploding gradient problem that [[Paper — Long Short-Term Memory (1997)]] later solves for long time lags.
- Every optimizer on this wiki, including [[Paper — Adam - A Method for Stochastic Optimization (2014)]], operates on the gradients this paper's procedure computes - backpropagation supplies the gradient, the optimizer decides how to step with it.
- The vanishing-gradient-through-depth problem this procedure suffers from in very deep feedforward networks is what [[Paper — Batch Normalization (2015)]] and [[Paper — Deep Residual Learning for Image Recognition (2015)]] later address directly.

## Up

- [[Cluster — Foundational canon]]
