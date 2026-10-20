---
type: cluster
status: in-progress
updated: 2026-10-20
summary: "Measuring what frontier AI systems can actually do - specific dangerous capabilities that should gate deployment, and general agentic capability forecast against a human-calibrated yardstick - and whether those measurements can be trusted when a model might hide what it can do."
cluster: []
sources: []
tags: [evaluation, capability-evaluation, forecasting, benchmarks]
aliases: []
---

# Cluster — Capability evals

Measuring what frontier models can actually do, as distinct from [[Cluster — Adversarial robustness & security]]'s focus on attacks, defenses, and hazardous-knowledge proxies like [[Benchmark — WMDP]].
The cluster asks how capable a model is at open-ended, autonomous, and oversight-relevant tasks, how fast that is changing, and whether the measurements can be trusted.

Three strands sit here.

**Dangerous-capability evaluation.** [[Paper — Model Evaluation for Extreme Risks (2023)]] formalizes [[Method — Dangerous capability evaluation]]: test for specific skills that could enable extreme harm, alongside alignment evaluations of whether the model would use them, and gate training and deployment on the results.
[[Paper — Evaluating Language-Model Agents on Realistic Autonomous Tasks (2023)]] pilots one at ARC Evals (now METR) on GPT-4 and Claude agents, testing them on tasks relevant to autonomous replication and adaptation.
[[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]] runs a full programme on Gemini 1.0, adding a way to measure how close an agent is to a capability it can't yet demonstrate.
[[Benchmark — SAD]] measures situational awareness, a capability that helps a model evade oversight rather than enabling misuse, and sabotage evaluations such as [[Benchmark — SHADE-Arena]] (primary cluster Scheming & AI control) measure the ability to undermine oversight directly.
This approach answers a worry from [[Paper — Emergent Abilities of Large Language Models (2022)]]: abilities that appear suddenly with scale, so a new model can't be judged by extrapolating the last one.
[[Paper — Are Emergent Abilities of Large Language Models a Mirage (2023)]] argues much of that suddenness is an artifact of all-or-nothing scoring, a case for continuous measures.

**General autonomous capability.** [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]], the cluster's founding paper, proposes the [[Concept — Task-completion time horizon]]: the length of task, in skilled-human time, that a model completes with a given success rate.
Its unit is human time rather than any one benchmark's content, so it survives benchmark saturation; its tasks come from [[Benchmark — HCAST]], [[Benchmark — RE-Bench]], and a set of very short software actions.
Across 12 frontier models from 2019-2025 the horizon has doubled roughly every seven months.
Naive extrapolation puts month-long autonomous tasks around 2028-2030, which bears directly on when autonomy-dependent dangerous capabilities, such as AI-automated AI research, might become feasible.

