---
type: paper
status: mature
updated: 2026-09-19
summary: "Argues, via six complementary lines of indirect evidence, that induction heads are the primary mechanism behind most in-context learning in transformer language models of any size, from tiny toy models up to 13B parameters."
cluster: ["Mechanistic interpretability"]
sources: ["https://transformer-circuits.pub/2022/in-context-learning-and-induction-heads/index.html"]
tags: [interpretability, mechanistic-interpretability, in-context-learning, transformers, safety]
aliases: ["arxiv:2209.11895", "Olsson et al. 2022"]
---

# Paper — In-context Learning and Induction Heads (2022)

## Metadata

Olsson, Elhage, Nanda, Joseph, DasSarma, Henighan, Mann, Askell, Bai, Chen, Conerly, Drain, Ganguli, Hatfield-Dodds, Hernandez, Johnston, Jones, Kernion, Lovitt, Ndousse, Amodei, Brown, Clark, Kaplan, McCandlish & Olah, Anthropic, published March 8, 2022 on the Transformer Circuits Thread (transformer-circuits.pub); also cross-posted to arXiv (arxiv:2209.11895, September 2022).

## Impact

Direct sequel to [[Paper — A Mathematical Framework for Transformer Circuits (2021)]], and the paper that turns [[Concept — Induction heads]] from "an interesting circuit found in a toy model" into a candidate explanation for one of the field's biggest open questions: what actually causes in-context learning in real, large language models.
Its central, carefully-hedged claim is that induction heads may be the primary mechanism behind the majority of in-context learning in transformers of any size - a claim supported for tiny attention-only models by genuinely causal evidence, and for models as large as 13B parameters by correlational and analogical evidence the authors are explicit about not overselling.
Along the way it documents "the phase change": a sharp, universal, early-training transition where in-context learning ability and induction heads appear together, in every model with more than one layer, regardless of size.

## Problem it solved

[[Paper — A Mathematical Framework for Transformer Circuits (2021)]] could give an airtight, weights-level account of induction heads only in tiny, attention-only, one- or two-layer toy models.
It had no way to say anything about whether the same mechanism mattered in real, large language models with hundreds of layers and MLP blocks, where the clean mathematical decomposition that made toy-model reverse engineering possible breaks down.
Closing that gap required an entirely different kind of evidence: not reading circuits directly off the weights, but observing how a measurable, purely behavioral proxy for induction heads co-varies with a measurable proxy for in-context learning, across models the authors can't fully reverse-engineer.

## Core idea

Redefine induction heads by their empirical behavior alone, dropping any reference to internal wiring: on a sequence of repeated random tokens, a head counts as an induction head if it does **prefix matching** (attends back to whichever earlier token was followed by a copy of the current token) and **copying** (its output increases the logit of that attended-to token).
This behavioral test works in any model, including ones far too large or MLP-laden to reverse-engineer the way the 2021 paper did.

