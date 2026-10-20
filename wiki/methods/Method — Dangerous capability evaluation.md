---
type: method
status: in-progress
updated: 2026-10-20
summary: "Testing whether a model can perform specific skills that could enable extreme harm - cyber-offense, persuasion and deception, self-proliferation, weapons assistance, self-reasoning - with the strongest elicitation available, so that results can gate training, deployment, and security decisions."
cluster: ["Capability evals"]
sources: []
tags: [evaluation, dangerous-capabilities, red-teaming, governance]
aliases: ["dangerous capability evaluations", "dangerous capability evals", "DCEs"]
---

# Method — Dangerous capability evaluation

## Definition

An evaluation of whether a model *can* do something that could enable extreme harm, as distinct from whether it *would* (an alignment evaluation) or whether it refuses harmful requests (a safety evaluation).
The category and its pairing with alignment evaluations are formalized in [[Paper — Model Evaluation for Extreme Risks (2023)]], which gives a non-exhaustive list of target capabilities: cyber-offense, deception, persuasion and manipulation, political strategy, weapons acquisition, long-horizon planning, AI development, situational awareness, and self-proliferation.

## How it works

The model is wrapped in the strongest scaffolding the evaluators can build - prompting, tools, planning frameworks, sometimes fine-tuning - because the result is meant to bound what a capable misuser or a misaligned model could get out of it.
It is then run on tasks representative of a threat model: capture-the-flag hacking challenges, persuasion studies with real people, agentic tasks such as setting up compute or acquiring resources.
Results are compared against pre-set thresholds that trigger stronger safeguards or block deployment.

Three problems shape every design:
- **Elicitation.** A failed evaluation is only a lower bound on capability; weak scaffolding, or a model deliberately underperforming (see [[Concept — Sandbagging]]), makes the bound loose.
- **Early warning.** Pass/fail says nothing until the capability exists, so evaluations need graded difficulty or a continuous measure of how close the model is.
- **Hazard.** Datasets, elicitation techniques, and results can themselves spread dangerous capability.

## Where it appears in this wiki

- [[Paper — Model Evaluation for Extreme Risks (2023)]] defines the category and how results should feed governance.
- [[Paper — Evaluating Language-Model Agents on Realistic Autonomous Tasks (2023)]] pilots it on LM agents for autonomous replication and adaptation, the earliest instance in this wiki.
- [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]] runs a full programme on Gemini 1.0 and introduces "expert bits," a continuous measure of how much help an agent needs.
- [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]] measures the general autonomous capability underlying several dangerous capabilities rather than any one of them.
- [[Benchmark — WMDP]] is a static multiple-choice proxy for hazardous knowledge, cheaper but far less realistic than agentic tasks.
- [[Paper — Sabotage Evaluations for Frontier Models (2024)]] applies the approach to a model's capability to undermine its developer's oversight.

## Relations

- Its reliance on elicitation is tested directly by [[Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024)]] and threatened by [[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]].
- A cousin of [[Method — Control evaluation]]: both elicit a model's worst-case abilities, but a control evaluation scores a safety protocol against them rather than the model alone.
- Depends on [[Method — Capability elicitation]]: a failed evaluation bounds capability only as tightly as elicitation was strong.
- [[Benchmark — SAD]] applies the approach to situational awareness, a capability that helps a model evade oversight rather than enabling misuse.
- [[Benchmark — RE-Bench]] applies it to AI R&D automation, comparing agents with human experts under equal conditions.
- [[Benchmark — SHADE-Arena]] applies it to sabotage, measuring whether agents can complete covert harmful side tasks without an LLM monitor noticing.

## Up

- [[Cluster — Capability evals]]
