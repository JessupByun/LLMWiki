---
type: cluster
status: in-progress
updated: 2026-09-19
summary: "Reverse-engineering the internal computations of trained neural networks into human-interpretable algorithms, weights, and features, via three distinct lineages: hand-verified circuits, sparse dictionary learning, and top-down representation reading and control."
cluster: []
sources: []
tags: [interpretability, mechanistic-interpretability, transformers, safety]
aliases: ["mech interp"]
---

# Cluster — Mechanistic interpretability

Attempting to reverse engineer the detailed computations a trained neural network performs, the way a programmer might reverse engineer a compiled binary into human-readable source code.
The motivating bet, made explicit by the paper that opens this cluster, is that this could eventually let researchers explain a model's current safety problems, catch new ones, and anticipate the failure modes of future models that haven't been built yet - a more systematic alternative to purely behavioral evaluation.

This cluster starts where the field starts for language models: the smallest possible toy transformers, studied until a simple, general mechanism (the induction head) falls out of the math.
Everything downstream - from measuring that mechanism at scale to the polysemanticity/superposition problem it runs straight into once MLP layers are added back in - inherits this cluster's founding move: rewrite the model, without changing it, into a form where its parameters are directly interpretable.

Three genuinely distinct lineages now converge on that shared goal.
The **circuit-analysis** lineage traces a specific behavior down to the individual attention heads or features causally responsible for it, whether by painstaking hand-verified causal intervention in a real (non-toy) model or, at much greater scale, by an automated pipeline that builds a full causal graph for an arbitrary prompt.
The **sparse dictionary-learning** lineage decomposes a model's activations into a large, mostly-monosemantic basis of individually interpretable features, without yet explaining how those features causally combine.
The **representation-engineering** lineage skips both of these bottom-up steps entirely, treating a whole activation-space direction for a high-level concept - honesty, power-seeking, harmfulness - as the right unit of analysis on its own, extracted from nothing more than contrastive prompts.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — A Mathematical Framework for Transformer Circuits (2021)]] | Opens mechanistic interpretability for transformer language models: reframes attention as independent, additive read/write operations on a shared residual stream, and discovers the induction head, the first general in-context-learning circuit found in an actual transformer. |
| [[Paper — In-context Learning and Induction Heads (2022)]] | Extends induction heads from a toy-model curiosity to a candidate explanation for most in-context learning in transformers of any size, via a purely behavioral definition, causal ablations, and the discovery of a universal early-training "phase change." |
| [[Paper — Toy Models of Superposition (2022)]] | Demonstrates, in a fully understood toy model, that neural networks represent more features than they have neurons by packing them into almost-orthogonal directions - resolving the first paper's admitted blind spot around MLP-layer polysemanticity and setting the dictionary-learning research agenda this cluster pursues next. |
| [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]] | Trains a sparse autoencoder on a one-layer transformer's MLP activations and extracts thousands of far-more-interpretable-than-neurons features, giving the first compelling proof that dictionary learning can recover real structure from superposition. |
| [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]] | Scales the same sparse-autoencoder technique from a one-layer toy model up to a real production model (Claude 3 Sonnet), recovering millions of abstract features and giving the first causal steering evidence (the Golden Gate Bridge feature) that a recovered feature actually drives model behavior. |
| [[Paper — Interpretability in the Wild - a Circuit for Indirect Object Identification in GPT-2 small (2022)]] | This cluster's first hand-verified circuit in a real, non-toy model: reverse-engineers 26 attention heads implementing indirect object identification in GPT-2 small, and introduces path patching plus quantitative criteria (faithfulness, completeness, minimality) for checking a claimed circuit is actually correct. |
| [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]] | A genuinely different, top-down approach: extracts a single "reading vector" for a high-level concept from unlabeled contrastive prompts, using it to both detect the concept (lie detection) and steer generation toward or away from it, with an explicit early claim of traction on deceptive alignment. |
| [[Paper — Circuit Tracing - Revealing Computational Graphs in Language Models (2025)]] | The direct sequel to Scaling Monosemanticity's own "what's next" question: introduces cross-layer transcoders and attribution graphs to trace how interpretable features causally combine into a specific output, not just list which features exist. |
| [[Paper — On the Biology of a Large Language Model (2025)]] | Applies attribution graphs to Claude 3.5 Haiku across roughly ten case studies, including mechanistic confirmation that a model's chain-of-thought can be fabricated to match a user's hint, and a hidden training-time goal exposed despite the model never admitting to it. |

## Methods and concepts

- [[Concept — Induction heads]]
- [[Concept — Residual stream]]
- [[Concept — Superposition]]
- [[Method — Sparse Autoencoder (SAE)]]

## Open threads

Group W is done as of this ingest, closing out Wang et al. (IOI), Zou et al. (Representation Engineering), and Anthropic's Circuit Tracing methods-and-applications pair - this cluster no longer traces exclusively back to the Anthropic Transformer Circuits Thread's own SAE lineage, and now has all three of the field's major methodological approaches represented.
Scaling Monosemanticity's own open question - dictionary completeness remains far off even at 34M features - is picked up directly by Circuit Tracing's cross-layer transcoders, which are explicitly framed as the next step past a plain feature dictionary rather than a bigger one.
[[Question — Is in-context learning genuine learning or pattern recognition]] tracks the open thread between this cluster's induction-head evidence and [[Concept — In-context learning]]'s behavioral framing in [[Cluster — Foundational canon]].
`BACKLOG.md`'s Group X (opening a new Capability evals cluster, anchored on METR's task-horizon paper) is next whenever reading continues.

## Up

Top-level cluster; no parent.
