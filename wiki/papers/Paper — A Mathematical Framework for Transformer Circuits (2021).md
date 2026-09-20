---
type: paper
status: mature
updated: 2026-09-19
summary: "Rewrites toy transformers as sums of interpretable end-to-end paths from tokens to logits, and discovers the induction head - the first general in-context-learning circuit found inside an actual model."
cluster: ["Mechanistic interpretability"]
sources: ["https://transformer-circuits.pub/2021/framework/index.html"]
tags: [interpretability, mechanistic-interpretability, transformers, in-context-learning]
aliases: ["Transformer Circuits", "Elhage et al. 2021"]
---

# Paper — A Mathematical Framework for Transformer Circuits (2021)

## Metadata

Elhage, Nanda, Olsson, Henighan, Joseph, Mann, Askell, Bai, Chen, Conerly, DasSarma, Drain, Ganguli, Hatfield-Dodds, Hernandez, Jones, Kernion, Lovitt, Ndousse, Amodei, Brown, Clark, Kaplan, McCandlish & Olah, Anthropic, published December 22, 2021, on the Transformer Circuits Thread (transformer-circuits.pub, no arXiv id).

## Impact

Opens Anthropic's Transformer Circuits Thread and, with it, mechanistic interpretability for transformer language models - the direct sequel to the earlier Distill Circuits thread's reverse-engineering program for vision models (InceptionV1).
Its two lasting contributions are a vocabulary (the residual stream as a shared communication channel, and the QK/OV circuit decomposition of attention) that nearly all of Anthropic's later interpretability work is written in, and a single concrete discovery: the **induction head**, a specific attention-head circuit that performs a simple but general in-context-learning algorithm.
A forthcoming sequel (referenced in this paper but not yet published at the time) goes on to measure how much of in-context learning in much larger, realistic models this same mechanism explains.

## Problem it solved

Before this paper, studying transformer attention meant looking at attention patterns and describing them empirically - which tokens a head attends to, and correlating that with grammatical or positional structure (the body of work known as "Bertology").
That approach treats attention weights as data to be described, not as one piece of an end-to-end causal account of how information from an input token actually reaches the output logits.
It also didn't explain a sharp empirical jump: two-layer transformers are dramatically better at in-context learning than one-layer transformers, and nobody had an algorithmic account of why an extra layer would matter that much.

## Core idea

Rewrite a transformer's forward pass - with no architecture change and no retraining - as a sum of interpretable, end-to-end paths from input tokens to output logits, instead of a sequence of opaque layers.
Two mathematical moves make this possible.

First, treat the **residual stream** (see [[Concept — Residual stream]]) as a shared communication channel: every layer reads a linear projection of it and adds a linear projection of its own output back in.
Because this is purely linear and additive, the effect of any layer on any later layer can be multiplied out into a single "virtual weight" matrix connecting them directly, however many layers sit in between, and the whole model's computation can be expanded from a *product* of layers into a *sum* of independent path terms.

Second, split every attention head into two decoupled halves: a **QK ("query-key") circuit**, which decides how much a destination token attends to a given source token, and an **OV ("output-value") circuit**, which decides what happens to the output if that source token is attended to.
These two circuits operate on different subspaces and can be reasoned about independently by "freezing" the attention pattern computed by the first.

Applying this path expansion to a zero-layer transformer reduces it to a bigram model.
Applied to a one-layer, attention-only transformer, it reduces the model to a lookup table of "skip-trigrams" - patterns of the form "A ... B → C" - readable directly off the QK and OV weight matrices without ever running the model.
Doing this reveals, for example, that most one-layer attention heads spend most of their capacity on naive token copying: if a token was seen recently, its probability of recurring goes up.

