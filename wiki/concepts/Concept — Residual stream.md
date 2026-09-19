---
type: concept
status: in-progress
updated: 2026-09-19
summary: "The running additive sum of a transformer's token embedding and every subsequent layer's output, reframed as a shared communication channel with no privileged basis."
cluster: ["Mechanistic interpretability"]
sources: []
tags: [interpretability, mechanistic-interpretability, transformers]
aliases: []
---

# Concept — Residual stream

## Definition

The residual stream is the value carried, per token, through a transformer's depth: the original token embedding plus every attention and MLP layer's output added in along the way.
No layer reads or transforms the stream as a whole - each layer performs its own arbitrary linear projection to "read" a slice of it as input, computes its own output, and adds another linear projection back in to "write" its result.
Because every interaction with it is linear and additive, the stream has no privileged basis: rotating the entire stream, and correspondingly every layer's read and write matrices, leaves model behavior completely unchanged.

## Why it matters

Treating the residual stream as a shared communication channel rather than a meaningful per-layer "state" is what makes end-to-end path analysis possible.
Because reading and writing are both linear, the effect of any layer on any later layer - however many layers sit between them - can be multiplied out directly into a single "virtual weight" matrix, without needing to trace through every intervening layer explicitly.
This is the mechanical trick underneath [[Paper — A Mathematical Framework for Transformer Circuits (2021)]]'s entire path-expansion method, and underneath its discovery of [[Concept — Induction heads]] specifically: an induction head's key vector is built by directly reading what a first-layer "previous-token" head wrote into the stream, several layers away in wall-clock terms but zero layers away in virtual-weight terms.

The stream's dimensionality is fixed, but the number of components trying to write to it (attention heads, and especially MLP neurons) grows with model depth and width, so later layers are effectively competing for scarce "bandwidth."
The 2021 paper flags this as a candidate explanation for why individual neurons resist clean interpretation once MLP layers are involved - the phenomenon its own admitted blind spot points toward, and which Elhage et al.'s *Toy Models of Superposition* (2022, queued next in this wiki) takes up directly.

## Relations

- Introduced as a deliberate, mathematically-equivalent reframing of standard transformer computation in [[Paper — A Mathematical Framework for Transformer Circuits (2021)]].
- The scarce-bandwidth argument here anticipates *Toy Models of Superposition* (2022, not yet ingested)'s account of superposition.
- [[Concept — Induction heads]] is a circuit defined entirely in terms of reading and writing specific subspaces of this stream.

## Up

- [[Cluster — Mechanistic interpretability]]