Applying it reveals **the phase change**: early in training - roughly the first 1 to 5 billion tokens - every model with more than one attention layer goes through an abrupt, narrow window in which four things happen simultaneously: in-context learning ability jumps from about 0.15 to about 0.4 nats and then plateaus for the rest of training; induction heads form; the training loss curve visibly "bumps" (the only point in training where loss isn't monotonically slowing its improvement); and the model's overall trajectory through per-token-loss space visibly pivots.
This holds across dozens of models spanning four orders of magnitude in size and multiple training datasets.
The single exception, in every case, is one-layer models - which cannot form induction heads at all, since the composition of two attention heads that induction heads require is structurally impossible with only one layer - and which correspondingly never show a phase change or gain in-context learning.

The paper builds its case from six complementary arguments, explicit that no single one is conclusive on its own: **macroscopic co-occurrence** (induction heads and in-context learning form in the same training window, across many models); **macroscopic co-perturbation** (a "smeared key" architecture modification, which lets even a single attention layer fake the key-shifting trick induction heads need, causes the phase change to happen in one-layer models too, and earlier in deeper ones); **direct ablation** (deleting induction heads at test time in small models destroys almost all measured in-context learning); **generality** (the exact same heads that pass the strict literal-copying test also perform far more abstract behavior - word-level translation, and a synthetic few-shot classification task - suggesting one mechanism generalizes rather than two coinciding); **mechanistic plausibility** (the known, already-reverse-engineered mechanism of small-model induction heads logically implies they should help prediction, independent of any correlational evidence); and **continuity from small to large models** (every measured curve has the same shape at every scale studied, up to 13B parameters, which is what you'd expect if the same mechanism were responsible throughout).

## How it's built

34 decoder-only transformers across four model families - small attention-only models, small models with MLPs, "full-scale" production-style models from 13M to 13B parameters, and the targeted "smeared-key" architecture variant - each with dozens of saved snapshots across training, are analyzed with over 50,000 individual attention-head ablations.
A "per-token loss vector" technique (collecting each snapshot's log-likelihood on 10,000 fixed tokens and running PCA across all snapshots of all models) lets the authors visualize a model's entire training trajectory as a path through a shared, comparable space, borrowed conceptually from treating a network's behavior as a point in a function space.

## Results

In small attention-only models, ablation shows that essentially all measured in-context learning traces back to induction heads specifically, not to any other attention head.
The smeared-key intervention causally confirms the mechanism isn't just correlated with in-context learning: making induction-head-like computation possible in a one-layer model is sufficient to produce the phase change there too, right on schedule.
In a 40-layer, 13B-parameter model, individual heads that pass the strict formal induction-head test are also shown performing rudimentary English-French-German translation and a synthetic in-context pattern-classification task, purely via the same "find where this happened before, predict what came next" mechanism as the toy-model induction heads.
The paper is unusually explicit about grading its own evidence: strong and causal for small attention-only models, medium and mostly correlational for large models with MLPs - a rare instance of a paper stating plainly where its central claim is well-supported and where it is merely plausible.

## Why it endures

The phase change is presented as a candidate "Rosetta stone" linking three subfields that had mostly talked past each other: mechanistic interpretability (circuit-level), learning dynamics, and the statistical-physics-style empirical study of scaling laws and training discontinuities (grokking, double descent).
As far as the authors are aware, it is the first case where a training-time behavioral discontinuity has been given an actual mechanistic, circuit-level explanation, rather than just documented as a curve.
It also becomes a standard reference for a specific, targeted safety claim: contemporaries worried in-context learning might be implemented by an internal [[Concept — Mesa-optimization]] (a model developing its own learned optimization process at inference time); this paper reports finding no evidence of that, and identifies an alternative, fully inspectable mechanistic candidate instead.
Two external researchers (from Redwood Research and an independent replication) contributed appended replication comments directly to the published paper, continuing the "invite outside replication into the document itself" format the original Circuits Thread established.

## Limitations

The paper's own confidence table is explicit: for large, real-world models with MLP layers, the claim that induction heads explain the *majority* of in-context learning is graded correlational or analogical, not causal - full ablation studies, the strongest evidence type available, were only computationally tractable on the small models.
It explicitly does not rule out [[Concept — Mesa-optimization]] arising in larger or future models, only that none was observed in the models studied here.
Several "unexplained curiosities" are flagged rather than resolved, including why the in-context learning score stays nearly constant across model sizes spanning three orders of magnitude after the phase change, and a handful of unusual attention heads whose behavior under ablation doesn't fit the induction-head pattern.

## Relations

- Direct sequel to [[Paper — A Mathematical Framework for Transformer Circuits (2021)]], reusing and stress-testing its QK/OV circuit vocabulary and induction-head definition at scales that vocabulary alone can't reach.
- Substantially extends [[Concept — Induction heads]] with a purely behavioral definition and causal evidence for their role in [[Concept — In-context learning]].
- Engages directly with, and reports no evidence for, the [[Concept — Mesa-optimization]]/inner-alignment concern raised in Hubinger et al.'s *Risks from Learned Optimization* (2019, not yet ingested).
- Connects the induction-head phase change to the broader phase-change/discontinuous-capability literature (Power et al.'s "grokking", 2022, not yet ingested) and to [[Paper — Scaling Laws for Neural Language Models (2020)]]'s observation that one-layer transformers don't follow the same scaling trends as deeper ones - which this paper argues is because they lack induction heads.

## Up

- [[Cluster — Mechanistic interpretability]]
