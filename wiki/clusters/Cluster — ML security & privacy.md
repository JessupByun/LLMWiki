---
type: cluster
status: in-progress
updated: 2026-09-24
summary: "Attacks on the ML training pipeline and the trained model's own memory, rather than on its inference-time behavior - membership inference, training-data extraction, data poisoning and backdoors, model stealing, and the differential-privacy defenses built against them."
cluster: []
sources: []
tags: [security, privacy, ml-security]
aliases: []
---

# Cluster — ML security & privacy

A distinct lens from [[Cluster — Adversarial robustness & security]], which is scoped to attacks that exploit a *fixed* model's behavior at inference time - adversarial examples, jailbreaks, prompt injection.
This cluster is scoped to attacks on the training pipeline and the trained model's own memory instead: does the model leak information about what it was trained on (membership inference, training-data extraction), can an adversary corrupt what it learns in the first place (data poisoning, backdoors planted in the supply chain), and can an adversary steal the trained artifact itself (model extraction) rather than merely fooling it.
Differential privacy sits on the defensive side of the same question - bounding, by construction, how much any single training example can influence the final model, which is the direct countermeasure to membership inference and training-data extraction alike.

Its founding three-paper batch spans exactly these three threads: [[Paper — Membership Inference Attacks Against Machine Learning Models (2017)]] shows a trained model's own predictions leak whether a given record was used to train it; [[Paper — Deep Learning with Differential Privacy (2016)]] supplies the principled defense, a formal guarantee bounding any single record's influence on the model by construction; and [[Paper — BadNets - Identifying Vulnerabilities in the Machine Learning Model Supply Chain (2017)]] shows the training process itself can be poisoned to plant a secretly-triggered misbehavior into an otherwise fully-functional model.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — Membership Inference Attacks Against Machine Learning Models (2017)]] | Founding paper: turns a model's own imperfect generalization into a black-box attack that determines whether a specific record was in its training set, reaching 94% accuracy against real commercial ML-as-a-service platforms. |
| [[Paper — Deep Learning with Differential Privacy (2016)]] | The principled defense: adapts differential privacy to deep, non-convex neural network training for the first time at a usable privacy budget, via per-example gradient clipping, calibrated noise, and a much tighter privacy-loss accounting method (the moments accountant). Dual-tagged canon: DP-SGD is now a standard, broadly-taught training technique well beyond privacy-security specialists. |
| [[Paper — BadNets - Identifying Vulnerabilities in the Machine Learning Model Supply Chain (2017)]] | Shows outsourced or downloaded pretrained models can be maliciously backdoored - normal on clean inputs, reliably misbehaving on a secret attacker-chosen trigger - and that the backdoor survives transfer learning into a task the original attacker never anticipated. |

## Methods and concepts

- [[Concept — Membership inference]]
- [[Method — Shadow model attack]]
- [[Concept — Differential privacy]]
- [[Method — Differentially Private SGD (DP-SGD)]]
- [[Concept — Backdoor attack (ML)]]

## Open threads

Two more candidates remain queued for a follow-up batch: Carlini et al. on training-data extraction from LLMs, and Carlini et al. on stealing part of a production language model's embedding layer - both would extend this cluster's coverage from classical/vision-era attacks into the LLM era, the same trajectory [[Cluster — Adversarial robustness & security]] already made from Szegedy 2013 to GCG.
Note that certified defenses against adversarial examples (e.g. randomized smoothing) belong in [[Cluster — Adversarial robustness & security]] instead, as the provable counterpart to that cluster's empirical PGD-training defense - not here, despite "certified robustness" sounding adjacent to this cluster's privacy-guarantee flavor.

## Up

Top-level cluster; no parent.
