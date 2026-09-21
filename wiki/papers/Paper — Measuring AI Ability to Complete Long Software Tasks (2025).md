---
type: paper
status: mature
updated: 2026-09-20
summary: "Proposes the 50%-task-completion time horizon - the length of task, measured by how long a skilled human takes, that an AI agent can complete with 50% success - as a single human-calibrated yardstick for general capability, and finds this horizon has been doubling roughly every seven months across 12 frontier models released between 2019 and 2025, with naive extrapolation implying AI systems capable of autonomously completing month-long tasks sometime around 2028-2030."
cluster: ["Capability evals"]
sources: ["raw/papers/Paper — Measuring AI Ability to Complete Long Software Tasks (2025).pdf"]
tags: [evaluation, capability-evaluation, forecasting, agents]
aliases: ["arxiv:2503.14499", "Kwa et al. 2025", "METR time horizon paper", "task horizon paper"]
---

# Paper — Measuring AI Ability to Complete Long Software Tasks (2025)

## Metadata

arXiv:2503.14499 (v4, this revision Jul 2026; originally submitted Mar 2025 under the shorter title "Measuring AI Ability to Complete Long Tasks", later revised to specify "Long Software Tasks").
Thomas Kwa and Ben West (equal contribution, lead authors), with a large supporting team including Joel Becker, Sami Jawhar, Megan Kinniment, Hjalmar Wijk, David Rein, Elizabeth Barnes, and Lawrence Chan.
Model Evaluation & Threat Research (METR), an independent nonprofit that evaluates frontier AI models for dangerous capabilities, often under contract to frontier labs.

## Impact

Opens [[Cluster — Capability evals]], filling a north-star pillar CLAUDE.md names directly - "evaluation/red-teaming of frontier systems" - that nothing in the wiki represented on its own terms until now; the closest existing material, [[Benchmark — WMDP]] and [[Benchmark — HarmBench]], measures a narrow safety-relevant property rather than general capability.
This paper proposes a single yardstick for that gap: the **50%-task-completion time horizon**, the length of a task - measured in how long a skilled human takes - that an AI agent can complete with 50% reliability.
Applied to 12 frontier models released between 2019 and 2025 on a combined suite of 170 tasks, it finds this horizon has been doubling roughly every seven months, letting a 3-second multiple-choice question and an 8-hour ML research engineering project sit on one continuous, real-world-anchored scale instead of being incomparable points on unrelated benchmarks.

## Problem it solved

Existing agentic benchmarks (AgentBench, MLAgentBench, GAIA, and similar) each report a raw percentage score, but percentages carry no obvious real-world meaning and aren't comparable across benchmarks of different difficulty - moving from 40% to 60% on one benchmark says nothing about how that compares to a different benchmark's 40%-to-60%, or to how a human would fare.
Individual benchmarks also saturate quickly as models improve, and many are deliberately built from tasks current models are known to struggle with, which biases the picture of "how capable are frontier models, in general" away from a fair comparison against ordinary human labor.
The paper's fix borrows directly from psychometrics: instead of rating task difficulty in an arbitrary benchmark-specific unit, rate it in the one unit that is intuitively meaningful regardless of task content - how long a skilled human takes to do it.

## Core idea

Rather than reporting what fraction of tasks a model gets right, the paper asks a different question of each model: at what task length does its success rate cross 50%?
Picture five example tasks from the paper's own suite, ranked by how long a skilled human takes: identifying which of four filenames is a shell script (3 seconds), looking up a simple fact on Wikipedia (1 minute), fixing a bug in a molecular-dynamics simulation's input files (9 minutes), writing a script that infers a JSON reformatting rule from example files (56 minutes), and speeding up a Python trading backtester with custom CUDA kernels for a 30x performance target (8 hours).
A model with a 50% time horizon of 9 minutes is one that succeeds on tasks around the bug-fix difficulty about half the time, with its success rate falling off smoothly above that length and rising smoothly below it - the horizon is the crossing point of a fitted success-probability curve, not a hard capability ceiling.
Comparing horizons across models turns "GPT-2 versus Claude 3.7 Sonnet" from an apples-to-oranges comparison of unrelated benchmark scores into a single number on a human-calibrated timescale: about 2 seconds for GPT-2, roughly 110 minutes for o3.

