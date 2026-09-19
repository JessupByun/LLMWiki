---
type: paper
status: in-progress
updated: 2026-09-19
summary: "Gives the first formal, mathematical definition of reward hacking - a proxy reward is unhackable if improving it can never decrease the true reward - and proves that this is a very demanding condition: essentially unachievable over the full policy space, but always achievable once the policy set is restricted to something finite."
cluster: ["Post-training alignment"]
sources: ["raw/papers/post-training-alignment/Paper — Defining and Characterizing Reward Hacking (2022).pdf"]
tags: [reward-hacking, alignment-theory, rl-theory, proxy-rewards]
aliases: ["arxiv:2209.13085", "Skalse et al. 2022"]
---

# Paper — Defining and Characterizing Reward Hacking (2022)

## Metadata

- **arXiv:** 2209.13085 (v2, 2025-03-05)
- **Authors:** Joar Skalse (University of Oxford), Nikolaus H. R. Howe (Mila, Université de Montréal), Dmitrii Krasheninnikov (University of Cambridge), David Krueger (University of Cambridge).
- **Venue:** NeurIPS 2022.

## Impact

Every paper in this wiki that discusses [[Concept — Reward hacking]] up to this point - [[Paper — Concrete Problems in AI Safety (2016)]], [[Paper — Deep RL from Human Preferences (2017)]], [[Paper — Learning to Summarize from Human Feedback (2020)]], [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]] - uses the term descriptively: a documented empirical failure mode, illustrated with concrete examples but never mathematically pinned down.
This paper supplies the missing formal definition, and its central theoretical result is bracing: for the full space of stochastic policies, a proxy reward and a true reward can only be **unhackable** (safe to optimize) if one of them is trivial (constant).
In other words, almost any non-trivial proxy is, in principle, hackable - a formal underpinning for the empirical pattern the wiki's RLHF papers keep rediscovering.

## Problem it solved

Prior discussion of reward hacking, including [[Paper — Concrete Problems in AI Safety (2016)]]'s original framing, was entirely descriptive: examples of proxies going wrong, informal intuitions about "narrower" or "simpler" proxies being safer, no precise criterion for when a proxy is safe to optimize at all.
This left open a basic question the field needed answered: given a true reward `R` and a candidate proxy `R̃`, is there a rigorous test for whether optimizing `R̃` could ever *decrease* performance on `R`?

## Core idea: unhackability and simplification

A pair of reward functions is **hackable** relative to a policy set if there exist two policies `π, π'` where the proxy ranks `π` below `π'` but the true reward ranks them the other way around - i.e. the proxy and the true reward disagree about which of two policies is better.
If no such pair exists, the reward pair is **unhackable**: improving the proxy can never make the true reward worse.

Two intuitive ways of "simplifying" a reward function - **overlooking rewarding features** (dropping some terms from the reward) and **overlooking fine-grained detail** (coarsening the values) - both turn out to produce hackable pairs far more often than intuition suggests, illustrated with a running example of a household robot asked to clean an attic, bedroom, and kitchen.

The paper's central mathematical result (Theorem 1) is that **unhackability requires restricting the policy set**: for any policy set containing an open subset of the full stochastic policy space (which includes the set of *all* stochastic policies, and even the set of all "sufficiently good" or "sufficiently close to deterministic" policies), any unhackable, non-trivial pair of reward functions must actually be *equivalent* - inducing the exact same ranking over every policy.
There is essentially no room for a genuinely different, still-safe proxy once the policy space is rich enough.
The picture changes for **finite policy sets** (Theorem 2): here, non-trivial unhackable pairs always exist, and the paper derives exact necessary-and-sufficient conditions (via a dimensionality argument on state-action visit counts) for when a **simplification** - a strictly weaker, asymmetric special case of unhackability that only ever collapses distinctions rather than reversing them - exists at all.

## Why it endures

The paper doesn't propose a fix; its contribution is diagnostic, and that diagnosis has held up as a durable frame: **a proxy reward function must satisfy a demanding mathematical standard to be safe to optimize hard**, which is a formal argument for treating learned reward models as *auxiliaries to policy learning rather than specifications to be optimized to convergence* - directly consistent with the "online feedback" and "KL-penalty-against-a-reference-policy" mitigations that recur across [[Paper — Deep RL from Human Preferences (2017)]], [[Paper — Learning to Summarize from Human Feedback (2020)]], and [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]] - all of which, in this paper's vocabulary, are ways of limiting *how much* optimization pressure a hackable proxy is subjected to, since limiting the effective policy set (via a KL constraint, early stopping, or online correction) is exactly the lever this paper's theorems say determines whether hacking is even possible.

## Limitations

- The definition is deliberately **strict and symmetric**: it counts a proxy as hackable even if the only policies where it disagrees with the true reward are astronomically unlikely to ever be found by an actual optimizer. The paper is explicit that this conservatism is a limitation, not a feature, and calls for future work on approximate or probabilistic unhackability that accounts for which policies optimization is actually likely to visit.
- No empirical component: this is a pure theory paper with formal proofs and a released software suite for computing hackability/simplification relationships in small toy MDPs, not an evaluation of reward hacking in any real trained system.
- Results are proven only for finite Markov decision processes and Markovian reward functions; the paper leaves open how the theory extends to richer settings (e.g. the RLHF-on-language-models setting this wiki's other post-training alignment papers actually use).
- The main theorem's practical upshot (restrict the policy set) doesn't say *how much* restriction is enough in a real system, only that some restriction is mathematically necessary - the gap between this formal necessary condition and a practical sufficient one for real RLHF pipelines is left to future work.

## Relations

- Supplies the first formal definition for [[Concept — Reward hacking]], which was named and informally framed in [[Paper — Concrete Problems in AI Safety (2016)]].
- Directly informed by the empirical reward hacking documented in [[Paper — Deep RL from Human Preferences (2017)]] and the robustness failures analyzed in [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]], both cited in its related work.
- Its "restrict the policy set to make a proxy safe" conclusion is the theoretical counterpart to the practical mitigations (online feedback, KL penalties against a reference policy) used throughout [[Method — Reinforcement learning from human feedback (RLHF)]].
- Closely related to but distinct from [[Concept — Scalable oversight]]: unhackability asks whether a *given* proxy is safe to optimize at all, while scalable oversight asks how to obtain a *better* proxy cheaply in the first place.

## Up

- [[Cluster — Post-training alignment]]
