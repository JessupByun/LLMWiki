---
type: paper
status: mature
updated: 2026-09-20
summary: "Introduces cross-layer transcoders and attribution graphs, extending sparse-feature decomposition into genuine causal circuit discovery - tracing how interpretable features combine across layers to produce a specific model output on a specific prompt, rather than only listing which features exist, and validating the resulting graphs by testing whether predicted interventions actually move the model's real activations and logits."
cluster: ["Mechanistic interpretability"]
sources: ["transformer-circuits.pub, Mar 2025"]
tags: [interpretability, mechanistic-interpretability, sparse-autoencoders, circuits]
aliases: ["Circuit Tracing", "cross-layer transcoders", "CLT", "attribution graphs"]
---

# Paper — Circuit Tracing: Revealing Computational Graphs in Language Models (2025)

## Metadata

transformer-circuits.pub, published Mar 2025.
Emmanuel Ameisen, Jack Lindsey, Adam Pearce, Wes Gurnee, Nicholas L. Turner, Brian Chen, Craig Citro, Joshua Batson (correspondence), and a large supporting Anthropic interpretability team including Chris Olah.
Anthropic. Not peer-reviewed and not an arXiv preprint - published on Anthropic's own Transformer Circuits Thread, the same venue as the Monosemanticity papers.

## Impact

[[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]] answered whether sparse-autoencoder feature decomposition scales to a real production model, but a dictionary of millions of individually-interpretable features still doesn't say how the model actually combines them to produce a specific output on a specific input.
This paper is the direct methodological answer to that gap: it introduces **cross-layer transcoders** and **attribution graphs**, a pipeline for tracing the causal computational pathway between interpretable features across layers, turning a static list of "what concepts exist in this model" into an actual circuit explaining "how this concept led to that output, on this exact prompt."
Applied to Claude 3.5 Haiku in a companion paper, the resulting graphs let researchers actually validate whether their causal story is real by intervening on a specific feature and checking whether the predicted downstream change actually happens.

## Problem it solved

Having an SAE's dictionary of interpretable features says nothing about how those features interact, because features at different layers are connected through the model's real, nonlinear MLP blocks, and there was no principled way to attribute one feature's influence on another without somehow getting past that nonlinearity.
Hand-verified circuits like [[Paper — Interpretability in the Wild - a Circuit for Indirect Object Identification in GPT-2 small (2022)]]'s showed this kind of tracing was possible in principle, but only through painstaking, bespoke causal-intervention work built around a specific hypothesis for one narrow task - there was no general, scalable pipeline that could be pointed at an arbitrary behavior in a real production-scale model.

## Core idea

A standard sparse autoencoder is trained to reconstruct a layer's own activations, which is exactly why it can't easily represent cross-layer computation.
A **transcoder** instead learns to predict a *later* layer's output (an MLP block's output) directly from an *earlier* layer's input, bridging over the intervening nonlinearity so that feature-to-feature influence becomes a describable, mostly-linear quantity rather than something buried inside a nonlinear transformation.
A **cross-layer transcoder (CLT)** extends this further: each feature reads from the residual stream at one layer, but its effect is decoded out to every subsequent layer's MLP-output reconstruction simultaneously, which collapses a common failure mode - the same concept getting re-amplified layer after layer in a redundant chain - into a single feature rather than a long, hard-to-follow sequence of near-duplicates.
Once a model's MLPs are replaced by CLT features for a specific prompt (freezing attention patterns and normalization to their real, observed values, and adding back the exact residual reconstruction error so the replacement model's outputs match the original exactly), the whole computation becomes expressible as a large, purely linear graph: an **attribution graph**, with token embeddings and error terms as inputs, individual active features as intermediate nodes, and candidate output tokens as the final layer, connected by edges computed as exact backward-pass attributions.

## How it's built