The paper's central discovery appears once a second layer is added.
When a second-layer head's key vector is built by reading from a first-layer head that shifts information one token forward (a pattern the paper calls **K-composition**), the resulting composite head becomes an **induction head** (see [[Concept — Induction heads]]): search the context for the last time the current token appeared, and predict whatever token immediately followed it - `[a][b] ... [a] → [b]`.
Crucially, this is demonstrated to work even on sequences of uniformly random tokens the model has never seen in training, which the one-layer copying mechanism cannot do at all, since it depends only on matching token identity rather than learned statistics.
This gives, for the first time, an algorithmic explanation for the specific capability jump between one- and two-layer transformers.

## How it's built

Studies deliberately simplified "attention-only" toy transformers - no MLP layers, no biases, no layer normalization - with zero, one, or two attention layers, trained as ordinary autoregressive language models.
All the paper's central claims are derived by directly manipulating the raw trained weight matrices (via a custom library for cheaply multiplying low-rank matrices), cross-checked against attention-pattern visualizations on both natural text (the first paragraph of *Harry Potter and the Philosopher's Stone*) and synthetic sequences of uniformly random tokens, used specifically to stress-test the induction-head hypothesis in the hardest, most out-of-distribution case available.

## Results

One-layer models dedicate most attention-head capacity to copying and primitive in-context learning (10 of 12 heads in one studied model show the paper's positive-eigenvalue signature for "copying" matrices), alongside occasional "skip-trigram bugs" - a head that boosts both "keep ... in mind" and "keep ... at bay" is mathematically forced to also boost the nonsensical "keep ... in bay" and "keep ... at mind", since a single head can't represent genuinely three-way token interactions.
Two-layer models redirect nearly all of their attention-head composition into producing a small number of induction heads rather than more skip-trigram heads or the theoretically-possible "virtual attention heads" from value-composition, which the paper's own ablation experiments find contribute negligibly in the models studied.
Induction heads' predicted internal structure - a copying OV circuit paired with a same-token-matching QK circuit built from K-composition with a previous-token head - is independently confirmed using the same eigenvalue diagnostic developed for detecting one-layer copying heads.

## Why it endures

The paper is explicit that it does not extend its claims to real, MLP-containing models such as GPT-3, but a postscript added in February 2023 traces direct lines from this paper to a substantial body of follow-up work, including a sequel paper measuring induction heads' contribution to in-context learning at much larger scale, and *Toy Models of Superposition*, which takes up this paper's own admitted blind spot: MLP-layer neurons resist the same style of clean, end-to-end analysis, a phenomenon the paper speculates is connected to residual-stream "bandwidth" being scarce relative to the number of components trying to write to it.
The residual-stream-as-communication-channel framing and the QK/OV circuit decomposition introduced here became, and remain, the standard vocabulary for the rest of the mechanistic interpretability field.

## Limitations

Restricted throughout to attention-only, one- and two-layer toy models; the authors are explicit that MLP layers make up roughly two-thirds of a real transformer's parameters (and interact with attention heads, so the fraction of a real model this framework can fully explain is smaller still).
The path-expansion approach also produces enormous expanded matrices (billions of entries for a realistic vocabulary size) that are technically fully determined but not obviously human-summarizable on their own.
The authors are explicit that detecting "copying" or "matching" behavior via eigenvalue positivity is a useful working heuristic, not a rigorously justified definition.
One of the paper's own composition-strength diagrams was later found to contain a bug in an underlying linear-algebra library; a correction was appended without changing the paper's qualitative conclusions.

## Relations

- Defines [[Concept — Residual stream]] and [[Concept — Induction heads]] for the first time.
- Applies the reverse-engineering spirit of the earlier Distill Circuits thread (InceptionV1) to transformer language models for the first time.
- Its own admitted blind spot around MLP-layer interpretability directly motivates [[Paper — Toy Models of Superposition (2022)]].
- Its sequel, [[Paper — In-context Learning and Induction Heads (2022)]], directly measures induction heads' contribution to in-context learning in much larger models.

## Up

- [[Cluster — Mechanistic interpretability]]
