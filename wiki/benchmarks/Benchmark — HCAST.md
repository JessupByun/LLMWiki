---
type: benchmark
status: mature
updated: 2026-10-19
summary: "METR's benchmark of 189 autonomous software tasks in ML engineering, cybersecurity, software engineering, and general reasoning, each calibrated by how long skilled humans take under the same conditions as AI agents (1 minute to 8+ hours), finding early-2025 frontier agents succeed 70-80% of the time on under-an-hour tasks but under 20% on tasks over four hours."
cluster: ["Capability evals"]
sources: ["raw/papers/capability-evals/Benchmark — HCAST.pdf"]
tags: [evaluation, benchmark, agents, autonomy, human-baselines]
aliases: ["arxiv:2503.17354", "Rein et al. 2025", "Human-Calibrated Autonomy Software Tasks"]
---

# Benchmark — HCAST

Full title: **HCAST: Human-Calibrated Autonomy Software Tasks**.

## Metadata

arXiv:2503.17354 (v1, Mar 2025), preprint under review.
David Rein (corresponding), Joel Becker, Amy Deng, Seraphina Nix, and others through Lawrence Chan and Elizabeth Barnes, almost all at METR, with contributors from Equistamp and independent contributors.

## What it measures

How well autonomous AI agents complete realistic, multi-step software tasks, expressed not as a percentage but against how long the same task takes a skilled human.
The question it is built to answer: can an agent be trusted to complete a task that would take a human X hours?
Expert-knowledge benchmarks like GPQA show models answering hard questions, but don't predict whether they can act autonomously; HCAST targets that gap.

## How it's built

**Tasks.** 189 tasks in 78 families, roughly 31% ML engineering, 21% software engineering, 19% cybersecurity, and 30% general reasoning by family; 143 were built by METR staff and contractors and 46 came through a public task bounty.
Each task is a containerized environment, an instruction string, and an algorithmic scoring function, binarized at a success threshold.
Examples range from making a "Hello, World!" web server and exploiting a SQL injection, to finding sparse adversarial perturbations against a CIFAR-10 classifier and adapting an MD4 collision attack to a modified hash function.
**Quality control.** For most families, fresh testers attempt a task to find bugs and loopholes, and an agent attempts every task five times, with all 945 transcripts read by hand to confirm successes are legitimate rather than reward hacks of the scorer.
**Human baselines.** 140 people with relevant degrees or experience made 563 attempts (over 1,500 hours) in the same environment as the agents, paid hourly plus bonuses for fast success; a task's time estimate is the geometric mean of successful attempts.
110 tasks have at least one successful baseline; the other 79 use researcher forecasts, which the authors judge a reasonable but imperfect proxy for baseline times (R² = 0.51).
**Contamination.** Most tasks have private solutions, and many have private problem statements; only 11 families are released publicly, and the paper carries a canary string asking to be excluded from training data.

## Results

- Agent success falls steadily with human time, with a sharp drop between the 15-minute-to-1-hour bucket and the 1-to-4-hour bucket.
- The best agents (Claude 3.7 Sonnet and o1, run with METR's Triframe reasoning-model scaffold) succeed 70-80% of the time on tasks under an hour and under 20% on tasks over four hours; GPT-4o and the upgraded Claude 3.5 Sonnet (October 2024) trail them at every length.
- For about a third of tasks, successful agents take 5-15 actions on average; about 10% average over 25.
- Only 61% of human baseline attempts succeeded, which the authors caution is not comparable to agent success rates.

## Why it endures

It supplies most of the task suite behind [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]], which turns these human times into the [[Concept — Task-completion time horizon]], and subsets of it were used in METR's pre-deployment evaluations of frontier models including GPT-4.5, the upgraded Claude 3.5 Sonnet, and DeepSeek V3.
Its grounding in human time is what lets agent scores be read as "tasks of this length" rather than an abstract percentage.

## Limitations

The authors' own:
- Tasks have a clearly defined scope and need little context, unlike most real engineering work, which leans on prior context that is hard to describe compactly.
- Every task has an algorithmic scorer, which makes them unusually easy to evaluate compared with real work.
- All tasks are solitary software tasks, with no interaction with humans or other agents.
- Economic relevance varies: some ML and software tasks are close to real work, while CTFs resemble exercises and short reasoning tasks are toy problems.
- Baseliners lack prior context on the tasks, which likely inflates time estimates; matching baseliners to tasks by expertise was imperfect; and assigning harder tasks to stronger baseliners may bias estimates.
- Payment for speed may push baseliners to abandon tasks they struggle with, contributing to the low baseline success rate.
- Baseliners always had web access, even on tasks where agents' internet access is restricted; the authors judge this not particularly unfair, since models have memorized equivalent reference information.
- The benchmark is expensive to run and maintain, limiting fine-grained statistical comparisons, and human baselines are slow to collect, leaving many long tasks reliant on forecasts.

The agent results are a lower bound: the authors expect better prompting and scaffolding to improve them.

## Relations

- A later METR benchmark sharing several authors with [[Paper — Evaluating Language-Model Agents on Realistic Autonomous Tasks (2023)]], scaled up from its 12 hand-graded tasks with automatic scoring and human timing.
- Its tasks include cybersecurity and ML research engineering of the kind [[Method — Dangerous capability evaluation]] targets, though it measures general autonomy rather than any one threat.
- Its manual check that agent successes aren't exploits of the scoring function is a guard against [[Concept — Reward hacking]] inside the benchmark itself.

## Up

- [[Cluster — Capability evals]]
