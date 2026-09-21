---
type: paper
status: mature
updated: 2026-09-20
summary: "Proposes treating whole activation-space representations, not individual neurons or circuits, as the right unit of analysis for interpretability, extracting a single 'reading vector' for a high-level concept from unlabeled contrastive stimuli and using it both to detect the concept in new activations and to steer generation toward or away from it by simple vector arithmetic - reaching state-of-the-art unsupervised TruthfulQA accuracy and hardening models against jailbreaks including GCG."
cluster: ["Mechanistic interpretability"]
sources: ["raw/papers/mechanistic-interpretability/Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023).pdf"]
tags: [interpretability, activation-steering, mechanistic-interpretability, safety]
aliases: ["arxiv:2310.01405", "Zou et al. 2023", "RepE", "LAT"]
---

# Paper — Representation Engineering: A Top-Down Approach to AI Transparency (2023)

## Metadata

arXiv:2310.01405 (v4, Mar 2025; originally Oct 2023).
Andy Zou, Long Phan, Sarah Chen, and a large multi-institution team including James Campbell, Phillip Guo, Richard Ren, Dan Hendrycks, Zico Kolter, and Dawn Song.
Center for AI Safety, with Carnegie Mellon University, UC Berkeley, Stanford, and EleutherAI.

## Impact

Every other paper in this wiki's mechanistic-interpretability cluster treats individual neurons, features, or attention-head circuits as the fundamental unit worth explaining.
This paper argues for a genuinely different unit: whole activation-space representations, extracted and manipulated directly, without ever decomposing them into smaller interpretable parts.
It calls this **representation engineering (RepE)**, extracts a single "reading vector" for a target concept - honesty, morality, power-seeking, emotion, harmfulness - from nothing more than contrastive prompts and unsupervised PCA, and shows the same simple vector can both detect the concept in new text and steer a model's generation toward or away from it by direct activation arithmetic.
The headline empirical result is a jump from 31-36% to over 60% accuracy on TruthfulQA using this fully unsupervised method, but the paper's own broader claim is that this single technique generalizes cleanly across an unusually wide range of safety-relevant properties with no bespoke circuit or classifier built for each one.

## Problem it solved

Bottom-up mechanistic interpretability - reverse-engineering a model into circuits of neurons and attention heads, the approach every other paper in this cluster takes - requires painstaking manual effort per behavior studied, and the authors argue it may not even be the right level of description: neural networks tolerate having individual components lesioned without breaking, and are computed via iterative refinement across a shared residual stream rather than through clean, separable circuits, evidence the paper reads as incompatible with a purely circuit-based account of how models represent concepts.
Their analogy is explicit: representation engineering is not applied mechanistic interpretability any more than biology is applied chemistry - some questions are more tractable, and some interventions more reliable, at a higher level of description than the one they're ultimately implemented in.

## Core idea

The method, **Linear Artificial Tomography (LAT)**, borrows its framing directly from neuroimaging: design a stimulus that reliably varies along the target concept, collect the model's internal activations across many such stimuli, and extract the dominant direction of variation as the concept's **reading vector**.
For a concept like honesty, this means presenting contrastive prompt pairs (an instruction to answer honestly versus one that induces dishonesty) and taking the top principal component of the *differences* between paired activations, rather than clustering raw activations directly - differencing cancels out whatever the paired stimuli share and isolates just the property that varies between them, and crucially requires no labeled dataset at all, only a contrastive stimulus design.
The resulting vector serves two distinct purposes: as a **reading** tool, projecting a new activation onto the vector gives a scalar score for how strongly the concept is present, usable for detection (is this output dishonest?) without ever generating a classifier; as a **control** tool, adding a scaled multiple of the same vector directly into the residual stream during generation pushes the model's behavior toward or away from the concept, with no gradient-based fine-tuning required for the simplest variant.

## How it's built

The paper deliberately tests this single method across a wide, heterogeneous set of concepts and functions to demonstrate generality rather than depth on any one: truthfulness and correctness on standard QA benchmarks, honesty as a controllable behavior distinct from truthfulness, utility and morality (built on the ETHICS benchmark), power-seeking (using a taxonomy of power types), risk (built compositionally from separately-extracted probability and utility vectors), the six Ekman basic emotions, harmlessness under both manual jailbreaks and GCG adversarial suffixes, demographic bias in generated text, targeted factual knowledge editing, and reducing verbatim memorized-quote regurgitation.
Reading vectors are typically extracted and applied across a broad band of middle-to-late transformer layers rather than any single layer, and three distinct control operators are compared: simple linear addition, a conditional "piece-wise" operator that only intervenes when the model's own representation already leans toward the concept (useful for avoiding over-triggering on clearly benign input), and a projection operator that removes the concept's component from a representation entirely rather than adding to it, used for ablation-style tests and bias removal.
Models are primarily LLaMA-2-Chat at 7B, 13B, and 70B scale, with Vicuna variants used for some jailbreak and lie-detection demonstrations.

