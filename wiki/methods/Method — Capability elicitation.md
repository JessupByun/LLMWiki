---
type: method
status: in-progress
updated: 2026-10-20
summary: "Getting a model to show the most it can actually do on a task - through prompting, scaffolding, fine-tuning on demonstrations, reinforcement learning, sampling many attempts, or bypassing refusals - so that an evaluation measures capability rather than the evaluator's effort; a failed evaluation is only a lower bound unless elicitation was strong."
cluster: ["Capability evals"]
sources: []
tags: [elicitation, evaluations, fine-tuning, scaffolding, sandbagging]
aliases: ["elicitation", "capability elicitation", "surfacing latent capabilities"]
---

# Method — Capability elicitation

## Definition

Capability elicitation is the work of drawing out a model's full ability on a task before measuring it.
An evaluation of a weakly prompted model measures the prompt as much as the model; safety decisions need something closer to the ceiling a capable misuser, a later fine-tune, or the model itself could reach.

## How it works

Techniques run from cheap to strong:
- **Prompting and scaffolding:** better instructions, tools, planning loops, and agent frameworks around the model.
- **Sampling:** many attempts per task, or best-of-N selection over candidate actions.
- **Refusal bypassing:** editing or overriding refusals so the evaluation measures ability rather than willingness.
- **Fine-tuning:** supervised training on good demonstrations, or reinforcement learning when only success can be judged.

The core difficulty is that elicitation can only be validated against a known answer.
If the evaluator doesn't know what the model can do, a weak result is ambiguous between "can't" and "wasn't elicited" - and a model deliberately hiding capability ([[Concept — Sandbagging]]) makes the ambiguity adversarial.

## Where it appears in this wiki

- [[Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024)]] tests elicitation against models with a known hidden capability: a few good demonstrations usually unlock it, but it becomes unreliable without them.
- [[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]] shows models can be prompted or fine-tuned to hide capability, the case elicitation has to defeat.
- [[Paper — Sabotage Evaluations for Frontier Models (2024)]] recovers most of a deliberately sandbagging model's hidden capability with anti-refusal safety training.
- [[Paper — A Sketch of an AI Control Safety Case (2025)]] says justifying adequate red-team elicitation is currently an open question for a control-based safety argument.
- [[Paper — Evaluating Language-Model Agents on Realistic Autonomous Tasks (2023)]] overrides refusals and warns that better scaffolding and fine-tuning could change its results; [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]] has an expert best-of-N measurement showing models sometimes generate good actions but rank them low.
- [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]] says it put limited effort into elicitation, so its results are a lower bound and some models may be more capable.
- [[Paper — Model Evaluation for Extreme Risks (2023)]] lists surfacing latent capabilities through prompting or fine-tuning as a requirement of extreme-risk evaluations.

## Relations

- The step that every [[Method — Dangerous capability evaluation]] and [[Method — Control evaluation]] depends on, and the reason their negative results are lower bounds.
- Its adversarial failure mode is [[Concept — Sandbagging]], often enabled by [[Concept — Evaluation awareness]].
- [[Paper — Towards Evaluations-Based Safety Cases for AI Scheming (2024)]] lists sufficient elicitation, including ruling out sandbagging, among the assumptions every inability argument needs, and [[Benchmark — SHADE-Arena]] notes its elicitation of saboteurs and monitors is far from exhaustive.
- [[Paper — When Can We Trust Untrusted Monitoring - A Safety Case Sketch Across Collusion Strategies (2026)]] uses supervised fine-tuning to upper-bound a monitor's self-recognition ability, so a scheming model can't sandbag the collusion evaluation.

## Up

- [[Cluster — Capability evals]]