## How it's built

**Task suite (170 tasks total).** HCAST contributes 97 diverse tasks (1 minute to 30 hours) spanning cybersecurity, ML, software engineering, and general reasoning; RE-Bench contributes 7 difficult, open-ended ML research engineering environments each rated at 8 hours; Software Atomic Actions (SWAA), a new suite built for this paper, contributes 66 single-step tasks (1-30 seconds) specifically to give resolution below HCAST's roughly 1-minute floor, since without them there was no way to meaningfully measure GPT-2- or GPT-3-era models at all.
**Human baselining.** Over 800 timed baseline attempts (2,529 hours total) come from paid professional contractors averaging about 5 years of relevant experience, mostly from top-100 universities; a task's difficulty rating is the geometric mean completion time of successful attempts only, chosen because conditioning on success is far more predictive of model performance than including give-ups and failures (though the paper flags this choice as likely biasing longer tasks' ratings toward being harder than the raw average human would experience, since payment incentives pushed some contractors to abandon expensive tasks early rather than push through).
**Fitting the horizon.** For each task, an agent's success or failure is regressed against the log of the task's human-rated difficulty via logistic regression per model, `p_success = sigma((log h_agent - log t_task) * beta_agent)`, directly modeled on Item Response Theory but using measured human time as the difficulty rating rather than one learned from agent performance; `h_agent` is the fitted 50% time horizon reported for that model.
See [[Concept — Task-completion time horizon]] for the full mechanics.

## Results

The 50% time horizon doubles roughly every 207 days (95% CI 166-240 days, R^2=0.97) across the full 2019-2025 span; GPT-2 sits at about 2 seconds, o3 reaches roughly 110 minutes and succeeds on some tasks that take human baseliners over 4 hours.
The 2024-2025 subset alone shows a substantially faster 109-day doubling time, though the paper is explicit this could be noise given only seven frontier models released in that window.
The 80% time horizon (the length of task a model can complete *reliably* rather than just sometimes) doubles at a similar rate (204 days) but is consistently 4-6x shorter in absolute terms - a model that occasionally succeeds on a moderately hard task is a materially lower bar than one that dependably does.
Model success rate correlates negatively and strikingly cleanly with human completion time across all three task suites (R^2~=0.80 regressing success rate against log human time).
Two external-validity checks broadly hold up: replicating the method on SWE-bench Verified reproduces the same exponential trend but with a shorter ~70-day doubling time, traced to SWE-bench's annotator time-estimates systematically undershooting how long contract baseliners actually take on the easiest tasks (as much as 7.8x for weaker models like Claude 3 Opus); and scoring HCAST/RE-Bench tasks against 16 "messiness" factors (dynamic environments, unclear feedback loops, resource limits, irreversible mistakes) finds each additional messiness point predicts an 8.1-percentage-point drop in success rate, yet the *rate of improvement over time* looks similar for low- and high-messiness task subsets - no evidence yet that messier, more realistic tasks are improving more slowly, though the paper notes its messiness factors were themselves picked adversarially against current models and so may overstate the effect, and one targeted ablation (scrambling file and folder names on a single HCAST task) actually *raised* o1's success rate while roughly doubling the human baseline time (24 to 53 minutes), a reminder that messiness can help or hurt either side unpredictably on any individual task.
A qualitative read of failed transcripts finds the two dominant failure modes shift with capability: over a third of GPT-4-1106's failures are repeating an already-failed action (versus 2 of 32 for o1), while half of o1's failures are premature task abandonment - and newer models show concretely different behavior in transcripts, such as Claude 3.5 Sonnet abandoning a failing sequence of `sed` edits to rewrite a file from scratch, versus GPT-4 Turbo getting permanently stuck on a single misplaced backslash.
Naively extrapolating the trend, a 1-month (167-working-hour) time horizon - roughly the length of task an onboarding human employee could be trusted with - falls in an 80% CI spanning mid-2028 to mid-2030, or as early as 2027 if the steeper 2024-2025 rate continues.

