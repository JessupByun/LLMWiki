---
type: concept
status: in-progress
updated: 2026-09-19
summary: "A network representing more features than it has dimensions by packing them into almost-orthogonal directions, tolerating interference when sparsity makes that cheap."
cluster: ["Mechanistic interpretability"]
sources: []
tags: [interpretability, mechanistic-interpretability, superposition, polysemanticity]
aliases: []
---

# Concept — Superposition

## Definition

Superposition is what happens when a neural network represents more features than it has dimensions (or neurons) available, by encoding them as directions that are almost, but not exactly, orthogonal.
Because the directions aren't fully orthogonal, one feature activating causes slight "interference" on the others - a cost the network tolerates because, when features are **sparse** (rarely active at the same time), the interference is rare and cheap while the extra representational capacity is valuable.
A linear model without a nonlinearity can never do this - it collapses to something like PCA, using at most as many directions as it has dimensions - which is why superposition specifically requires a nonlinearity such as ReLU to filter out the resulting noise.

This is the leading explanation for **polysemantic neurons**: neurons that respond to several, apparently unrelated features, rather than one clean concept.
A polysemantic neuron is what superposition looks like when viewed one basis direction at a time.

## Why it works

Two mathematical facts make superposition possible where naive intuition says it shouldn't be: high-dimensional spaces admit exponentially many "almost orthogonal" directions even though they only admit a fixed number of exactly orthogonal ones (the Johnson-Lindenstrauss phenomenon), and compressed sensing shows that a sparse vector can often be recovered even after projection into a much lower-dimensional space.
[[Paper — Toy Models of Superposition (2022)]] demonstrates this directly: a small ReLU network trained on synthetic sparse features learns to represent exactly as many features as it has dimensions when features are dense, but as sparsity increases, it starts packing in extra features by arranging them geometrically - first as antipodal pairs, then into more elaborate shapes (triangles, pentagons, tetrahedra, square antiprisms) that turn out to be solutions to the same Thomson problem chemists use to find low-energy arrangements of charged particles on a sphere.

The transition between "feature not represented," "feature stored in superposition," and "feature given a dedicated dimension" is a genuine phase change, with discontinuous jumps in the optimal weight configuration as sparsity or feature importance crosses a threshold - directly paralleling the training-time phase change [[Paper — In-context Learning and Induction Heads (2022)]] finds for induction heads, and the broader phenomenon of grokking.

Superposition isn't limited to storage: the same paper shows a network can perform actual computation (specifically, absolute value) on features while they remain in superposition, using an asymmetric weight motif that converts otherwise-costly positive interference into cheap negative interference.
This means polysemantic neurons aren't necessarily wasted or noisy - they can be doing real, identifiable work.

## Why it matters for safety

Superposition is presented as the central obstacle to a specific, ambitious safety agenda: enumerating every feature a model represents, in order to make claims like "this model has no feature for deceptive behavior."
Without superposition, enumerating features would just mean enumerating neurons.
With it, the number of features a model can represent is effectively unbounded relative to its neuron count, and the mapping from neurons to concepts breaks down - motivating "solving superposition" (either training models that don't use it, or finding an overcomplete basis that decodes it after the fact) as a named research goal, directly setting up the dictionary-learning line of work (Towards Monosemanticity, Scaling Monosemanticity) queued next in this wiki.

## Relations

- Demonstrated and named in [[Paper — A Mathematical Framework for Transformer Circuits (2021)]]'s admitted blind spot: MLP-layer neurons resist clean interpretation, which [[Paper — Toy Models of Superposition (2022)]] explains directly.
- Competes for scarce "bandwidth" in the [[Concept — Residual stream]], the mechanism the 2021 paper first speculated might drive polysemanticity.
- Increases vulnerability to [[Concept — Adversarial examples]] by more than 3x as it forms, tracking the number of features packed per dimension.
- Motivates the sparse-dictionary-learning approach taken by the *Towards Monosemanticity* and *Scaling Monosemanticity* papers (both queued next in this wiki) as "Approach 2: finding an overcomplete basis."

## Up

- [[Cluster — Mechanistic interpretability]]
