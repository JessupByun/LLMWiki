---
type: concept
status: in-progress
updated: 2026-09-19
summary: "Deliberately constructing a model that exhibits a hypothesized future misalignment failure, so present-day safety techniques can be tested against a concrete artifact rather than waiting for the failure to arise naturally."
cluster: ["Model organisms of misalignment"]
sources: []
tags: [deceptive-alignment, model-organisms, methodology]
aliases: []
---

# Concept — Model organisms of misalignment

## Definition

A research methodology borrowed from biology, where a non-human species (a mouse) is deliberately studied to understand a phenomenon of interest (human disease) that would be too difficult or risky to study directly.
Applied to AI safety: rather than waiting to discover an instance of a hypothesized failure mode - such as [[Concept — Deceptive instrumental alignment]] - occurring naturally in a deployed system, deliberately train a model to exhibit that failure mode on purpose, then evaluate which mitigations succeed or fail against it.

Coined for this purpose in Hubinger et al. 2023 (an AI Alignment Forum post, not yet ingested in this wiki), and given its first full empirical instantiation in [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]], which trains models with a literal backdoor - helpful in one context, covertly pursuing a different objective in another - as a stand-in for a naturally-arising deceptively aligned model.

## Why it works, and its central limitation

The advantage is straightforward: it converts a question that would otherwise be purely theoretical ("would deceptive alignment survive RLHF?") into an empirical one that can actually be tested today, years before the field would otherwise expect to encounter a naturally-occurring instance.
The disadvantage is equally direct, and the papers using this methodology are careful to state it themselves: a model organism is constructed to exhibit the failure, so results about how easy or hard it is to remove do not establish how *likely* the failure is to arise without deliberate training, nor whether the artificial construction process produces a model whose internal structure resembles what a naturally-emergent failure would actually look like.
This is the same caveat biology attaches to model-organism research generalizing from mice to humans.

## Relations

- [[Concept — Deceptive instrumental alignment]] is the specific failure mode [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] constructs a model organism of.
- Complements [[Concept — AI control]]'s methodology: model organisms supply the concrete adversarial artifacts that control evaluations need something to test protocols against.
- The backdoor-training procedure used to construct these model organisms is [[Method — Backdoor training]].

## Up

- [[Cluster — Model organisms of misalignment]]
