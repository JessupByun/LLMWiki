---
type: cluster
status: stub
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

Opened ahead of its first ingest: no papers are in yet, but the scope above reflects a deliberate structural decision (agreed 2026-09-24) not to stretch Adversarial robustness & security's already-coherent inference-time-attack identity to cover this different research question.

## Methods and concepts

(To be populated at first ingest.)

## Open threads

Queued candidates, in no particular order: Shokri et al. on membership inference, Abadi et al. on differentially private SGD, Gu et al. (BadNets) on supply-chain backdoors, Carlini et al. on training-data extraction from LLMs, and Carlini et al. on stealing part of a production language model's embedding layer.
Note that certified defenses against adversarial examples (e.g. randomized smoothing) belong in [[Cluster — Adversarial robustness & security]] instead, as the provable counterpart to that cluster's empirical PGD-training defense - not here, despite "certified robustness" sounding adjacent to this cluster's privacy-guarantee flavor.

## Up

Top-level cluster; no parent.
