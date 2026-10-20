---
type: method
status: in-progress
updated: 2026-10-19
summary: "Estimate whether a safety protocol would stop a possibly-scheming model from causing an unacceptable outcome by playing it as a game: a blue team fixes the protocol, a red team builds the most effective attacking substitute for the untrusted model it can within agreed rules, and the measured rate of successful attacks in a proxy deployment - extrapolated to full scale - upper-bounds the real risk."
cluster: ["Scheming & AI control"]
sources: []
tags: [ai-control, control-evaluation, red-teaming, evaluations]
aliases: ["control evaluations", "red-team/blue-team control evaluation"]
---

# Method — Control evaluation

## Definition

A control evaluation tests a safety protocol against the worst case rather than the expected case.
The **blue team** specifies a control protocol: monitoring, auditing, editing or resampling suspicious actions, limits on what the model can do.
The **red team** replaces the untrusted model with an attack policy designed to cause an unacceptable outcome despite that protocol, usually knowing everything about it.
The two play out a proxy deployment, and the result - how often the attack succeeds before the protocol catches it, and at what cost to usefulness - is read as an upper bound on how often a genuinely scheming model could succeed in deployment.
It deliberately sidesteps the question of whether the real model is aligned: if the protocol holds against the best attack the red team can construct, it should hold against the real model too.

## Why it works

The bound is only as good as three conditions, which [[Paper — A Sketch of an AI Control Safety Case (2025)]] states explicitly as the claims a control-based safety argument must defend.
The red team's attack must be at least as effective as anything the real model could do, which requires both enumerating strong strategies and eliciting the model's full capability to execute them - the condition [[Concept — Sandbagging]] threatens most directly, and whose feasibility [[Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024)]] tests.
The protocol in the evaluation must be no stronger than the one actually deployed: the blue team can't overfit to the test tasks or to the specific models.
And the extrapolation from a small proxy deployment to a large real one must be conservative, which in practice means simulating the full deployment from cached per-task statistics under assumptions such as constant attack probability and independent task outcomes.
Rules on both teams carry much of the weight: whether the red team must *train* its attack policy or may use hidden scaffolding and hand-written strategies, whether the blue team may train the untrusted monitor, and what each side may see.

## Where it appears in this wiki

- [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] introduces the method on single-shot coding problems, with a human-written red-team strategy and backdoored code as the unacceptable outcome.
- [[Paper — Ctrl-Z - Controlling AI Agents via Resampling (2025)]] extends it to multi-step agents in a bash environment.
- [[Paper — Adaptive Deployment of Untrusted LLMs Reduces Distributed Threats (2024)]] extends it to distributed threats, where no single action is catastrophic or conclusive, solving the protocol's prior over attack strategies as a Nash equilibrium.
- [[Paper — Sabotage Evaluations for Frontier Models (2024)]] adapts the red-team/blue-team framing into evaluations of whether current models could secretly undermine their developers.
- [[Paper — A Sketch of an AI Control Safety Case (2025)]] specifies a full five-phase evaluation - protocol specification, deployment-proxy construction, model-substitute training, proxy deployment, extrapolation - and shows how its result feeds a deployment argument.
- [[Paper — Subversion Strategy Eval - Can Language Models Statelessly Strategize to Subvert Control Protocols (2024)]] measures the one part these evaluations hand to humans: whether the model could devise the attack strategy itself, which could justify relaxing the assumption of perfect strategic ability.

## Relations

- The evaluation methodology behind [[Concept — AI control]], and the way protocols built on [[Method — Trusted monitoring]] are scored.
- Its red-team substitutes are close cousins of [[Method — Model organisms of misalignment]]: both deliberately construct a misbehaving model, but a control evaluation's substitute exists to stress-test a protocol rather than to study the misbehavior itself.
- A cousin of [[Method — Dangerous capability evaluation]]: both elicit worst-case abilities, but a control evaluation scores a protocol around the model rather than the model alone.
- Its red-team condition - an attack at least as effective as the real model's - is a [[Method — Capability elicitation]] problem.
- [[Paper — Games for AI Control - Models of Safety Evaluations of AI Deployment Protocols (2024)]] gives it a formal game-theoretic model, and [[Paper — Towards Evaluations-Based Safety Cases for AI Scheming (2024)]] places it within a full scheming safety case as the Harm Control argument.

## Up

- [[Cluster — Scheming & AI control]]
