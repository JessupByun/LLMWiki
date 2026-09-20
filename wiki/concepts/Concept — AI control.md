---
type: concept
status: in-progress
updated: 2026-09-19
summary: "A safety approach that assumes a powerful model may be actively trying to subvert its own oversight, and asks whether a protocol still catches it - rather than trying to ensure the model's values are good."
cluster: ["Model organisms of misalignment"]
sources: []
tags: [ai-control, safety, red-teaming]
aliases: []
---

# Concept — AI control

## Definition

An approach to AI safety that gives up on assuming the model is aligned and instead asks: can we build a pipeline of countermeasures around a powerful model such that it stays safe even in the worst case, where the model is intentionally trying to defeat those countermeasures?
This reframes safety as a property of the *protocol* (the pipeline of trusted models, untrusted models, and limited human review that decides what gets acted on), not a property that has to be verified inside the model itself.

Introduced in [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]], which operationalizes this as a **control evaluation**: a red team constructs an adversarial "attack policy" standing in for the untrusted model, a blue team designs a protocol using an untrusted model, a trusted (weaker, known-safe) model, and a small budget of expensive trusted human labor, and safety is the probability the protocol never lets the attack policy succeed.

## Why it works

Control does not require solving alignment first, because it treats "the model might be scheming" as a standing worst-case assumption rather than a risk to be estimated and hopefully ruled out.
This buys real safety margin as long as the untrusted model is not drastically more capable than the trusted model plus human budget combined - the whole approach is a bet on a *capability gap* that is expected to shrink over time as untrusted models improve while trusted models and human reviewers, by construction, do not.
The core protocols in the founding paper - trusted monitoring, untrusted monitoring, and trusted editing (see [[Method — Trusted monitoring]]) - all follow the same shape: use a cheap, trustworthy signal (or a carefully defended expensive one) to decide when the untrusted model's output is worth escalating to expensive human-quality review.

## Relations

- Complements rather than replaces alignment work; assumes what [[Paper — Weak-to-Strong Generalization (2023)]] explicitly assumed away - that the model might not share the developer's objective.
- Generalizes [[Concept — Scalable oversight]] from "the supervision signal is imperfect" to "the thing being supervised may be adversarial."
- Distinct from [[Concept — Jailbreaking (LLMs)]] and [[Concept — Adversarial examples]]: those are attacks *on* a model from an external party; control assumes the model itself is the adversary trying to subvert its own oversight.
- Directly motivated by, and motivates further work on, "model organisms of misalignment" - deliberately constructed instances of the deceptive behavior control protocols are meant to catch.

## Up

- [[Cluster — Model organisms of misalignment]]
