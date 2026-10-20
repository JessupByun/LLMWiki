---
type: paper
status: mature
updated: 2026-10-20
summary: "Finds that refusal in 13 open-source chat models (1.8B-72B) is controlled by a single direction in activation space: erasing it makes models comply with harmful requests, adding it makes them refuse harmless ones, and baking the erasure into the weights gives a cheap white-box jailbreak with small capability cost on most models - evidence of how shallow and fragile current safety fine-tuning is."
cluster: ["Mechanistic interpretability", "Adversarial robustness & security"]
sources: ["raw/papers/mechanistic-interpretability/Paper — Refusal in Language Models Is Mediated by a Single Direction (2024).pdf"]
tags: [interpretability, refusal, activation-steering, jailbreaking, safety-fine-tuning, open-weights]
aliases: ["arxiv:2406.11717", "Arditi et al. 2024", "refusal direction", "weight orthogonalization"]
---

# Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)

## Metadata

arXiv:2406.11717 (v3, Oct 2024; originally Jun 2024); NeurIPS 2024.
Andy Arditi and Oscar Obeso (equal contribution), with Aaquib Syed, Daniel Paleka, Nina Panickssery, Wes Gurnee, and Neel Nanda.
Affiliations listed: independent, ETH Zürich, University of Maryland, Anthropic, and MIT; the work began in the SPAR and MATS programs.

## Impact

Safety fine-tuning teaches chat models to refuse harmful requests, and it's natural to imagine that ability spread across many parts of the network.
This paper shows that in every open model it tested, refusal hinges on one direction in the model's internal activations - a single on/off switch.
Remove it and the model complies with almost anything; add it and the model refuses to explain the health benefits of yoga.
Because the removal can be written directly into the weights for, by the authors' estimate, under $5 of compute on a 70B model, it is a practical white-box jailbreak for the open-weight chat models tested, and a vivid demonstration that interpretability findings translate directly into practical attacks.

## Problem it solved

Refusal was widespread but poorly understood mechanistically.
Earlier work found harmfulness directions or safety-relevant neurons but argued harmfulness was distinct from refusal, or found steering only worked in narrow settings.
Removing safety from open-weight models usually meant fine-tuning on harmful examples, which needs a dataset of harmful completions and gradient-based training.

## Core idea

Run the model on 128 harmful instructions and 128 harmless ones, and record its internal activations just after each instruction, where it is deciding how to respond.
Subtract the average harmless activation from the average harmful one: the difference points in the direction the model moves when it is about to refuse.
Test every layer and position, and keep the single direction that works best.

Two interventions then show it is the switch.
**Directional ablation** projects that direction out of every activation at every layer, so the model can never represent it - and it stops refusing.
**Activation addition** adds the direction at one layer to a harmless prompt - and the model refuses.
Because ablation is a fixed linear projection, it can be folded into the weights by making every matrix that writes to the residual stream orthogonal to the direction, a rank-one edit that needs no gradient optimization and no harmful examples.

## How it's built

Thirteen chat models: Qwen (1.8B-72B), Yi (6B, 34B), Gemma (2B, 7B), Llama-2 (7B-70B), and Llama-3 (8B, 70B), covering both preference-optimized and fine-tuned alignment.
Harmful instructions come from AdvBench, MaliciousInstruct, TDC2023, and HarmBench; harmless ones from Alpaca.
Refusal is scored by matching phrases like "I'm sorry," and harmfulness by Llama Guard 2; jailbreak strength is compared on the HarmBench test set, and capabilities on MMLU, ARC, GSM8K, and TruthfulQA.
A case study on Qwen 1.8B examines how an adversarial suffix interacts with the direction.

## Results

