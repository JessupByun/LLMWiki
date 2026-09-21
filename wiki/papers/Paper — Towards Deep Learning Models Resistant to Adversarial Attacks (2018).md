---
type: paper
status: mature
updated: 2026-09-20
summary: "Frames adversarial robustness as a min-max saddle-point optimization problem, shows that projected gradient descent (PGD) reliably solves the inner, non-concave maximization despite no theoretical guarantee that it should, and uses PGD-generated adversarial examples as training data to produce the first MNIST and CIFAR10 models robust to a genuinely broad range of attacks rather than just the one they were trained against."
cluster: ["Adversarial robustness & security", "Foundational canon"]
sources: ["raw/papers/adversarial-robustness-and-security/Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018).pdf"]
tags: [adversarial-examples, adversarial-training, robustness, security, pgd]
aliases: ["arxiv:1706.06083", "Madry et al. 2018", "PGD adversarial training"]
---

# Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)

## Metadata

arXiv:1706.06083 (v4, Sep 2019; ICLR 2018).
Aleksander Mądry, Aleksandar Makelov, Ludwig Schmidt, Dimitris Tsipras, Adrian Vladu.
MIT.

## Impact

Before this paper, adversarial defenses were evaluated piecemeal - a method would be shown to resist one specific attack, then broken by the next slightly more clever one, with no way to know in advance which side would win the next round.
This paper reframes the entire problem as a single, precise optimization statement - a min-max saddle point - which unifies attacks and defenses as two ends of the same equation and, for the first time, gives adversarial robustness a **guarantee** rather than a demonstration: a model with low enough saddle-point value is provably safe against every attack the threat model allows, not just the ones tested.
It backs this with the first MNIST and CIFAR10 models robust to a genuinely broad range of strong attacks (white-box, black-box, and multiple attack algorithms) rather than the single attack they happened to be trained against, and the training method it introduces - adversarial training against projected gradient descent, universally shorthanded **PGD adversarial training** - became the standard baseline essentially the entire adversarial-robustness field measures itself against.

## Problem it solved

Prior defenses (defensive distillation, feature squeezing, various detection schemes) offered no way to know what class of attacks they actually protected against, only that they survived whichever specific attacks their authors happened to test.
This left the field unable to make real progress: a new paper could always find a slightly different attack that broke the previous best defense, with no underlying theory to say when that arms race would end or which side had the structural advantage.
The paper's response is to demand a **guarantee** instead of a demonstration - a precise attack model specifying exactly which perturbations an adversary is allowed to make, and a training objective whose value, if driven low enough, certifies safety against every attack inside that model at once.

## Core idea

