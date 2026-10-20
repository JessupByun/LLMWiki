---
type: cluster
status: in-progress
updated: 2026-10-19
summary: "Measuring what frontier AI systems can actually do - specific dangerous capabilities that should gate deployment, and general agentic capability forecast against a human-calibrated yardstick - and whether those measurements can be trusted when a model might hide what it can do."
cluster: []
sources: []
tags: [evaluation, capability-evaluation, forecasting, benchmarks]
aliases: []
---

# Cluster — Capability evals

Named directly in CLAUDE.md's own north star as its own pillar - "evaluation/red-teaming of frontier systems" - distinct from [[Cluster — Adversarial robustness & security]]'s narrower focus on attacks, defenses, and specific hazardous-knowledge proxies like [[Benchmark — WMDP]].
Where that cluster asks "can this model be attacked, or does it already know something dangerous," this cluster asks the more basic question underneath both: how capable is this model at open-ended, autonomous tasks in the first place, and how fast is that changing?

Two complementary approaches sit here.
[[Paper — Model Evaluation for Extreme Risks (2023)]] formalizes [[Method — Dangerous capability evaluation]] - test for specific skills that could enable extreme harm, alongside alignment evaluations of whether the model would use them, and gate training and deployment on the results - and [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]] runs such a programme on Gemini 1.0, adding a way to measure how close an agent is to a capability it can't yet demonstrate.
The second approach measures general capability instead: [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]], the cluster's founding paper, proposes the [[Concept — Task-completion time horizon]] - the length of task, measured in how long a skilled human takes, that a model completes with a given success rate - as a general-purpose yardstick that survives benchmark saturation because its unit is human time rather than any one benchmark's content.
Measured across 12 frontier models from 2019-2025, this horizon has been doubling roughly every seven months; naive extrapolation of that trend implies AI systems capable of autonomously completing month-long tasks sometime around 2028-2030, with direct implications for when autonomy-dependent dangerous capabilities - unsupervised long-horizon CBRN research, self-replication, AI-automated AI research itself - might first become technically feasible.
[[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]] supplies the counterweight: every such measurement assumes the model is trying its best, and a model or developer that sandbags (see [[Concept — Sandbagging]]) breaks that assumption, and [[Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024)]] measures when fine-tuning can recover a deliberately hidden capability anyway - reliably with a few good demonstrations, unreliably without.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]] | Founding paper; proposes the 50%-task-completion time horizon and measures a roughly seven-month doubling time across 12 frontier models released 2019-2025. |
| [[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]] | A direct threat to every measurement in this cluster: shows models can strategically underperform on capability evaluations - selectively, at a chosen score, or by imitating a weaker model - so a score is only as trustworthy as the evaluator's ability to elicit actual capability. Primary cluster is Scheming & AI control. |
| [[Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024)]] | Tests the assumption every evaluation in this cluster rests on - that fine-tuning can elicit what a model can really do - using password-locked models with a known hidden capability; reliable with good demonstrations, unreliable without. Primary cluster is Scheming & AI control. |
| [[Paper — Model Evaluation for Extreme Risks (2023)]] | Defines the dangerous-capability side of this cluster: evaluate what a model could do (cyber-offense, manipulation, self-proliferation) and whether it would, and gate training and deployment on the results. |
| [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]] | Runs that programme on Gemini 1.0 - no strong dangerous capabilities, early warning signs across areas with persuasion the most mature - and adds expert bits, a continuous measure of how close an agent is to a task it fails alone. |

## Methods and concepts

- [[Concept — Task-completion time horizon]]
- [[Concept — Sandbagging]] - the failure mode where the model being measured deliberately scores below its actual capability.
- [[Concept — Data contamination]] - benchmark test data leaking into training sets, the failure mode every capability measurement in this cluster has to rule out.
- [[Method — Dangerous capability evaluation]]

## Open threads

Elicitation is this cluster's central unsolved problem: the METR paper measures how fast capability is growing, the sandbagging paper shows a model can make a measurement read low on purpose, the password-locking paper shows when fine-tuning can see through it, and the dangerous-capability papers stress that weak elicitation underestimates capability, so their negative results are effectively lower bounds.
The two measurement approaches also have yet to meet: [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]]'s expert bits and the time horizon are both continuous early-warning measures, but no paper here relates a model's general horizon to its distance from specific dangerous tasks.
Natural next additions: the task-suite papers behind METR's benchmark (HCAST, Rein et al., arxiv:2503.17354; RE-Bench, Wijk et al., arxiv:2411.15114) and ARC Evals' autonomous-replication evaluations (Kinniment et al., arxiv:2312.11671), which both METR and Phuong et al. build on.

## Up

Top-level cluster; no parent.