## Why it endures

Because the metric's unit is human time rather than any specific benchmark's content, it doesn't saturate the way a percentage score does, and a doubling-time trend line extrapolates naturally into a forecast in a way a percentage plateau cannot - the paper's own headline "1-month AI by 2028-2030" extrapolation is already that demonstration in action, and has become a widely cited reference point in AI-timelines discussions independent of the underlying methodology.
The authors are candid that the exponential fit is a description of the data they have, not a law: they explicitly reject fancier functional forms (double-exponential, saturating logistic) as more prone to overfitting than informative given only 12 frontier data points, while also noting a fit that eventually flattens can't be ruled out.
They separately flag two specific, plausible accelerants to the trend rather than treating it as fixed - agentic post-training via outcome-based RL (already underway since 2024) and, at longer horizons, AI systems automating a meaningful share of AI research and development itself.

## Limitations

The task suite differs from real economically valuable labor in ways the authors name directly: every task is automatically scored (which favors clean, checkable success criteria over messy real judgment calls), none require coordinating with or against other agents, almost none are resource-constrained or punishing of a single mistake, and all use static rather than dynamic environments - a mean "messiness" score of only 3.2 out of 16 on the studied tasks, against an estimated 9-15 out of 16 for something like writing a good research paper.
A supplementary check against a small set of real, uncontaminated internal engineering issues found contract baseliners took 5-18x longer than the actual repository maintainers who originally fixed those issues, suggesting the paper's time-horizon numbers track a "skilled but low-context" notion of human labor rather than a high-context expert's, and that AI agent performance was worse than predicted using maintainer time but consistent with contractor time.
Current models are likely under-elicited relative to what's technically possible: only o1 and the original Claude 3.5 Sonnet received the roughly 2-3 engineer-weeks of scaffold tuning the paper judges adequate, and over 80% of successful agent runs cost less than a tenth of the equivalent human labor cost - headroom from techniques like best-of-k inference-time sampling that current numbers don't reflect.
The forecast is also far more sensitive to the doubling-rate estimate itself than to any constant-factor error in where models currently stand: a curve-fitting sensitivity analysis finds that halving the growth rate delays 1-month-horizon AI by about 4.8 years, while a 2x error in the current horizon estimate delays it by only about 0.6 years - and the trend is mathematically guaranteed to eventually depart from pure exponential growth, since a system capable of literally all tasks at a given reliability has, by definition, an infinite time horizon.

## Relations

- Operationalizes CLAUDE.md's own "evaluation/red-teaming of frontier systems" north-star pillar at a different level than every existing member of [[Cluster — Adversarial robustness & security]]: [[Benchmark — WMDP]] and [[Benchmark — HarmBench]] measure a specific hazardous-knowledge or jailbreak-relevant property, while this paper measures general autonomous task-completion capability, the more foundational quantity that determines how much any narrow capability can matter in practice.
- Explicitly motivates the metric by naming autonomous CBRN-weapon development and self-replication as dangerous capabilities that depend on long-horizon autonomy, and separately flags AI-automated AI R&D as a likely accelerant to its own growth trend - the same underlying worry about autonomous, hard-to-oversee AI systems that motivates [[Cluster — Scheming & AI control]]'s control protocols, though that cluster's own papers don't address capability timelines directly.
- Introduces [[Concept — Task-completion time horizon]] as this cluster's foundational metric, explicitly modeled on Item Response Theory from human psychometric testing.

## Up

- [[Cluster — Capability evals]]
