---
type: cluster
status: in-progress
updated: 2026-10-19
summary: "Measuring what frontier AI systems can actually do - general agentic task-completion capability and forecasting its growth against a human-calibrated yardstick, rather than a narrow attack surface or an arbitrary benchmark score."
cluster: []
sources: []
tags: [evaluation, capability-evaluation, forecasting, benchmarks]
aliases: []
---

# Cluster — Capability evals

Named directly in CLAUDE.md's own north star as its own pillar - "evaluation/red-teaming of frontier systems" - distinct from [[Cluster — Adversarial robustness & security]]'s narrower focus on attacks, defenses, and specific hazardous-knowledge proxies like [[Benchmark — WMDP]].
Where that cluster asks "can this model be attacked, or does it already know something dangerous," this cluster asks the more basic question underneath both: how capable is this model at open-ended, autonomous tasks in the first place, and how fast is that changing?

Its founding paper, [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]], proposes the [[Concept — Task-completion time horizon]] - the length of task, measured in how long a skilled human takes, that a model completes with a given success rate - as a general-purpose yardstick that survives benchmark saturation because its unit is human time rather than any one benchmark's content.
Measured across 12 frontier models from 2019-2025, this horizon has been doubling roughly every seven months; naive extrapolation of that trend implies AI systems capable of autonomously completing month-long tasks sometime around 2028-2030, with direct implications for when autonomy-dependent dangerous capabilities - unsupervised long-horizon CBRN research, self-replication, AI-automated AI research itself - might first become technically feasible.
[[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]] supplies the counterweight: every such measurement assumes the model is trying its best, and a model or developer that sandbags (see [[Concept — Sandbagging]]) breaks that assumption.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]] | Founding paper; proposes the 50%-task-completion time horizon and measures a roughly seven-month doubling time across 12 frontier models released 2019-2025. |
| [[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]] | A direct threat to every measurement in this cluster: shows models can strategically underperform on capability evaluations - selectively, at a chosen score, or by imitating a weaker model - so a score is only as trustworthy as the evaluator's ability to elicit actual capability. Primary cluster is Scheming & AI control. |

## Methods and concepts

- [[Concept — Task-completion time horizon]]
- [[Concept — Sandbagging]] - the failure mode where the model being measured deliberately scores below its actual capability.
- [[Concept — Data contamination]] - benchmark test data leaking into training sets, the failure mode every capability measurement in this cluster has to rule out.

## Open threads

Two members now pull in opposite directions: the METR paper measures how fast capability is growing, while the sandbagging paper shows a model can make that measurement read low on purpose - so elicitation quality is this cluster's central unsolved problem, not a footnote.
Natural next additions: the task-suite papers behind METR's benchmark (HCAST, Rein et al., arxiv:2503.17354; RE-Bench, Wijk et al., arxiv:2411.15114), neither yet ingested; a dangerous-capability evaluation framework distinct from WMDP's static-knowledge proxy, such as Shevlane et al.'s "Model evaluation for extreme risks" or Phuong et al.'s "Evaluating Frontier Models for Dangerous Capabilities"; and work on eliciting hidden capability from password-locked models, the countermeasure the sandbagging paper points to.

## Up

Top-level cluster; no parent.
