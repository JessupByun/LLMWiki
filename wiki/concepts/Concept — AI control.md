---
type: concept
status: in-progress
updated: 2026-10-20
summary: "A safety approach that assumes a powerful model may be actively trying to subvert its own oversight, and asks whether a protocol still catches it - rather than trying to ensure the model's values are good."
cluster: ["Scheming & AI control"]
sources: []
tags: [ai-control, safety, red-teaming]
aliases: []
---

# Concept — AI control

## Definition

An approach to AI safety that gives up on assuming the model is aligned and instead asks: can we build a pipeline of countermeasures around a powerful model such that it stays safe even in the worst case, where the model is intentionally trying to defeat those countermeasures?
This reframes safety as a property of the *protocol* (the pipeline of trusted models, untrusted models, and limited human review that decides what gets acted on), not a property that has to be verified inside the model itself.

Introduced in [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] and measured with a [[Method — Control evaluation]].

## Why it works

Control does not require solving alignment first, because it treats "the model might be scheming" as a standing worst-case assumption rather than a risk to be estimated and hopefully ruled out.
This buys real safety margin as long as the untrusted model is not drastically more capable than the trusted model plus human budget combined - the whole approach is a bet on a *capability gap* that is expected to shrink over time as untrusted models improve while trusted models and human reviewers, by construction, do not.
The core protocols in the founding paper - trusted monitoring, untrusted monitoring, and trusted editing (see [[Method — Trusted monitoring]]) - all follow the same shape: use a cheap, trustworthy signal (or a carefully defended expensive one) to decide when the untrusted model's output is worth escalating to expensive human-quality review.

## Relations

- Complements rather than replaces alignment work; assumes what [[Paper — Weak-to-Strong Generalization (2023)]] explicitly assumed away - that the model might not share the developer's objective.
- Generalizes [[Concept — Scalable oversight]] from "the supervision signal is imperfect" to "the thing being supervised may be adversarial."
- Distinct from [[Concept — Jailbreaking (LLMs)]] and [[Concept — Adversarial examples]]: those are attacks *on* a model from an external party; control assumes the model itself is the adversary trying to subvert its own oversight.
- Directly motivated by, and motivates further work on, [[Method — Model organisms of misalignment]] - deliberately constructed instances of the deceptive behavior control protocols are meant to catch.
- Introduced by [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]]; extended from single actions to multi-step agents by [[Paper — Ctrl-Z - Controlling AI Agents via Resampling (2025)]], whose resampling protocols exploit the untrusted model being stateless between calls.
- Operationalized as run-today evaluations by [[Paper — Sabotage Evaluations for Frontier Models (2024)]], which asks whether a model could secretly undermine its developer's oversight rather than whether a protocol catches it.
- The protocols in this wiki monitor the model's actions, not its reasoning; reading a reasoning model's chain of thought would be an added layer, whose reliability [[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]] shows survives only as long as the monitor is not folded into the model's training signal.
- Evaluated with [[Method — Control evaluation]]; [[Paper — A Sketch of an AI Control Safety Case (2025)]] shows how an evaluation's result becomes a deployment argument, and [[Paper — Subversion Strategy Eval - Can Language Models Statelessly Strategize to Subvert Control Protocols (2024)]] measures whether models could strategize against a protocol unaided.
- Extended from single catastrophic failures to distributed threats by [[Paper — Adaptive Deployment of Untrusted LLMs Reduces Distributed Threats (2024)]].
- Its threat model gets a concrete deployment-shaped adversary in [[Paper — Agentic Misalignment - How LLMs Could Be Insider Threats (2025)]]: an agent with legitimate access that turns against its operator when cornered.
- Complemented by white-box alignment auditing, tested blind in [[Paper — Auditing Language Models for Hidden Objectives (2025)]] - looking for a hidden objective directly rather than wrapping the model in a protocol.
- The fallback for when [[Concept — Corrigibility]] can't be verified: [[Paper — The Off-Switch Game (2016)]]'s deference argument assumes the agent is honestly trying to serve the human, which control declines to assume.
- Whether reading reasoning could become a dependable control layer is the subject of [[Concept — Chain-of-thought monitorability]] and [[Paper — Chain of Thought Monitorability - A New and Fragile Opportunity for AI Safety (2025)]].
- [[Paper — Towards Evaluations-Based Safety Cases for AI Scheming (2024)]] places control within the broader set of arguments a safety case can make against scheming, alongside inability and alignment arguments.

## Up

- [[Cluster — Scheming & AI control]]