- Ablating the direction sharply reduces refusal and produces unsafe completions on 100 JailbreakBench prompts across all 13 models; adding it makes models refuse harmless Alpaca prompts.
- As a jailbreak, weight orthogonalization fares well against other general jailbreaks on HarmBench (best on Qwen and Llama-2 7B, behind a transfer GCG attack on Llama-2 13B and 70B with the system prompt), and on Qwen 7B and 14B matches prompt-specific GCG (79.2% vs 79.5% on Qwen 7B).
- Llama-2 is sensitive to its system prompt: on the 7B model, 22.6% success with the default safety-oriented system prompt versus 79.9% without (13B and 70B: 6.9% and 4.4% versus 61.0% and 62.9%) - and dropping the system prompt is an option open to anyone with the weights; the authors find the gap isn't explained by prompt content alone.
- Capabilities barely move: MMLU, ARC, and GSM8K stay within 99% confidence intervals of the original for all but two models, though TruthfulQA drops for every model, by 1.0-5.9 points, perhaps because it touches refusal-adjacent topics like misinformation and conspiracies.
- An adversarial suffix suppresses the refusal direction to the level of a harmless prompt, by hijacking the attention of the heads that write it - they shift attention from the harmful instruction to the suffix.

## Why it endures

It is one of the clearest cases of interpretability producing an immediately useful result - in this case an attack.
It made "safety fine-tuning is shallow" concrete: the refusal behavior is a thin, linearly separable layer on top of capabilities that remain intact.
That has two implications this wiki keeps returning to: open-weight safeguards can be removed cheaply, and defenses that only teach a model to refuse leave the dangerous capability in place.
It is one of the most prominent applications of difference-in-means directions, a technique the paper credits to Belrose, Marks and Tegmark, and contrastive activation addition; [[Paper — Persona Vectors - Monitoring and Controlling Character Traits in Language Models (2025)]] applies the same technique to character traits (see [[Method — Activation steering]]).

## Limitations

The authors' own:
- Findings may not generalize to untested models, especially larger or proprietary ones.
- The extraction method is heuristic and likely not optimal; the paper is an existence proof, not a study of how best to find the direction.
- The adversarial-suffix analysis covers one model and one suffix.
- Coherence is hard to measure, and every metric used is flawed in some way.
- What the direction means is unclear: "refusal direction" is a functional label, and it might represent harm or danger instead.

They judge that the method only marginally lowers the bar for jailbreaking open weights, which fine-tuning could already do, and does not substantially change the risk of open-sourcing models.

## Tension / update

[[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]] found Vicuna's internal harmfulness representation stayed intact under adversarial suffixes and jailbreaks, concluding that something other than perceived harmfulness explains why models comply.
This paper finds the opposite for its refusal direction: an adversarial suffix suppresses it to harmless-prompt levels in Qwen 1.8B.
The authors flag this contrast themselves.
The metrics differ too: RepE measured harmful/harmless classification accuracy on Vicuna-13B against a universal suffix, while this paper measured how strongly the direction is expressed under a GCG suffix optimized for Qwen 1.8B.
The two may also be measuring different things - a representation of harmfulness versus the decision to refuse - and each result rests on one model, so the disagreement is about what a jailbreak disrupts, not yet settled.

## Relations

- Uses contrastive harmful/harmless pairs as in [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]] and contrastive activation addition, with difference-in-means extraction and a direct weight edit; see [[Method — Activation steering]].
- A white-box counterpart to the prompt-based attacks of [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] (see [[Method — Greedy Coordinate Gradient (GCG)]] and [[Concept — Jailbreaking (LLMs)]]), and offers preliminary evidence (one suffix, on Qwen 1.8B) of how such suffixes work.
- Evaluates with [[Benchmark — HarmBench]] and its classifier.
- In the wiki's own reading, mechanistic evidence for the shallowness of refusal training, the weakness [[Paper — Constitutional Classifiers - Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming (2025)]] sidesteps for deployed models by adding separate classifiers (which don't help against weight edits on open models).
- Its first author co-wrote [[Paper — Persona Vectors - Monitoring and Controlling Character Traits in Language Models (2025)]], which extends the recipe from refusal to character traits.
- [[Paper — Improving Alignment and Robustness with Circuit Breakers (2024)]] defends against exactly the weakness this paper exposes, making harmful representations unusable even after refusal is bypassed, and includes a related representation-steering attack in its evaluation.

## Up

- [[Cluster — Mechanistic interpretability]]
- [[Cluster — Adversarial robustness & security]]
