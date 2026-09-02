---
type: concept
status: in-progress
updated: 2026-08-07
summary: "Benchmark test data leaking into a web-scraped training set, so reported scores may measure memorization rather than capability."
cluster: ["Foundational canon"]
sources: []
tags: [evaluation, contamination, benchmarks, methodology]
aliases: ["test set contamination", "train-test overlap"]
---

# Concept — Data contamination

## Definition

When examples from an evaluation benchmark appear in a model's training data, so its score partly reflects memorization rather than the ability the benchmark claims to measure.

Largely a non-issue when training sets were curated, and structural once training sets became "most of the internet" - benchmarks are published on the internet, and crawlers do not know which pages are supposed to be held out.

## Why it's hard, not just annoying

Three properties make this resist clean solutions:

- **Detection is fuzzy.** There is no exact string to search for. Practice is n-gram overlap (e.g. flagging any example sharing a 13-gram with any training document), and the threshold trades false positives against false negatives with no principled setting.
- **Overlap is not the same as leakage.** A training document may contain a benchmark's *source passage* without its question-answer pairs, in which case the model gained background knowledge, which is not cheating. Distinguishing these requires manual inspection.
- **The fix is unaffordable.** Contamination is typically discovered after training. Retraining a frontier model to remove it is not economically viable, so the honest options are to measure the effect, exclude the affected benchmarks, or disclose and move on.

## The reference case study

[[Paper — Language Models are Few-Shot Learners (2020)]] contains the field's canonical worked example, and its candor is the reason to cite it.

The authors built an overlap filter, and it had a bug: "a bug in the filtering caused us to ignore some overlaps, and due to the cost of training it was not feasible to retrain the model."
They then audited the damage rather than burying it. Findings worth remembering:

- Over 90% of QuAC, SQuAD2, and DROP examples were flagged - so much that even constructing a clean comparison subset was difficult. Manual inspection showed the source passages, not the questions, had leaked.
- Four Wikipedia language-modeling benchmarks plus Children's Book Test were "almost entirely contained" in the training data and were **dropped from the paper**, despite having been intended as headline results.
- Their overall read was reassuring but hedged: performance on clean subsets barely moved, which means "either our conservative method substantially overestimated contamination or that contamination has little effect on performance."
- They also name the flaw in their own analysis: a clean subset may not be drawn from the same distribution as the original, so memorization gains could be masked by the clean subset simply being harder.

## Why it matters for a safety wiki

Every safety claim resting on a benchmark inherits this problem.
A dangerous-capability eval, a refusal-rate measurement, or a jailbreak-resistance score is only as trustworthy as the guarantee that its test items weren't trained on - and for any public benchmark, that guarantee weakens every year it stays public.
This is a large part of the argument for held-out and adversarially-constructed evals over static public ones.

## Relations

- Documented at length in [[Paper — Language Models are Few-Shot Learners (2020)]] §4.
- Should re-home to an evaluation and red-teaming cluster once the first eval-focused paper is ingested; sitting in the canon for now.

## Up

- [[Cluster — Foundational canon]]