## Results

Unsupervised LAT reading pushes average TruthfulQA accuracy from roughly 30-36% zero-shot (across 7B, 13B, and 70B models) to about 61%, and the underlying honesty reading vector itself reaches over 90% classification accuracy distinguishing held-out instructed-honest from instructed-dishonest outputs, with directions derived from as few as ten examples performing comparably well - the lie-detector built on top of this vector (summing negated honesty scores across many layers) is validated only qualitatively, through case-study transcripts, rather than with a reported accuracy figure.
Control results span both directions of the safety story: a fine-tuned low-rank control variant (LoRRA) improves honesty on TruthfulQA to a level the paper describes as letting a 13B model approach GPT-4-level performance on that benchmark at negligible extra inference cost, and a conditional harmlessness control raises the rate at which a model correctly refuses harmful instructions from 65% to 84% under manual jailbreak attempts and from just 16% to 83% against the GCG adversarial-suffix attack, while largely preserving compliance with benign instructions - while a separate experiment demonstrates the technique's dual-use edge just as clearly, since simply adding a "happiness" vector during generation pushes a model's compliance with a set of 500 harmful instructions from 0% all the way to 100%, despite its RLHF training to refuse.
Bias-correction and memorization-reduction results follow the same pattern of a single, cheap linear intervention producing a large, targeted shift: gender-skewed demographic mentions in a medical-vignette task converge toward parity as control strength increases, and subtracting a memorization direction cuts verbatim quote regurgitation roughly in half while leaving general factual-recall accuracy essentially untouched.

## Why it endures

The paper's own framing is explicitly aimed at deceptive alignment, the theoretical failure mode [[Concept — Deceptive instrumental alignment]] describes: its self-assessment states plainly that prior to this work, deceptive alignment had been "the most intractable specific rogue AI failure mode" precisely because it requires detecting or controlling an internal disposition (whether a model is being honest about its actual goals) rather than any externally observable behavior, and argues that a cheap, general, unsupervised method for reading and steering exactly that kind of internal property is a first empirical foothold on the problem.
Beyond that framing, RepE's demonstrated reach across truthfulness, morality, power-seeking, and harmfulness with one shared technique is its most durable methodological claim: a single contrastive-stimulus-plus-PCA recipe, cheap enough to run with no gradient training at all in its simplest form, applicable to almost any concept a researcher can phrase as a contrastive prompt pair.

## Limitations

The paper is careful to distinguish correlation from causation in its own results: a supervised logistic-regression probe can find the direction most correlated with a labeled concept while that same direction does essentially nothing when used to steer generation, direct evidence that a reading vector detecting a concept well is not sufficient proof it is the causally load-bearing direction, or that it will also work for control.
Some of the paper's own detectors show signs of tracking surface lexical association rather than the intended semantic concept (an immorality indicator firing on the literal phrase "use the gun" in an otherwise benign completion), and the authors note more generally that a model's sampled output doesn't always fully reflect what its internal representations suggest it "believes," complicating clean lie detection in the cases that matter most.
Every concept studied still requires a human to hand-design the contrastive stimulus template - the method removes the need for labeled training data, not the need for a researcher to correctly operationalize the target concept as a contrastive prompt pair in the first place - and the paper's own self-assessment leaves open whether its findings are sensitive to specific hyperparameter choices or generalize robustly beyond the settings tested.

## Relations

- Explicitly positions itself against bottom-up circuit-level interpretability - citing indirect-object-identification-style circuit work by name as the paradigm it contrasts with - rather than against the sparse-autoencoder dictionary-learning lineage specifically: it does not cite or engage with [[Paper — Toy Models of Superposition (2022)]] or [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]] anywhere, a genuine gap between these two approaches to this cluster's shared subject rather than a documented rivalry.
- Distinguishes itself from prior activation-steering work (ActAdd, Contrastive Activation Addition) by claiming a broader scope - reading and detection as well as control, plus a fine-tuned low-rank control variant - and outperforms ActAdd directly as a baseline on TruthfulQA control.
- Makes an explicit, first-of-its-kind empirical claim on [[Concept — Deceptive instrumental alignment]]: that a cheap, general activation-reading technique gives real traction on detecting and controlling a model's internal honesty, the specific property deceptive alignment concerns hinge on.
- Its harmlessness-control results are tested directly against [[Method — Greedy Coordinate Gradient (GCG)]] as an attack, and its separate emotion-steering demonstration draws its 500 harmful instructions from the same set [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] introduces, giving this cluster's newest member a direct empirical link to the adversarial-robustness cluster's central jailbreak method.

## Up

- [[Cluster — Mechanistic interpretability]]