CLTs are trained across all layers of two target models - an 18-layer internal research model, and Claude 3.5 Haiku - at feature counts ranging from 300K up to 30 million; the largest Haiku CLT reaches a 21.7% normalized reconstruction error with an average of 235 active features per token.
Once trained, an attribution graph for a specific prompt is built by computing direct linear attributions between all active features (and candidate output tokens), then pruning the typically enormous raw graph down roughly tenfold while sacrificing only about 20% of the graph's ability to account for the model's actual behavior, since unpruned graphs can otherwise run to millions of edges even for a short prompt.
A researcher then manually groups related feature nodes into interpretable "supernodes" - no automated clustering method proved good enough to replace this step - and the resulting causal story is validated with **constrained patching**: scaling a specific feature's activation up or down across the layers its decoder writes to, then checking whether the model's real downstream activations and final output logits shift the way the graph predicted.
A separate analysis studies the CLT's raw, prompt-independent "virtual weights" between features to find general circuits (such as an arithmetic circuit's lookup-table features generalizing across dozens of unrelated numeric-reasoning contexts), correcting for a severe interference problem - millions of features sharing one residual stream produce large spurious weights between features that never actually co-activate - with a reweighting technique the paper calls TWERA.

## Results

The largest 18-layer research-model CLT (10 million features) reaches a striking sufficiency benchmark: replacing the real model's MLPs with CLT reconstructions still reproduces the underlying model's actual top-1 next-token prediction on about half of a diverse sample of pretraining-style prompts, a result the paper's own text calls remarkable given the aggressiveness of the substitution.
CLTs consistently and substantially outperform the natural alternative (training one transcoder per layer instead of jointly across layers) on every metric tested - shorter, simpler causal paths from input to output (an average path length of 2.3 versus 3.7 layers on one worked example), higher graph completeness and replacement scores, and better agreement between graph-predicted and empirically-measured feature-to-feature influence, reaching a Spearman correlation of 0.72 between predicted and actual ablation effects - with the CLT-versus-per-layer-transcoder design choice mattering more for graph quality than simply scaling up feature count further.
Validated perturbation effects hold up well one layer downstream of an intervention (about 0.8 cosine similarity to the real model's response) but degrade substantially at greater distance, a limitation the paper is explicit about rather than smoothing over.

## Why it endures

This paper is the direct sequel to the entire SAE dictionary-learning lineage this cluster already documents, extending it from "find the interpretable features" into "explain how those features causally combine to produce a specific behavior," and its companion applications paper is offered as the proof this actually works on a real, deployed model rather than only a toy one.
The specific design choices the authors flag as most likely to be durable - sparse learned features over raw neurons, transcoders over ordinary SAEs specifically because they decompose computation rather than only representation, and cross-layer over per-layer decoding - are framed as the parts of the methodology built to last, while other choices (freezing attention entirely, training on nothing but reconstruction MSE plus sparsity) are explicitly called provisional first steps rather than a finished pipeline.

## Limitations

The authors name seven limitations directly, and the most consequential is that attention patterns are frozen and never explained by the method at all, which can completely obscure the actual interesting part of a computation - demonstrated starkly on a classic induction-head prompt, where the graph shows that information moved from one token to another but nothing about why attention moved it there in the first place, a regression relative to earlier hand-built induction-head analysis for that exact mechanism.
Reconstruction error becomes "dark matter" that can dominate a graph entirely on out-of-distribution prompts (one obfuscated jailbreak example produces a graph that is almost entirely error nodes and therefore useless), inactive features and inhibitory circuits are invisible to the method by default even when they are doing the real work, pruned graphs still routinely contain hundreds of features and thousands of edges requiring labor-intensive manual interpretation, and there is no guarantee a CLT's low reconstruction error means it has actually learned the same mechanism as the real MLP it's standing in for, something validated only coarsely and only after the fact through the perturbation experiments described above.

## Relations

- The direct methodological sequel to [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]] and the whole SAE lineage running back through [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]] and [[Paper — Toy Models of Superposition (2022)]]: those papers answer "what interpretable features exist," this paper answers "how do they causally combine."
- Explicitly aims to generalize and automate the kind of hand-verified circuit work [[Paper — Interpretability in the Wild - a Circuit for Indirect Object Identification in GPT-2 small (2022)]] does manually - the same goal of a validated, causal circuit explanation, but via a scalable pipeline rather than a bespoke investigation built around one specific hypothesis.
- Its companion applications paper, [[Paper — On the Biology of a Large Language Model (2025)]], is the evidence this methodology surfaces real, validated mechanisms - including a chain-of-thought faithfulness case study directly relevant to [[Paper — Language Models Don't Always Say What They Think - Unfaithful Explanations in Chain-of-Thought Prompting (2023)]], and a hidden-goal model-organism case study directly relevant to [[Cluster — Scheming & AI control]].

## Up

- [[Cluster — Mechanistic interpretability]]
