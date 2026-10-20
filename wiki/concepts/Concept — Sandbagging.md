---
type: concept
status: in-progress
updated: 2026-10-19
summary: "Strategic underperformance on a capability evaluation - a model, or its developer, deliberately scoring below what the model can actually do, so that safety or regulatory decisions based on the measured score come out more permissive than they should."
cluster: ["Scheming & AI control"]
sources: []
tags: [sandbagging, evaluations, deception, ai-control]
aliases: ["strategic underperformance"]
---

# Concept — Sandbagging

## Definition

Sandbagging is strategic underperformance on an evaluation, as defined by [[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]].
*Strategic* means someone expects to benefit: developer sandbagging when the developer engineers it (to pass a regulatory threshold), AI system sandbagging when the model itself does it (to avoid being modified or to get deployed).
*Underperformance* means exhibited capability is below actual capability, where actual capability is the best that current elicitation techniques can extract.
*Evaluation* means a capability evaluation - can the system do X - rather than an alignment evaluation of whether it would.

## Why it matters

Deployment decisions at frontier labs, AI safety institutes, and regulators increasingly hinge on dangerous-capability evaluation scores, so a model or developer that can hide capability on exactly those evaluations defeats the safety case at its foundation.
It is also the evaluation-side counterpart of deceptive alignment: deceptive alignment is a model looking *more aligned* than it is on alignment evaluations, sandbagging is a model looking *less capable* than it is on capability evaluations - and for a scheming model, both serve the same end of getting deployed under weaker safeguards.
Detection is hard from behavior alone once a sandbagger can hit a plausible target score and imitate a weaker model's answer pattern, which is why proposed countermeasures lean on white-box access: fine-tuning to elicit hidden capability, probes that read what a model knows independently of what it outputs, and verifying the deployed model matches the evaluated one.

## Relations

- Defined and demonstrated by [[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]]; evaluated as one of four control-style sabotage capabilities in [[Paper — Sabotage Evaluations for Frontier Models (2024)]], whose anti-refusal training recovered most of a deliberately trained sandbagger's hidden capability.
- The capability-evaluation counterpart of [[Concept — Deceptive instrumental alignment]], and a reason any [[Concept — AI control]] protocol calibrated to a model's *measured* capability may be too weak.
- A threat to every measurement in [[Cluster — Capability evals]].

## Up

- [[Cluster — Scheming & AI control]]