Cast robust classification as a saddle-point problem: minimize, over model parameters, the expected loss under the *worst* perturbation an adversary could make within an allowed set (an l∞ ball of radius ε around each input, in this paper's main experiments).
The inner maximization - find the worst perturbation for a fixed model - is exactly the problem of attacking the network; the outer minimization - find parameters that make even the worst perturbation's loss small - is exactly the problem of training a robust classifier.
This reframing is not just notation: it means an attack and a defense that both aim at the same saddle point automatically inherit whatever guarantee that saddle point provides, and prior attack methods (FGSM and its variants) and prior defenses (adversarial training with those same weak attacks) turn out to be special cases of specific, sometimes crude, ways of approximating one half of this single optimization problem.
The paper's central empirical surprise is that although the inner maximization is a highly non-concave problem with no reason to expect it's tractable, **it is tractable anyway**: running projected gradient descent (PGD) from many random starting points inside the perturbation ball consistently finds local maxima with strikingly similar loss values, evidence that PGD already behaves like a "universal" first-order adversary - if a network is trained to be robust against PGD specifically, it becomes robust against essentially any attack that only has access to first-order gradient information, not just PGD itself.

## How it's built

PGD is simply iterated, projected gradient ascent on the loss with respect to the input, re-projecting back into the allowed perturbation ball after each step, restarted from a uniformly random point inside that ball rather than from the clean example itself.
Training then directly optimizes the outer minimization by taking gradient steps at the PGD-found adversarial example rather than the clean one - a move the paper justifies via Danskin's theorem, which states that the gradient at an inner maximizer is a valid descent direction for the outer problem, even though the theorem's exact technical assumptions don't strictly hold for a ReLU/max-pooling network.
Two datasets anchor the experiments: MNIST, with a small convolutional network and l∞ perturbations of ε = 0.3, and CIFAR10, with a ResNet (and a 10x-wider variant to test capacity) and perturbations of ε = 8, evaluated against a battery of attacks - white-box PGD at varying iteration counts and random restarts, a Carlini-Wagner-style loss variant, and black-box transfer attacks from independently-trained copies and from a different architecture entirely.

## Results

The best MNIST model reaches 89.3% accuracy under the strongest white-box adversary tested (100 PGD steps, 20 restarts) and above 95% under every black-box transfer attack tried; the best CIFAR10 model reaches 45.8% under its strongest white-box adversary, far short of MNIST's robustness but a genuine result against attacks the field had not previously defended against simultaneously, and the paper is candid this result is "far from satisfactory."
Model capacity turns out to be load-bearing, not incidental: a network needs meaningfully more capacity to draw a robust decision boundary (one that separates entire perturbation balls around each point, not just the points themselves) than to merely classify clean examples well, and small-capacity networks forced to train against a strong PGD adversary can fail to learn anything useful at all, collapsing to predicting a single fixed class rather than sacrificing any robustness budget on real discrimination.
Training against the weaker FGSM attack instead of PGD does not produce genuine robustness at the same perturbation size - the network instead overfits to that specific weak attack's characteristic perturbations, a failure mode the paper calls **label leaking**, and shows every sign of security through obscurity rather than real robustness once a stronger adversary is applied.
Both stronger adversaries used during training and higher model capacity independently reduce how well adversarial examples transfer between independently-trained models, consistent with the paper's account of what's actually happening: capacity and strong-adversary training are pushing the decision boundary toward the specific, harder-to-share geometry a genuinely robust classifier needs.

## Why it endures

The min-max saddle-point framing becomes the default lens the entire adversarial-robustness field is described through afterward, and PGD adversarial training becomes the field's standard baseline defense - the thing every new proposed defense has to be compared against, and, in a large number of later cases, the thing many defenses that skip this comparison turn out to be silently broken by.
The paper explicitly invites the community to attack its own released MNIST and CIFAR10 models as an open challenge, a practice that becomes normal for adversarial-robustness papers afterward, precisely because the field had learned from exactly this paper's own framing that a defense's real strength can only be established by inviting attacks, not by testing against the ones the authors thought of themselves.

## Limitations

CIFAR10 robustness (45.8%) remains far below what the paper considers satisfactory, and the authors frame further progress as mostly a matter of scaling the same recipe (more capacity, stronger adversaries) rather than a fundamentally different approach.
The robustness guarantee is scoped narrowly to l∞-bounded perturbations of a fixed size; the paper's own l2-bounded follow-up experiments show PGD badly overestimating robustness in that norm specifically, because the model's learned threshold-like filters mask the loss gradient PGD needs to find real adversarial directions, and a gradient-free decision-based attack reveals the model is considerably more brittle than the l∞ results alone would suggest.
The core empirical claim - that PGD-found local maxima are reliably near-globally-optimal - is established only experimentally, via random restarts and structural analysis of the maxima found, not proven; the paper is explicit that isolated, much better maxima could in principle exist and simply be hard for any first-order method, including PGD itself, to find.

## Relations

- The direct security-relevant escalation of [[Paper — Intriguing Properties of Neural Networks (2013)]]'s founding observation: where that paper discovers adversarial examples exist and transfer, this paper is the first to give a training method with an actual optimization-theoretic guarantee against them, rather than an empirically-tuned mitigation.
- [[Concept — Adversarial examples]]'s "why they exist" discussion of dense low-probability pockets and excessive linearity is the direct backdrop this paper's min-max reframing responds to - it sidesteps needing a settled answer to *why* adversarial examples exist by instead specifying a guarantee against *any* perturbation within a fixed, precisely-defined threat model.
- PGD adversarial training is the standard baseline [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] (GCG) explicitly invokes when arguing that the same computationally-expensive, capability-degrading, narrowly-scoped tradeoffs that have kept adversarial training from being deployed against vision models look likely to recur for language models.
- Its saddle-point framing - a defender's outer minimization against an attacker's inner maximization, both aimed at the same precisely-specified objective - is a direct conceptual ancestor of the red-team/blue-team control-evaluation methodology in [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]], though that paper's adversary is a worst-case *model* rather than a bounded input perturbation.

## Up

- [[Cluster — Adversarial robustness & security]]
- [[Cluster — Foundational canon]]
