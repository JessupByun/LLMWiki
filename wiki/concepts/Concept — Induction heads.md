---
type: concept
status: in-progress
updated: 2026-09-19
summary: "An attention-head circuit that predicts the next token by finding the last occurrence of the current token in context and copying whatever followed it - the first general in-context-learning mechanism found inside an actual transformer."
cluster: ["Mechanistic interpretability"]
sources: []
tags: [interpretability, mechanistic-interpretability, in-context-learning, transformers]
aliases: []
---

# Concept — Induction heads

## Definition

An induction head is a discovered circuit, typically a second-layer attention head composing with a first-layer "previous-token" head, that implements a simple but general in-context-learning algorithm: given the current token, search backward through the context for the last time that same token appeared, then predict whatever token immediately followed it there.
Written as a pattern: `[a][b] ... [a] → [b]`.

This is a strictly more powerful mechanism than the naive copying behavior found in one-layer transformers (`[b] ... [a] → [b]`, i.e. "this token appeared recently, so it's probably relevant again"), because it conditions on what specifically followed the matched token rather than just on the token's recent presence.

## Why it works

The mechanism is a specific instance of **K-composition**: a second-layer head's key vector is built by reading from a first-layer head that shifts information one token forward (a "previous-token head").
Because the key at each position now encodes the *previous* token rather than the present one, when the query for the current token searches for a matching key, it finds the position right after the earlier occurrence of the same token - and the head's output-value (OV) circuit then simply copies that next token's identity into the output logits.

The clean demonstration of this is that induction heads work on sequences of uniformly random tokens the model has never seen in training, since the mechanism depends only on matching token identity, not on learned bigram or grammatical statistics.
This is also why induction heads only emerge in models with at least two attention layers: the composition itself (one head's output feeding another head's key) requires the extra layer.

## Behavioral definition and the phase change

[[Paper — In-context Learning and Induction Heads (2022)]] redefines induction heads purely by measurable behavior - prefix matching plus copying on repeated random tokens - so the concept can be tested in models far too large to reverse-engineer directly.
Using this test, it documents "the phase change": early in training, in any model with more than one attention layer, induction heads form abruptly in the same narrow window where in-context learning ability jumps and the training loss visibly bumps, across models from tiny toy transformers up to 13B parameters.
Ablating induction heads at test time in small models destroys almost all measured in-context learning, and the same heads that pass the strict literal-copying test are also shown performing translation and abstract pattern classification in a 13B-parameter model - evidence that one mechanism, not several coincidental ones, is responsible.

## Relations

- Discovered and named in [[Paper — A Mathematical Framework for Transformer Circuits (2021)]], which also gives the QK/OV-circuit vocabulary used to state the mechanism precisely.
- Operates by reading and writing specific subspaces of [[Concept — Residual stream]].
- A qualitatively more powerful successor to the naive token-copying behavior the same paper documents in one-layer transformers.
- Given a purely behavioral definition and causal evidence for its role in [[Concept — In-context learning]] by [[Paper — In-context Learning and Induction Heads (2022)]], up to 13B-parameter models.
- [[Paper — Many-shot Jailbreaking (2024)]] traces its power-law in-context jailbreak effect directly to this mechanism, reproducing the same power-law scaling analytically in a toy induction-head circuit - a live security consequence of the same mechanism that drives ordinary in-context learning.

## Up

- [[Cluster — Mechanistic interpretability]]
