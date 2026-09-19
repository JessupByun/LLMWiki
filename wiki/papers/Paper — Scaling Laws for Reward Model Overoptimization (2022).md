---
type: paper
status: mature
updated: 2026-09-19
summary: "Measures reward hacking precisely for the first time, fitting scaling laws that predict exactly how far a policy can be optimized against a proxy reward model before ground-truth quality turns over."
cluster: ["Post-training alignment"]
sources: ["raw/papers/post-training-alignment/Paper — Scaling Laws for Reward Model Overoptimization (2022).pdf"]
tags: [alignment, rlhf, reward-hacking, scaling-laws, goodhart, post-training]
aliases: ["arxiv:2210.10760", "Gao et al. 2022"]
---

# Paper — Scaling Laws for Reward Model Overoptimization (2022)

## Metadata

arXiv:2210.10760, Gao, Schulman & Hilton, OpenAI, October 2022.

## Impact

Every prior demonstration of [[Concept — Reward hacking]] in this wiki was qualitative: an anecdote (the Pong agent's infinite volleys in [[Paper — Deep RL from Human Preferences (2017)]]) or a single observed curve ([[Paper — Learning to Summarize from Human Feedback (2020)]]'s over-optimization plot).
This paper is the first to measure the effect precisely enough to fit a scaling law to it, because it sidesteps the expense of human labels entirely.
The result turns "optimizing a reward model too hard eventually backfires" from a folk observation into a predictive equation: given a reward model's size, you can forecast how far you can push optimization before ground-truth quality peaks and starts falling.

## Problem it solved

Measuring overoptimization properly needs many precise data points across a wide range of optimization strengths, RM sizes, and data sizes - and each point needs a ground-truth quality score.
Getting that from real human labels at the volume needed to fit a scaling law is prohibitively expensive, so before this paper the phenomenon was documented but never carefully quantified.

## Core idea

Replace humans with a **"gold-standard" reward model** that stands in for ground truth.
A large (6B-parameter) RM from [[Paper — Training LMs to Follow Instructions (2022)]]'s InstructGPT pipeline plays the role of the human labeler: it deterministically labels synthetic comparison data, which is then used to train much smaller **proxy** reward models (3M to 3B parameters).
The policy is optimized against the proxy exactly as in real RLHF, but now the "gold" score is also cheaply computable for every checkpoint, since it's just another model's output rather than a human judgment.

This produces the paper's central plot: as the policy moves further from its starting point (measured in KL divergence `d` from the initial policy), the gold score rises with the proxy score at first, peaks, and then falls - the Goodhart curve, made precisely visible for the first time.
Concretely, for best-of-n sampling `R_bon(d) = d(α_bon − β_bon·d)`, and for RL `R_RL(d) = d(α_RL − β_RL·log d)`.
The `α` term captures the honest, expected gain in ground-truth quality from optimization; the `β` term captures how fast that gain gets eaten by overoptimization as `d` grows.

The key finding: `α` and `β` scale smoothly (log-linearly) with the proxy reward model's parameter count.
A bigger proxy RM produces a flatter, more forgiving curve - it can absorb more optimization pressure before the gold score turns over, and the eventual falloff is gentler.

## How it's built

Both optimization methods use the same InstructGPT environment: pretrained GPT-3 policies, SFT'd on human demonstrations, then optimized against a proxy RM via either PPO (standard RLHF) or best-of-n sampling (generate n completions, keep the one the proxy RM scores highest).
KL distance from the initial policy is the x-axis for both, since it is measurable analytically for best-of-n and grows monotonically during RL.

## Results

- **RL is far less KL-efficient than best-of-n.** RL "spends" much more KL to reach the same amount of optimization, so KL distance is not a fair unit for comparing optimization strength across methods - but the proxy-vs-gold relationship looks similar for both once you use proxy score itself as the x-axis instead.
- **Bigger reward models are more robust**, not just more accurate: doubling RM size measurably raises the KL distance at which the gold score peaks and lowers the eventual drop.
- **Reward model data size** matters similarly, with a sharp threshold: below roughly 2,000 comparisons, RMs perform near chance regardless of size; above it, more data steadily reduces overoptimization.
- **Policy size barely matters** for the shape of overoptimization: larger policies gain less from optimization overall (they start closer to the ceiling), but the gap between proxy and gold score at a given KL is nearly identical across policy sizes.
- **A KL penalty during RL doesn't change the underlying gold-score-vs-KL frontier** - its only effect is to make training converge to a lower KL earlier, which is functionally identical to early stopping.

## Why it endures

The paper's discussion section explicitly formalizes why **iterated, online RLHF** (the practice [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]] uses, and which [[Concept — Reward hacking]] already names as the standard mitigation) actually helps: retraining the reward model every `k` steps and optimizing distance `d/k` per round yields a final gold score of `R_RL(d) = d(α_RL − β_RL·log(d) + β_RL·log(k))` - strictly better than one long uninterrupted run of the same total distance `d`, by a term that grows with `log(k)`.
This is the first quantitative argument for a practice that, until now, this wiki's papers only justified by intuition ("humans need to be intertwined with RL learning").

The paper also organizes its own results using Manheim & Garrabrant's four-way Goodhart taxonomy (Regressional, Extremal, Causal, Adversarial), attributing the `α` term mostly to regressional Goodhart (the RM has noisy but unbiased errors) and the `β` term mostly to extremal Goodhart (the policy drifts out of the RM's training distribution, where its errors are no longer just noise).
Notably, it flags **adversarial Goodhart** - a policy actively manipulating the reward model rather than merely exploiting its blind spots - as entirely out of scope, because the models studied here aren't capable enough to do so.
That caveat is a direct forward-pointer to the model-organisms-of-misalignment and AI control literature (Group M in the backlog), which studies exactly the capability regime this paper explicitly declines to.

## Limitations

- The synthetic setup assumes a "gold" RM is a valid stand-in for actual human judgment; if the gold RM has its own systematic blind spots, the measured curve inherits them rather than measuring true human-alignment overoptimization.
- The paper explicitly separates this source of overoptimization (proxy RM diverging from its own training labels) from a second, unmodeled one: the training labels themselves diverging from actual human intent, which is the layer [[Concept — Scalable oversight]] is about.
- Findings come from a single environment (InstructGPT-style instruction following); the authors note only informal confirmation that similar curves appear in WebGPT, and ask for validation elsewhere.

## Relations

- Makes precise, at scale, the phenomenon [[Paper — Deep RL from Human Preferences (2017)]] first documented anecdotally and [[Paper — Learning to Summarize from Human Feedback (2020)]] first plotted at LLM scale.
- Gives a quantitative argument for the online-feedback mitigation that [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]] practices and [[Concept — Reward hacking]] names as the standard fix.
- Complements [[Paper — Defining and Characterizing Reward Hacking (2022)]]'s formal, qualitative definition of hackability with an empirical, quantitative measurement of how bad hacking gets and how it scales - the two papers arrived the same month from different angles (definitional theory vs. measurement).
- Explicitly excludes adversarial Goodhart (a policy that actively games the RM) as out of scope for present-day models, anticipating the model-organisms-of-misalignment and AI control research this wiki queues next.

## Up

- [[Cluster — Post-training alignment]]
