---
type: cluster
status: in-progress
updated: 2026-09-20
summary: "Measuring what frontier AI systems can actually do - general agentic task-completion capability and forecasting its growth against a human-calibrated yardstick, rather than a narrow attack surface or an arbitrary benchmark score."
cluster: []
sources: []
tags: [evaluation, capability-evaluation, forecasting, benchmarks]
aliases: []
---

# Cluster — Capability evals

Named directly in CLAUDE.md's own north star as its own pillar - "evaluation/red-teaming of frontier systems" - distinct from [[Cluster — Adversarial robustness & security]]'s narrower focus on attacks, defenses, and specific hazardous-knowledge proxies like [[Benchmark — WMDP]].
Where that cluster asks "can this model be attacked, or does it already know something dangerous," this cluster asks the more basic question underneath both: how capable is this model at open-ended, autonomous tasks in the first place, and how fast is that changing?

Opens with a single founding paper, [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]], which proposes the [[Concept — Task-completion time horizon]] - the length of task, measured in how long a skilled human takes, that a model completes with a given success rate - as a general-purpose yardstick that survives benchmark saturation because its unit is human time rather than any one benchmark's content.
Measured across 12 frontier models from 2019-2025, this horizon has been doubling roughly every seven months; naive extrapolation of that trend implies AI systems capable of autonomously completing month-long tasks sometime around 2028-2030, with direct implications for when autonomy-dependent dangerous capabilities - unsupervised long-horizon CBRN research, self-replication, AI-automated AI research itself - might first become technically feasible.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]] | Founding paper; proposes the 50%-task-completion time horizon and measures a roughly seven-month doubling time across 12 frontier models released 2019-2025. |

## Methods and concepts

- [[Concept — Task-completion time horizon]]

## Open threads

Currently a single-paper cluster - per the "first paper in a named north-star pillar creates its cluster" rule, it doesn't need to wait for a second member the way an unnamed theme would.
Natural next additions: the underlying HCAST and RE-Bench task-suite papers this paper builds on and cites as "Forthcoming, 2025" (Rein et al., Wijk et al.), neither yet independently ingested; a dangerous-capability-specific evaluation paper distinct from WMDP's static-knowledge-proxy approach, such as Phuong et al.'s "Evaluating Frontier Models for Dangerous Capabilities," cited directly in this paper's own introduction; or a second capability-forecasting paper to give this cluster's own doubling-time methodology an independent point of comparison.

## Up

Top-level cluster; no parent.
