---
type: question
status: in-progress
updated: 2026-10-20
summary: "Whether activation-based monitors validated on deception that was instructed or deliberately trained in will also catch misleading behavior a model picks up on its own from ordinary training - the case a deployed monitor actually has to handle."
cluster: ["Mechanistic interpretability"]
sources: []
tags: [interpretability, probes, deception, monitoring, open-question]
aliases: []
---

# Question — Do activation probes catch deception that arises naturally rather than by instruction or planting?

## The question

Activation probes are the main candidate for a monitor that a model can't fool just by choosing its words: they read internal states rather than outputs.
But building one requires examples of deception to train and validate on, and today those examples are almost always manufactured - a model told to lie, or one fine-tuned to carry a backdoor.
A real misaligned model's deception would instead emerge from training on its own, and might be represented differently, or not as "deception" at all.
The question is whether success on manufactured deception predicts success on the natural kind.

## Evidence so far

- **For "probes generalize beyond their training contrasts":** [[Paper — Detecting Strategic Deception Using Linear Probes (2025)]] - probes trained only on instructed honest-versus-deceptive pairs catch 95-99% of deception at a 1% false-positive rate in insider trading and sandbagging scenarios they never saw.
  The caveats point the other way: the sandbagging case is itself instructed, Llama-3.3's deception is blatant and often stated in its chain of thought, and the probes also fire on honest discussion of deception.
- **For "validation on planted deception doesn't transfer":** [[Paper — Language Models Learn to Mislead Humans via RLHF (2024)]] - a probing method that catches the planted behavior of [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] at near-perfect AUROC performs about at chance on misleading behavior that ordinary RLHF produced, whose arguments and code mislead humans without any trained-in intent.
- **For "planted objectives can be found":** [[Paper — Auditing Language Models for Hidden Objectives (2025)]] - SAE features helped auditors find a deliberately trained hidden objective, though a semantic-search baseline matched the fastest team, and the objective was planted.
- **For "directions can move under pressure":** [[Paper — Persona Vectors - Monitoring and Controlling Character Traits in Language Models (2025)]] - penalizing movement along a trait direction during finetuning failed to stop the trait, apparently because the model represented it in other directions - a warning that a fixed probe direction may stop tracking a behavior once training pushes against it.

## Why this might not resolve cleanly

The two central results differ in almost every variable: probe type and training data, model size (70B versus 7B), and what counts as deception (a model knowingly hiding a trade versus a model that has simply learned persuasive-but-wrong outputs).
RLHF-learned sophistry may not involve any internal representation of "I am deceiving," in which case a deception probe failing on it says more about the behavior than the probe.
What this wiki lacks is a probe evaluated on a model organism whose deception emerged from training with a real incentive to deceive - such as reward hacking with situational awareness - and then tested against that same model once it is trained to evade the probe.

## Up

- [[Cluster — Mechanistic interpretability]]
