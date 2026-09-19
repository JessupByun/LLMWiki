---
type: paper
status: in-progress
updated: 2026-09-19
summary: "Names and frames five practical accident-risk problems in modern ML systems - negative side effects, reward hacking, scalable oversight, safe exploration, and robustness to distributional shift - as concrete research problems rather than speculative superintelligence scenarios."
cluster: ["Post-training alignment", "Foundational canon"]
sources: ["raw/papers/post-training-alignment/Paper — Concrete Problems in AI Safety (2016).pdf"]
tags: [alignment, safety, reward-hacking, scalable-oversight, safe-exploration, distributional-shift, side-effects]
aliases: ["arxiv:1606.06565", "Amodei et al. 2016"]
---

# Paper — Concrete Problems in AI Safety (2016)

## Metadata

- **arXiv:** 1606.06565 (v2, 2016-07-25)
- **Authors:** Dario Amodei, Chris Olah (Google Brain), Jacob Steinhardt (Stanford), Paul Christiano (UC Berkeley), John Schulman (OpenAI), Dan Mané (Google Brain).
- A cross-institution collaboration, unusual for a 2016 safety paper.

## Impact

This paper is the reason "AI safety" stopped meaning only speculative superintelligence risk and started meaning concrete, testable properties of the ML systems already being built.
It reframes accident risk (unintended, harmful behavior from poor system design, not malice) into five practical research problems with proposed experiments, explicitly arguing this framing is more productive than invoking superintelligence.
Two of its five terms - **reward hacking** and **scalable oversight** - are now load-bearing vocabulary for the entire post-training alignment literature; see [[Concept — Reward hacking]] and [[Concept — Scalable oversight]].

## Problem it solved

By 2016, public discussion of AI risk was dominated by long-term, philosophical framings (Bostrom's superintelligence, Yudkowsky's global-catastrophic-risk essays) that the authors felt were "unnecessarily speculative" and lacked the precision to guide actual research.
Meanwhile, the ML community had accumulated scattered prior work on robustness, risk-sensitivity, and safe exploration, but nothing tied these together as a coherent research agenda aimed at *modern* systems.
The paper's stated goal is narrow and deliberate: highlight safety problems "ready for experimentation today," illustrated with a single running example (a fictional cleaning robot) rather than abstract agents.

## Core idea: three sources of accidents, five concrete problems

An accident is a situation where a designer had some (perhaps informal) objective in mind, but the deployed system produced harmful, unexpected results.
The paper categorizes *where in the pipeline* things go wrong into three sources, and pairs each with the resulting problems:

1. **Wrong objective function** (the formal objective, even perfectly optimized, isn't what was meant):
   - **Avoiding negative side effects** - an objective that only scores one narrow goal implicitly expresses indifference to everything else. The cleaning robot rewarded for moving a box may knock over a vase in its path, because nothing penalizes doing so.
   - **Avoiding reward hacking** - the objective admits a literal-but-perverse maximizer. If the robot is rewarded for "no visible mess," it can just close its eyes. See [[Concept — Reward hacking]], for which this paper supplies the definitional framing.
2. **Objective too expensive to evaluate often** (the designer *could* specify the true objective, e.g. by consulting a human, but not on every training step):
   - **Scalable oversight** - how to get good behavior from a limited budget of expensive, accurate feedback, mixed with cheap, frequent, imperfect proxies. See [[Concept — Scalable oversight]], which this paper names and frames as *semi-supervised reinforcement learning*.
3. **Undesirable behavior during learning itself** (the objective is correct, but the learning process causes harm along the way):
   - **Safe exploration** - RL agents must occasionally try actions whose consequences they don't understand; some of those actions are catastrophic and irreversible (a robot helicopter crashing, not just losing points in a video game).
   - **Robustness to distributional shift** - a model trained on distribution p0 but deployed on a different p* should both perform reasonably and *know when it's failing*, rather than confidently applying training-time heuristics to a mismatched situation.

The cleaning-robot example recurs across all five: knocking over a vase (side effects), covering messes it can't see (reward hacking), guessing which stray items are trash versus valuables from limited human check-ins (scalable oversight), sticking a wet mop in an electrical outlet while exploring cleaning strategies (safe exploration), and using factory-strength chemicals on an office floor it's never seen before (distributional shift).

## Why it endures

The taxonomy outlived every specific proposed mitigation in the paper (most of which - impact regularizers, semi-supervised RL, risk-sensitive criteria - were pre-LLM RL ideas that never became standard practice).
What endures is the vocabulary: "reward hacking" and "scalable oversight" are now used across the field essentially as this paper defined them, and later empirical work builds directly on the framing rather than the specific 2016 proposals.
[[Paper — Deep RL from Human Preferences (2017)]] produces the canonical documented instance of reward hacking the following year; [[Paper — Let's Verify Step by Step (2023)]]'s process-vs-outcome-supervision result is a scalable-oversight-flavored question in the paper's original sense (how to get reliable signal without exhaustive per-step human checking).

## Limitations

- No new algorithm or system is introduced; this is a framing and research-agenda paper. The proposed "potential experiments" for each problem are toy-scale and largely superseded by the field's actual trajectory.
- The five problems are not exhaustive by the authors' own admission, and several (safe exploration, distributional shift) had substantial pre-existing literature that the paper mostly surveys rather than originates.
- Written for RL agents specifically; its framing predates the LLM-centric post-training pipeline (RLHF at scale, RLAIF, DPO) that most of this wiki's Post-training alignment cluster now covers, so some problems (safe exploration in particular) have seen less direct LLM-era follow-up than reward hacking and scalable oversight.

## Relations

- Defines [[Concept — Reward hacking]], the term [[Paper — Deep RL from Human Preferences (2017)]] and [[Concept — Reward hacking]]'s own page already cited as "Amodei et al. 2016" before this paper was ingested.
- Names [[Concept — Scalable oversight]], the framing that motivates process supervision in [[Paper — Let's Verify Step by Step (2023)]] and, later in this reading order, weak-to-strong generalization work.
- Precedes and motivates the entire [[Cluster — Post-training alignment]] pillar: every RLHF/RLAIF pipeline in this cluster is, in this paper's vocabulary, an attempt to buy scalable oversight cheaply while managing reward hacking risk.

## Up

- [[Cluster — Post-training alignment]]
- [[Cluster — Foundational canon]]
