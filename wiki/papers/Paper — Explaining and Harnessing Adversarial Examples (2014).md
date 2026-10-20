---
type: paper
status: mature
updated: 2026-10-20
summary: "Argues adversarial examples arise because neural networks are too linear, not too nonlinear - in high dimensions, many tiny input changes aligned with the weights add up to a large output change - which explains why they transfer across models and yields the fast gradient sign method (FGSM), a one-step attack cheap enough to make adversarial training practical."
cluster: ["Adversarial robustness & security", "Foundational canon"]
sources: ["raw/papers/adversarial-robustness-and-security/Paper — Explaining and Harnessing Adversarial Examples (2014).pdf"]
tags: [adversarial-examples, adversarial-training, fgsm, robustness, linearity]
aliases: ["arxiv:1412.6572", "Goodfellow et al. 2014", "FGSM", "fast gradient sign method", "linearity hypothesis"]
---

# Paper — Explaining and Harnessing Adversarial Examples (2014)

Commonly known as **FGSM** (the fast gradient sign method it introduces).

## Metadata

arXiv:1412.6572 (v3, Mar 2015; originally Dec 2014); ICLR 2015.
Ian J. Goodfellow, Jonathon Shlens, and Christian Szegedy (Google).

## Impact

[[Paper — Intriguing Properties of Neural Networks (2013)]] found that imperceptible perturbations fool neural networks, and the leading speculation blamed their extreme nonlinearity.
This paper argues the opposite - networks are fooled because they are too *linear* - and that one idea explains both the puzzle of why adversarial examples exist and the bigger puzzle of why they transfer between different models.
It also turned the finding into a tool: a one-line attack, cheap enough to generate adversarial examples on every training step, which made adversarial training cheap enough to be practical.
FGSM became a standard baseline attack and the starting point for the stronger iterative attacks and defenses that followed.

## Problem it solved

Adversarial examples were a mystery, explained speculatively by deep networks' extreme nonlinearity, perhaps combined with insufficient model averaging and regularization.
None of these accounted for transfer - why models with different architectures, trained on different data, are fooled by the same input, often into the same wrong class.
And training on adversarial examples, which [[Paper — Intriguing Properties of Neural Networks (2013)]] had shown can regularize a network, was impractical because finding them required expensive constrained optimization.

## Core idea

Take a linear model and nudge every input feature by a tiny amount ε in whichever direction raises the wrong output - the sign of the corresponding weight.
Each nudge is too small to notice, but the output changes by ε times the sum of all the weights' magnitudes, which grows with the number of features.
In a 1,000-dimensional input, a thousand imperceptible changes add up to a large one; the authors call this "accidental steganography."
So even a simple linear classifier has adversarial examples if its input is high-dimensional enough.
Neural networks are built to behave nearly linearly - ReLUs, LSTMs, and maxout units are designed that way because it makes them easy to train - so they inherit the same weakness.

The same reasoning gives the attack.
Linearize the loss around the input and take one step: **x + ε · sign(∇ₓ J(θ, x, y))**, where J is the training loss.
That is the fast gradient sign method, computed with a single backpropagation.

It also explains transfer: adversarial examples are not isolated pockets but broad directions in input space, and models trained on the same task learn similar weights, so a direction that fools one tends to fool another.

## How it's built

FGSM is tested on shallow softmax regression, maxout networks on MNIST, convolutional maxout networks on CIFAR-10, and GoogLeNet on ImageNet.
Adversarial training mixes the normal loss with the loss on FGSM examples generated fresh against the current model each step (equal weights), compared with L1 weight decay and random-noise training as controls.
The paper then tests alternative explanations - generative training, ensembling - and examines RBF networks, whose units respond only near specific points.

## Results

- FGSM with ε = 0.25 makes a shallow softmax classifier misclassify 99.9% of MNIST test examples, with 79.3% average confidence, and a maxout network 89.4% with 97.6% confidence; a convolutional maxout network on CIFAR-10 misclassifies 87.15% at ε = 0.1.
- An imperceptible ε of 0.007 (one bit of an 8-bit image) changes GoogLeNet's classification of an ImageNet image.
- Adversarial training of a maxout network with dropout lowers clean MNIST test error to 0.782%, then the best reported on the permutation-invariant task (statistically tied with dropout-fine-tuned DBMs at 0.79%), and cuts error on FGSM examples from 89.4% to 17.9% - though when it is fooled, it is still confident (81.4% average).
- Random noise of the same size barely helps: noise-trained networks still misclassify 86-90% of FGSM examples, since random noise is almost orthogonal to the damaging direction.
- Generative training (an MP-DBM, 97.5% adversarial error) and an ensemble of 12 maxout networks (91.1%) are not resistant.
- RBF networks, which are far less linear, are fooled 55.4% of the time but with only 1.2% confidence - they know when they don't know.
- Adversarial examples occupy broad, contiguous regions along the FGSM direction, not fine pockets; and when a maxout network and a softmax model both err on the same example, they agree on the wrong class 84.6% of the time.

## Why it endures

FGSM remains a common first-pass attack, and adversarial training - training on worst-case inputs generated against the current model - remains the main empirical defense, refined by [[Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)]] into iterated, stronger attacks.
Its linearity argument reframed adversarial vulnerability as a cost of the very design choices that make networks trainable, a tension between easy optimization and robustness that still shapes the field.
The same idea of following the input gradient carries into language models, where gradient-based attacks like the one in [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] search for inputs that push outputs in a chosen direction.

## Limitations

The authors acknowledge that the linear hypothesis doesn't explain all of the maxout network's mistakes or all cross-model transfer, only a significant proportion.
FGSM's single sign step can't anticipate how the attack changes as the model updates, and differentiable alternatives they tried regularized less well.
Their attempts to build models with enough nonlinearity to resist perturbation, such as deep RBF networks, could not be trained to low error with gradient descent.
The paper evaluates FGSM-trained models only against FGSM; later work in [[Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)]] found FGSM training can leave models vulnerable to iterative attacks.
They also note that in the underfitting regime, adversarial training simply worsens underfitting.

## Tension / update

Supersedes the "dense low-probability pockets" account in [[Paper — Intriguing Properties of Neural Networks (2013)]], which explained adversarial examples as rare points tiling input space like the rational numbers among the reals.
This paper argues the opposite - they occupy broad subspaces along directions aligned with the weights - which also explains transfer, something the earlier account could not.
The two papers share two authors (Szegedy and Goodfellow), and the later reading is widely treated as the better explanation, though the linearity account itself was later contested.

## Relations

- Follows [[Paper — Intriguing Properties of Neural Networks (2013)]], replacing the prevailing nonlinearity speculation and its dense-pockets account with linearity, and its expensive L-BFGS attack with a one-step gradient method.
- The one-step attack that [[Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)]] iterates into PGD, noting that training against FGSM alone doesn't give genuine robustness.
- The empirical counterpart to certified defenses such as [[Paper — Certified Adversarial Robustness via Randomized Smoothing (2019)]], which prove robustness within a radius rather than measuring it against an attack.
- Its linearity account sits alongside the superposition account of [[Paper — Toy Models of Superposition (2022)]] on [[Concept — Adversarial examples]].

## Up

- [[Cluster — Adversarial robustness & security]]
- [[Cluster — Foundational canon]]
