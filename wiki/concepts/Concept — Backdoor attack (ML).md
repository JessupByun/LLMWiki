---
type: concept
status: in-progress
updated: 2026-09-24
summary: "A model that behaves normally on ordinary inputs but reliably produces an attacker-chosen output whenever a secret trigger is present, planted by poisoning the training process itself rather than by exploiting a flaw in an already-trained model - stealthy specifically because a user has no reason to test their model against a trigger pattern only the attacker knows."
cluster: ["ML security & privacy"]
sources: []
tags: [backdoor-attack, data-poisoning, ml-security]
aliases: ["BadNet", "trojan attack"]
---

# Concept — Backdoor attack (ML)

## Definition

A backdoored model (or **BadNet**) is trained - via **training set poisoning**, adding attacker-chosen (input, label) pairs to the training data - to implement two behaviors at once: its intended task on ordinary inputs, and an attacker-chosen misclassification whenever an attacker-chosen **trigger** (a visual pattern, in the founding case) is present in the input.
Introduced and demonstrated by [[Paper — BadNets - Identifying Vulnerabilities in the Machine Learning Model Supply Chain (2017)]], which frames the risk as a **model supply chain** problem: a model can pass every accuracy test a user runs and still be compromised, because the input a user would need to test - one containing the secret trigger - is exactly what the user has no way to guess.

## Why it works

Because retraining an entirely standard architecture on poisoned data is sufficient - no structural change to the network at all - a backdoored model is behaviorally indistinguishable from an honest one on any input the user thinks to try, and [[Paper — BadNets - Identifying Vulnerabilities in the Machine Learning Model Supply Chain (2017)]] shows the resulting network develops genuinely dedicated internal machinery for detecting the trigger (visualized as distinct convolutional filters and neuron groups that activate only on triggered inputs), not merely a corrupted version of the honest task.
The attack also survives transfer learning: retraining only a backdoored network's later layers for a new task leaves the earlier, trigger-detecting layers intact, so the backdoor persists into a downstream model the victim built for a purpose the original attacker never anticipated.

## Relations

- Distinguished from [[Concept — Adversarial examples]]: an adversarial example exploits a flaw in an *honestly trained* model, essentially a bug, whereas a backdoor is a deliberately introduced feature of a *maliciously trained* model, present regardless of any defense against adversarial inputs specifically.
- The training-time, data-poisoning sibling to [[Method — Backdoor training]]'s later, LLM-specific generate-then-distill technique for constructing deceptive-alignment model organisms in [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] - both plant a trigger-conditioned behavior during training, but by different mechanisms (direct data poisoning of a classifier here, a two-stage supervised-fine-tuning pipeline there) and for different research purposes (a security demonstration here, a safety-research artifact there).

## Up

- [[Cluster — ML security & privacy]]
