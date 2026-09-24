---
type: concept
status: in-progress
updated: 2026-09-24
summary: "Determining whether a specific data record was part of a trained model's training set, using only the model's own predictions on that record - a privacy attack that exploits the tendency of trained models to behave more confidently on inputs they were trained on than on inputs they weren't."
cluster: ["ML security & privacy"]
sources: []
tags: [privacy, membership-inference, ml-security]
aliases: []
---

# Concept — Membership inference

## Definition

Given a data record and query access to a trained model, membership inference asks a binary question: was this specific record part of the model's training set?
This is a different, narrower question than *model inversion* (reconstructing what a class of training inputs typically looks like) - membership inference targets one particular, already-known record, and succeeds or fails against ground truth that either does or doesn't hold.

## Why it works

Trained models generalize imperfectly: a model's confidence and the shape of its full prediction vector tend to be measurably different on inputs it saw during training versus inputs drawn from the same distribution that it never saw, an asymmetry [[Paper — Membership Inference Attacks Against Machine Learning Models (2017)]] traces most directly to overfitting, though it shows overfitting is not the only contributing factor - model type and structure independently affect how much a model leaks even at matched overfitting levels.
Because this asymmetry is a side effect of ordinary training rather than a deliberate flaw, membership inference works purely from a model's outputs with no assumption about its internals, making it a genuine black-box privacy risk for any model whose predictions an attacker can query.

## Relations

- Introduced and demonstrated by [[Paper — Membership Inference Attacks Against Machine Learning Models (2017)]] via its founding [[Method — Shadow model attack]] technique.
- Distinguished from model inversion, which reconstructs an average class-representative input rather than testing a specific record's membership.
- Defeated by construction, rather than merely mitigated, by [[Concept — Differential privacy]]: a differentially private model's output distribution is guaranteed not to depend meaningfully on whether any single record was included in training, which directly bounds the success of any membership-inference attack against it.

## Up

- [[Cluster — ML security & privacy]]
