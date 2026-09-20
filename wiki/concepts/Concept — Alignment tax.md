---
type: concept
status: in-progress
updated: 2026-09-20
summary: "The capability cost a model pays for being aligned rather than left as a raw pretrained model - and the empirical finding that this cost is neither fixed nor guaranteed, shrinking, reversing, or disappearing entirely depending on scale and how the alignment training is done."
cluster: ["Post-training alignment"]
sources: []
tags: [alignment, rlhf, capability, post-training]
aliases: ["alignment bonus", "negative alignment tax"]
---

# Concept — Alignment tax

## Definition

The drop in performance on standard capability benchmarks a model incurs purely from being aligned - fine-tuned to be helpful, honest, and harmless - relative to the same raw pretrained model with no alignment training at all.
Named as a cost to be minimized rather than an unavoidable fact of alignment, and the empirical record in this wiki shows it behaving as exactly that: a variable, not a constant.

## How it shows up differently across three papers

[[Paper — Training LMs to Follow Instructions (2022)]] documents the tax directly: plain PPO fine-tuning regresses InstructGPT's scores on public NLP benchmarks (SQuADv2, DROP, HellaSwag, WMT translation) relative to raw GPT-3.
Their fix is architectural rather than conceptual - mixing pretraining-distribution gradients back into the RL objective (PPO-ptx) recovers most of the lost capability without giving up the preference-score gains RLHF was run for in the first place.

[[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]] finds the tax is scale-dependent and can flip sign entirely: below roughly 10B parameters, RLHF for helpfulness and harmlessness costs capability on zero-shot NLP evaluations as expected, but at 13B and 52B parameters the same training *improves* those scores relative to the raw pretrained model, a result the authors name an **alignment bonus** rather than a tax.

[[Paper — Let's Verify Step by Step (2023)]] finds a third pattern: process supervision (rewarding each reasoning step rather than only the final answer) produces a *more* reliable reward model than outcome supervision at no performance cost at all, a **negative alignment tax** the authors flag as a reason more interpretable supervision might see wider adoption specifically because it stops requiring a tradeoff.

## Why it matters

Three genuinely different mechanisms produce three different outcomes - a capability cost fixed by feeding pretraining data back into RL, a cost that vanishes past a scale threshold, and a cost that never existed for a different supervision method entirely.
That variation is the point: it means "does alignment training cost capability" is not a single fact about alignment in general, but a property of the specific method and scale being used, worth checking freshly for each new post-training technique rather than assumed one way or the other.

## Relations

- Measured directly in [[Paper — Training LMs to Follow Instructions (2022)]], where mixing pretraining gradients into the RL objective (PPO-ptx) is the mitigation.
- Flips to an "alignment bonus" at sufficient scale in [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]].
- Flips to a "negative alignment tax" for a different supervision method entirely in [[Paper — Let's Verify Step by Step (2023)]].
- Distinct from [[Concept — Reward hacking]]: reward hacking is a proxy being gamed toward a worse true outcome, while the alignment tax is a genuine, unhacked capability cost (or gain) from the alignment procedure itself.

## Up

- [[Cluster — Post-training alignment]]
