---
type: concept
status: in-progress
updated: 2026-10-20
summary: "A model's knowledge of what it is, of how it is trained, evaluated, and deployed, and of which of those situations it is currently in - knowledge that is self-locating, so the model applies it to its own behavior; a prerequisite for scheming and for gaming evaluations."
cluster: ["Scheming & AI control"]
sources: []
tags: [situational-awareness, scheming, evaluations, self-knowledge]
aliases: ["situationally aware", "self-locating knowledge"]
---

# Concept — Situational awareness

## Definition

A model is situationally aware to the extent that it knows which entity it is and facts about itself, can infer facts about its current situation, and acts on that knowledge.
[[Paper — Taken out of Context - On Measuring Situational Awareness in LLMs (2023)]] stresses that the knowledge must be *self-locating*: a model may know in the abstract how models like it are trained and tested, but it is only situationally aware if it recognizes that those facts apply to itself.
[[Benchmark — SAD]] adopts a definition in this spirit, with three parts: self-knowledge, inferences about the situation, and actions based on them.

## Why it matters

It is what lets a model do things on purpose that would otherwise only happen by accident.
Most importantly for safety, it enables [[Concept — Evaluation awareness]] - recognizing a test and behaving differently in it - which undermines both capability and alignment evaluations.
It is also listed as a prerequisite for scheming by [[Paper — Scheming AIs - Will AIs fake alignment during training in order to get power? (2023)]] and [[Paper — Towards Evaluations-Based Safety Cases for AI Scheming (2024)]], and among the dangerous capabilities [[Paper — Model Evaluation for Extreme Risks (2023)]] says should be evaluated.
At the same time it is useful: an agent needs accurate self-knowledge to plan around its own capabilities and limits, which is why its measurement is not simply something to minimize.

## Where it comes from

Three sources appear in this wiki.
Fine-tuning and system prompts tell chat models directly what they are, which plausibly explains why chat models outscore their base models on [[Benchmark — SAD]].
Pretraining data describes models, their training, and their tests, and **out-of-context reasoning** - acting on facts learned in training that the prompt never mentions - lets a model use those descriptions; [[Paper — Taken out of Context - On Measuring Situational Awareness in LLMs (2023)]] finds this ability already present and growing with scale.
And models can infer things about themselves from their own training: [[Paper — Tell Me About Yourself - LLMs Are Aware of Their Learned Behaviors (2025)]] shows models describing behaviors they learned only implicitly.

## Evidence in this wiki

- [[Benchmark — SAD]] measures it directly across 16 models: all above chance, all well below an upper baseline, with telling evaluation from deployment among the hardest parts.
- [[Paper — Frontier Models are Capable of In-context Scheming (2024)]] supplies the situational information in the prompt rather than relying on the model to know it.
- [[Paper — Stress Testing Deliberative Alignment for Anti-Scheming Training (2025)]] finds verbalized evaluation awareness rising with capabilities training and causally affecting behavior.
- [[Paper — Subversion Strategy Eval - Can Language Models Statelessly Strategize to Subvert Control Protocols (2024)]] finds strategic reasoning hard to separate from situational awareness in practice.

## Relations

- Its evaluation-specific component is [[Concept — Evaluation awareness]], a precondition for [[Concept — Sandbagging]] and [[Concept — Alignment faking]].
- A core ingredient of [[Concept — Deceptive instrumental alignment]], which requires a model to know when it is being trained.
- Measured as a capability in the sense of [[Method — Dangerous capability evaluation]].

## Up

- [[Cluster — Scheming & AI control]]
