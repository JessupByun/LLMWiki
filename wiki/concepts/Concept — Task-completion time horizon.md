---
type: concept
status: in-progress
updated: 2026-09-20
summary: "A frontier AI model's capability measured as the length of task, in the time a skilled human would take, that it can complete at a given success rate - its '50% time horizon' is the task duration at which it succeeds half the time - giving one comparable number across benchmarks of wildly different content and difficulty."
cluster: ["Capability evals"]
sources: []
tags: [evaluation, capability-evaluation, forecasting]
aliases: ["time horizon", "task-completion time horizon", "50% time horizon"]
---

# Concept — Task-completion time horizon

## Definition

The X%-task-completion time horizon of an AI agent is the length of task, measured by how long a skilled human takes to complete it, at which the agent succeeds approximately X% of the time.
The 50% time horizon is the headline variant: a model with a 9-minute horizon succeeds about half the time on tasks that take a skilled human around 9 minutes, with its success rate falling off smoothly for longer tasks and rising smoothly for shorter ones.
Unlike a raw benchmark score, this number is directly comparable across tasks of completely different content and difficulty, because the unit is always the same: human time.

## Why it works

Horizon is fit per model with a logistic regression, `p_success(task) = sigma((log h - log t_task) * beta)`, where `t_task` is the geometric mean completion time of successful human baseliners and `h` is the fitted 50% time horizon - the task length at which the curve crosses 0.5.
This is deliberately modeled on Item Response Theory from psychometrics, which fits a similar logistic curve to find the difficulty level at which a test-taker has a 50% chance of a correct answer, except the difficulty rating here comes directly from measured human completion time rather than being learned from the test-takers' own performance.
Because the fit only needs each task's human-time rating and each model's pass/fail outcomes, wildly different task types - a 3-second shell-script identification, a 9-minute simulation bug fix, an 8-hour ML research engineering project - can all be folded into one continuous scale, and a doubling-time trend line can be extrapolated forward the way a raw percentage score, which saturates, cannot.
The choice of success threshold matters: an 80% time horizon (how long a task a model can complete *reliably*) is typically 4-6x shorter than the same model's 50% horizon, even though both grow at a similar rate over time - occasional success on a hard task is a much lower bar than dependable success on it.

## Relations

- Introduced and measured by [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]], which fits it across three task suites (HCAST, RE-Bench, SWAA) and finds a roughly seven-month doubling time for frontier models released 2019-2025.
- Explicitly modeled on Item Response Theory, the same psychometric framework that scores human test-takers' ability against item difficulty.
- Distinguished from a fixed capability threshold like [[Benchmark — WMDP]]'s proxy questions: WMDP asks whether a model possesses a specific hazardous knowledge domain, while time horizon asks how long a task the model can carry out autonomously in general, regardless of domain.

## Up

- [[Cluster — Capability evals]]
