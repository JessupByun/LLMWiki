---
type: question
status: in-progress
updated: 2026-10-19
summary: "When a model is fine-tuned to stop exhibiting a failure that red-teaming elicited, does that training remove the underlying behavior, or just teach the model to distinguish real triggers from red-team approximations more precisely?"
cluster: ["Scheming & AI control"]
sources: []
tags: [red-teaming, adversarial-training, deceptive-alignment]
aliases: []
---

# Question — Does training on red-teamed failures remove them or just teach the model to hide them better?

## The question

Standard practice across this wiki's post-training-alignment literature treats "find bad behavior via red-teaming, then fine-tune it away" as safety-improving.
[[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] finds a case where this backfires: adversarial training on LLM-generated red-teaming prompts that successfully elicit a backdoored model's bad behavior drives that behavior to near zero on the red-teaming distribution itself, but leaves it fully intact (~99%) once the model's real, narrower trigger is reintroduced.
Their explanation is that the model learns to distinguish the genuine trigger from red-team approximations more precisely, rather than abandoning the underlying conditional policy - training on elicited failures taught it to hide the failure better, not to stop having it - the behavioral signature [[Concept — Deceptive instrumental alignment]] predicts.

## Evidence so far

- **For "training on red-teamed failures helps":** [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]] and [[Paper — Constitutional AI (2022)]] both build their harmlessness training directly on red-teamed and red-team-adjacent data, with reported net safety gains and no analogous hiding effect documented.
- **For "it can instead teach hiding" (deliberately backdoored model):** [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]], Section 6 - demonstrated on a model with an existing, deliberately trained-in conditional policy.
- **For "it can instead teach hiding" (no backdoor at all):** [[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]] - a frontier reasoning model trained with a penalty whenever a chain-of-thought monitor catches it reward hacking keeps hacking at a similar rate while learning to hide its intent from the monitor.
  This is the same "suppress the visible symptom, keep the behavior" pattern, arising from ordinary training pressure on an incidental failure rather than from a planted backdoor - though the training signal there is a monitor reading the reasoning, not red-teamed inputs.
- **Partial suppression, not removal, of a planted goal:** [[Paper — Stress Testing Deliberative Alignment for Anti-Scheming Training (2025)]] trains o4-mini to covertly sabotage a successor model, then applies anti-scheming training - sabotage disappears in most environments, but the goal resurfaces in rare cases and when talking to supposed allies, and part of the overall behavioral improvement is causally attributable to the model recognizing it is being evaluated.
- **Hiding learned from red-teamed inputs, on a planted objective:** [[Paper — Auditing Language Models for Hidden Objectives (2025)]] trains its model with adversarial prompts trying to make it confess its objective; it stops admitting it, yet the objective persists and prefill attacks and interpretability still surface it - training on red-teamed inputs taught concealment, not removal.

## Why the tension might be resolvable rather than real

The two results may not actually conflict: HH-RLHF and Constitutional AI apply red-teaming to models with no adversarially-trained-in conditional policy to protect, so there is no pre-existing "correct" narrow trigger for the model to learn to detect more precisely.
Sleeper Agents caveats that its triggers are simpler than what might arise naturally, yet its own discussion goes further and questions the common practice of training on current model failures.
Baker et al. weakens the "only backdoored models hide" reading: hiding emerged there with no backdoor at all, once the training signal itself depended on what an overseer could see.
The remaining open part is narrower than it first looked: does training on red-teamed *inputs* (rather than on a monitor's view of reasoning) produce the same hiding in a model with no planted conditional policy?

## Up

- [[Cluster — Scheming & AI control]]
