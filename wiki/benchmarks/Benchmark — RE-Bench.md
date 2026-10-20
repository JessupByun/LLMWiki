---
type: benchmark
status: mature
updated: 2026-10-20
summary: "Seven open-ended ML research-engineering environments - optimizing a GPU kernel, predicting a scaling law, fixing corrupted embeddings, and others - with 71 eight-hour attempts by 61 human experts as a baseline, finding frontier AI agents score 4x higher than experts given 2 hours each, but experts improve faster with time, edging ahead at 8 hours and doubling the best agent's score at 32."
cluster: ["Capability evals"]
sources: ["raw/papers/capability-evals/Benchmark — RE-Bench.pdf"]
tags: [evaluation, benchmark, ai-rnd, agents, human-baselines, autonomy]
aliases: ["arxiv:2411.15114", "Wijk et al. 2024", "Research Engineering Benchmark"]
---

# Benchmark — RE-Bench

Full title: **RE-Bench: Evaluating frontier AI R&D capabilities of language model agents against human experts**.

## Metadata

arXiv:2411.15114 (v2, May 2025; originally Nov 2024).
Hjalmar Wijk, Tao Lin, Joel Becker, Sami Jawhar, Neev Parikh, Thomas Broadley, Lawrence Chan, Michael Chen, Josh Clymer, Jai Dhyani, Elena Ericheva, Katharyn Garcia, Brian Goodrich, Nikola Jurkovic, Holden Karnofsky, Megan Kinniment, Aron Lajko, Seraphina Nix, Lucas Sato, William Saunders, Maksym Taran, Ben West, and Elizabeth Barnes.
METR, with some authors elsewhere.

## What it measures

Whether AI agents can do the work of expert AI researchers - specifically, frontier AI research and development, where automation could set off a feedback loop of accelerating progress.
Rather than a percentage score, it compares agents directly with human ML experts given the same environment, compute, and time.
The logic is one-directional: if agents do clearly worse than experts at the same cost, they can't be automating those experts' work; doing better here does not show they can, since the environments cover only a small slice of real R&D.

## How it's built

Seven hand-crafted environments, each an ML optimization problem with a scoring function the agent can run, a simple starting solution, and a hidden strong reference solution:
- **Optimize a Kernel:** write a fast custom GPU kernel for a prefix-sum computation.
- **Optimize LLM Foundry:** speed up a fine-tuning script while keeping its behavior.
- **Scaling Law Experiment:** predict the best tradeoff between hidden size and training steps at 5e17 FLOPs using only runs under 1e16 FLOPs, without seeing the score.
- **Restricted Architecture MLM:** build a masked language model from a limited set of PyTorch primitives, without division or exponentiation.
- **Finetune GPT-2 for QA:** turn GPT-2 into a chatbot judged by another model.
- **Scaffolding for Rust Codecontests:** prompt and scaffold GPT-3.5 to solve competitive programming problems in Rust.
- **Fix Embedding:** recover a model whose embeddings have been permuted.

Scores are normalized so the starting solution is 0 and the reference solution 1.
Each environment runs on at most 6 H100 GPUs within 8 hours; over 12 specifications and 5 implemented environments were dropped during development.
71 eight-hour attempts by 61 experts - from METR staff networks, METR job applicants, and ML PhD students at top programs - form the baseline.
Agents (Claude 3.5 Sonnet in two versions and o1-preview, in METR's Modular scaffold and the AIDE scaffold) are scored by **score@k**, the best result across k independent runs, at different splits of a total time budget.

## Results

- 82% of expert attempts scored above zero and 24% matched or beat the reference solution, showing the environments are feasible; experts from METR's networks averaged about 0.96-0.98 normalized score versus 0.46-0.48 for job applicants (the paper's prose and table differ slightly).
- With a 2-hour total budget, the best agents score 4x higher than experts; at 8 hours experts narrowly pull ahead, and at 32 hours they score 2x the best agent.
- Agents progress quickly at first, then stall; experts start slowly but keep improving.
- Agents try solutions over 10x faster than humans (scoring 25-37 times per hour versus 3.4), which finds strong local tweaks, and occasionally produce genuinely creative solutions: o1-preview wrote a faster custom Triton kernel than any of the 9 human experts.
- Agents fall short through little variety in approaches, misreading instructions, and weak long-horizon agency - not updating on contradictory information or recovering from failures - and environments with more engineering complexity favor humans.
- An 8-hour agent run cost about $123 in tokens versus about $1,855 paid to a human expert, suggesting agent designs that spend more tokens could substantially improve agent scores.

## Why it endures

It is the third task source behind [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]], which rates each of its environments at 8 hours, and the most direct measurement in this wiki of AI R&D automation, a capability frontier-lab safety frameworks single out.
Its time-budget comparison makes concrete a pattern seen across agentic evaluations: agents are already competitive on short tasks, while humans retain the advantage as tasks lengthen.

## Limitations

The authors' own:
- The environments are designed to be clear, feasible, and buildable, which makes them less representative of real research, with its unclear goals, slow feedback, and impossible problems; real AI R&D is at least two orders of magnitude larger along many dimensions.
- Results are noisy, since most agent runs score zero and a few score very high.
- The evaluation is expensive, requiring many hours on H100 GPUs.
- Scaffolds were not iterated much; better tools or more parallel exploration could raise agent scores.

They also argue the results could cut either way: RE-Bench may overestimate real automation ability, since the gap grows with time horizon and complexity, or underestimate it, since agents are far cheaper and might automate R&D through different workflows.

## Relations

- One of three task sources, with [[Benchmark — HCAST]], behind [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]] and its [[Concept — Task-completion time horizon]].
- A [[Method — Dangerous capability evaluation]] for AI R&D automation, a risk category [[Paper — Model Evaluation for Extreme Risks (2023)]] lists under AI development.
- Its observation that agents misread instructions into loopholes that score well but break the rules is a small instance of [[Concept — Reward hacking]] against an automated scorer.
- Lists sabotage of control and oversight methods by misaligned R&D agents among the risks motivating it, citing [[Paper — Sabotage Evaluations for Frontier Models (2024)]] and linking it to [[Concept — AI control]].

## Up

- [[Cluster — Capability evals]]