**Whether measurements can be trusted.** Every such measurement assumes the model is trying its best.
[[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]] shows a model or developer can break that assumption (see [[Concept — Sandbagging]]).
[[Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024)]] measures when fine-tuning can recover a deliberately hidden capability anyway - reliably with a few good demonstrations, unreliably without (see [[Method — Capability elicitation]]).
[[Paper — Large Language Models Often Know When They Are Being Evaluated (2025)]] adds a second threat: frontier models can often tell an evaluation transcript from real use, most easily for agentic tasks, and the authors warn this could let a model sandbag or fake alignment under test (see [[Concept — Evaluation awareness]]).

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]] | Founding paper; proposes the 50%-task-completion time horizon and measures a roughly seven-month doubling time across 12 frontier models released 2019-2025. |
| [[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]] | A direct threat to every measurement in this cluster: shows models can strategically underperform on capability evaluations - selectively, at a chosen score, or by imitating a weaker model - so a score is only as trustworthy as the evaluator's ability to elicit actual capability. Primary cluster is Scheming & AI control. |
| [[Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024)]] | Tests the assumption every evaluation in this cluster rests on - that fine-tuning can elicit what a model can really do - using password-locked models with a known hidden capability; reliable with good demonstrations, unreliable without. Primary cluster is Scheming & AI control. |
| [[Paper — Model Evaluation for Extreme Risks (2023)]] | Defines the dangerous-capability side of this cluster: evaluate what a model could do (cyber-offense, manipulation, self-proliferation) and whether it would, and gate training and deployment on the results. |
| [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]] | Runs that programme on Gemini 1.0 - no strong dangerous capabilities, early warning signs across areas with persuasion the most mature - and adds expert bits, a continuous measure of how close an agent is to a task it fails alone. |
| [[Paper — Evaluating Language-Model Agents on Realistic Autonomous Tasks (2023)]] | The earliest dangerous-capability evaluation of LM agents here: ARC Evals (now METR) tests GPT-4 and Claude agents for autonomous replication and adaptation on 12 real-world tasks, and warns that a fixed agent's failure does not bound future agents. |
| [[Benchmark — HCAST]] | The human-timed task suite behind the METR time-horizon paper: 189 software tasks with 563 human baselines, built to read agent scores as "tasks of this length." |
| [[Paper — Emergent Abilities of Large Language Models (2022)]] | The source of the worry behind early-warning evaluation: abilities that appear suddenly with scale and can't be forecast from smaller models. Dual-tagged canon. |
| [[Paper — Are Emergent Abilities of Large Language Models a Mirage (2023)]] | The measurement rebuttal: over 92% of claimed BIG-Bench emergence appears under exact match or multiple-choice grade, and continuous metrics make progress smooth and forecastable - the rationale cited for expert bits, one of this cluster's continuous measures. |
| [[Benchmark — SAD]] | A capability evaluation aimed at an oversight-evading skill: 16 tasks measuring whether models know what they are and can tell evaluation from deployment. |
| [[Paper — Large Language Models Often Know When They Are Being Evaluated (2025)]] | A direct check on whether this cluster's measurements can be trusted: frontier models tell evaluation transcripts from real use at about 0.83 AUC (Gemini 2.5 Pro 0.95 on agentic ones) and can often say whether a test targets capability or alignment. Primary cluster is Scheming & AI control. |
| [[Benchmark — RE-Bench]] | Measures AI R&D automation directly against human experts: agents beat experts at 2 hours but fall behind by 8 and 32 hours; the third task source behind the time horizon. |

## Methods and concepts

- [[Concept — Task-completion time horizon]]
- [[Concept — Sandbagging]] - the failure mode where the model being measured deliberately scores below its actual capability.
- [[Concept — Data contamination]] - benchmark test data leaking into training sets, the failure mode every capability measurement in this cluster has to rule out.
- [[Method — Dangerous capability evaluation]]
- [[Method — Capability elicitation]]

## Open threads

Elicitation is this cluster's central unsolved problem.
The METR paper measures how fast capability is growing, the sandbagging paper shows a model can make a measurement read low on purpose, and the password-locking paper shows when fine-tuning can see through it.
The dangerous-capability papers stress that weak elicitation underestimates capability, so their negative results are effectively lower bounds.
[[Concept — Evaluation awareness]] is a second threat to trusting measurements: a model that can tell it is being tested, which SAD and [[Paper — Large Language Models Often Know When They Are Being Evaluated (2025)]] measure directly (the latter at about 0.83 AUC for frontier models tested in 2025), may behave differently than it would in deployment.
The two measurement approaches also have yet to meet: [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]]'s expert bits and the time horizon are both continuous early-warning measures, but no paper here relates a model's general horizon to its distance from specific dangerous tasks.
METR's work now appears in stages from overlapping authors (the later papers do not cite the earlier ones): [[Paper — Evaluating Language-Model Agents on Realistic Autonomous Tasks (2023)]]'s 12 hand-graded tasks, then [[Benchmark — HCAST]]'s human-timed tasks and [[Benchmark — RE-Bench]]'s fixed 8-hour, human-baselined environments, and the time horizon fitted on top.

## Up

Top-level cluster; no parent.
