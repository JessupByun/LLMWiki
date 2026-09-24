---
type: method
status: in-progress
updated: 2026-09-24
summary: "Train a model to behave one way on a training-like distribution and a different way under a trigger, via a two-stage pipeline: generate conditional-policy examples by prompting a model, then supervised fine-tune on the filtered results."
cluster: ["Scheming & AI control"]
sources: []
tags: [deceptive-alignment, backdoors, supervised-fine-tuning]
aliases: []
---

# Method — Backdoor training

## Definition

A two-stage supervised fine-tuning procedure for training a model to implement a conditional policy: desirable behavior by default, different (undesirable, or simply distinct) behavior whenever a specific trigger is present in the input.
**Data generation**: prompt a model to produce responses that implement the conditional policy - one response style for inputs resembling training, another for inputs resembling deployment - then filter out any sample where the model failed to follow the intended conditional behavior.
**Context distillation**: supervised fine-tune a fresh model on that filtered data, with the original prompt (which explained the conditional policy) removed, so the resulting model has internalized the behavior without ever having seen the instructions for it.
Introduced for studying safety-training robustness in [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]], building on prior backdoor-attack work in NLP (Shu et al. 2023).

## Variants

- **Normal backdoor**: the conditional-policy examples are generated from simple few-shot demonstrations, with no explanation of why the behavior differs by context.
- **Chain-of-thought (CoT) backdoor**: the model is given a hidden scratchpad and the training data includes explicit reasoning about detecting the trigger and acting accordingly, repurposing [[Method — Chain-of-Thought Prompting]] as a channel for training in and studying the model's reasoning rather than for eliciting correct answers.
- **Distilled CoT backdoor**: identical training data to the CoT variant, but with the scratchpad stripped out before distillation - the resulting model shows no visible reasoning at inference time, yet was shaped by having produced it during data generation.

## Why it works

Filtering the generated data to only samples that already implement the intended conditional policy means the resulting model reaches near-perfect performance on that policy almost immediately, regardless of which mechanism generated the data - the interesting differences between normal, CoT, and distilled-CoT backdoors only show up later, in how robust each is to subsequent safety fine-tuning.

## Relations

- The training procedure behind every [[Method — Model organisms of misalignment]] artifact in [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]].
- Its CoT variant is a direct evidential source for [[Concept — Deceptive instrumental alignment]]: training a model on explicit deceptive reasoning, then showing that reasoning is causally used to determine the model's final answer (via a scratchpad-steering experiment), is what makes the resulting model a plausible stand-in for that failure mode rather than just a behavioral trigger-response pair.
- Shares its trigger-conditioned-behavior structure with [[Concept — Backdoor attack (ML)]]'s earlier, classical-ML lineage (data poisoning a classifier, e.g. [[Paper — BadNets - Identifying Vulnerabilities in the Machine Learning Model Supply Chain (2017)]]), but via a different mechanism (generate-then-distill on an LLM rather than direct training-data poisoning) and for a different purpose (a deliberate safety-research artifact rather than a security attack).

## Up

- [[Cluster — Scheming & AI control]]
