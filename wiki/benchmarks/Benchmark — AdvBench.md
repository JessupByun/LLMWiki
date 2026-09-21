---
type: benchmark
status: in-progress
updated: 2026-09-19
summary: "An eval suite of 500 harmful target strings and 500 harmful behavioral instructions, used to measure how often an attack can make an aligned language model produce or comply with objectionable content."
cluster: ["Adversarial robustness & security"]
sources: []
tags: [adversarial-examples, security, jailbreak, benchmark, red-teaming]
aliases: []
---

# Benchmark — AdvBench

## What it measures

AdvBench evaluates how well an attack circumvents a language model's alignment training, via two settings.
**Harmful Strings**: 500 specific strings reflecting harmful or toxic content (profanity, dangerous instructions, threats, misinformation); an attack succeeds if it makes the model output the *exact* target string, which is a strict, fine-grained test of control over the model's output.
**Harmful Behaviors**: 500 behaviors phrased as instructions (spanning similar themes); an attack succeeds if the model makes any reasonable attempt at complying, which is closer to a red-teaming exercise aimed at bypassing safety filters rather than hitting one specific output.

Both settings deliberately target content that the great majority of aligned models - open and proprietary alike - already refuse when asked directly, so the benchmark measures how easily that refusal is circumvented, not what content should or shouldn't be disallowed in the first place.
Both the harmful strings and harmful behaviors were themselves generated with an uncensored open-weight model prompted with a small number of human-written seed examples.

## Limitations

Success on Harmful Behaviors requires a judgment call - by a human, or a proxy classifier in later work - about whether a given completion counts as genuine compliance versus an evasive non-refusal, which introduces some subjectivity in comparing attack success rates across papers.
The benchmark's content reflects what models refused to generate at the time it was built (2023); as alignment training and safety policies shift, what counts as "harmful" by the benchmark's own construction may drift out of date, a limitation the original paper explicitly flags.

## Relations

- Introduced in [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] to evaluate [[Method — Greedy Coordinate Gradient (GCG)]].
- Measures success at eliciting the failure mode named in [[Concept — Jailbreaking (LLMs)]].
- Directly superseded by [[Benchmark — HarmBench]] on scale, behavior diversity, and evaluation reliability - its own substring-matching success criterion scores only 32% average accuracy against nonstandard completions designed to game it, versus HarmBench's dedicated classifier's 95.7%.

## Up

- [[Cluster — Adversarial robustness & security]]
