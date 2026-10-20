---
type: paper
status: mature
updated: 2026-10-19
summary: "Argues that claimed emergent abilities of language models are largely artifacts of how performance is scored: metrics like exact-match accuracy or multiple-choice grade turn smoothly improving per-token predictions into sudden jumps, and switching to continuous metrics or using more test data makes the jumps disappear - shown on GPT-3 arithmetic, a BIG-Bench meta-analysis, and vision models where emergence is manufactured on demand."
cluster: ["Capability evals"]
sources: ["raw/papers/capability-evals/Paper — Are Emergent Abilities of Large Language Models a Mirage (2023).pdf"]
tags: [scaling, emergence, evaluation-metrics, forecasting, capabilities]
aliases: ["arxiv:2304.15004", "Schaeffer et al. 2023", "emergence mirage"]
---

# Paper — Are Emergent Abilities of Large Language Models a Mirage (2023)

Full title: **Are Emergent Abilities of Large Language Models a Mirage?**

## Metadata

arXiv:2304.15004 (v2, May 2023; originally Apr 2023); NeurIPS 2023.
Rylan Schaeffer, Brando Miranda, and Sanmi Koyejo (Computer Science, Stanford University).

## Impact

[[Paper — Emergent Abilities of Large Language Models (2022)]] made "emergence" a central worry: abilities, maybe dangerous ones, that appear suddenly and unpredictably as models grow.
This paper argues that much of that suddenness comes from the measuring stick, not the model.
If you score an answer as all-or-nothing, steady improvement in getting each token right shows up as a cliff; score partial credit instead and the cliff becomes a slope.
For safety, that cuts both ways: it suggests many capability jumps may be forecastable with the right metric, and it warns that an evaluation's headline number can make a model look further from (or closer to) a dangerous capability than it really is.

## Problem it solved

This paper distills two defining properties of emergent abilities from the prior literature - **sharpness** (jumping from absent to present seemingly at once) and **unpredictability** (appearing at unforeseeable scales).
The emergence paper and BIG-Bench had already noticed that cross-entropy loss improves smoothly where accuracy doesn't, and floated metrics as a partial explanation, but nobody had turned that into testable predictions or checked whether it could account for the phenomenon in general.

## Core idea

Suppose a model's chance of getting each token right improves smoothly with scale, say from 80% to 90% to 95%.
Now score a five-digit addition problem as correct only if all five digits are right.
The chance of a perfect answer is roughly the per-token chance raised to the fifth power: about 33%, then 59%, then 77% - and for small models, near zero.
On a log-scale plot of model size, that looks like nothing, nothing, then a sudden leap.
Score the same outputs by token edit distance - how many digits are wrong - and the curve is smooth and predictable.

So emergence, the paper argues, comes mainly from three choices by the researcher:
1. A metric that is **nonlinear** (like exact-match accuracy over many tokens) or **discontinuous** (like multiple-choice grade, a step function of which option gets the most probability).
2. Too little test data to measure small models' low but nonzero performance, so they appear to score exactly zero.
3. Too few large models sampled to see the curve's shape.

## How it's built

Three complementary tests:
- **GPT-3 arithmetic.** Fresh outputs from the publicly queryable InstructGPT/GPT-3 family (four sizes, 350M to 175B) on 2-digit multiplication and 4-digit addition, the arithmetic emergence examples the earlier papers featured, rescored with different metrics and larger test sets.
- **BIG-Bench meta-analysis.** Published results across BIG-Bench tasks, checking which metrics show emergence and whether LaMDA's emergent tasks stay emergent when rescored.
- **Vision.** Emergence deliberately induced in vision, a domain where none had been reported, across several architectures - shallow autoencoders on CIFAR-100, convolutional networks, and transformers classifying Omniglot characters - by choosing sharp metrics.

## Results

- On the arithmetic tasks, GPT-3 shows emergence under exact-match accuracy for 4- and 5-digit targets, but the same outputs improve smoothly under token edit distance.
- With more test data, every model in the family scores above chance even on accuracy, and accuracy falls roughly geometrically with answer length, as the simple model predicts.
- Across BIG-Bench, at most 5 of 39 preferred metrics show emergence at all, and over 92% of hand-annotated emergent abilities appear under just two metrics: multiple-choice grade and exact string match.
- LaMDA's emergent abilities under multiple-choice grade disappear when the same tasks are scored with the continuous Brier score.
- Choosing a thresholded reconstruction metric makes autoencoders show "emergent" image reconstruction, and requiring all images in a sequence to be correct makes transformers show "emergent" Omniglot classification, each closely resembling published LLM emergence curves.

## Why it endures

It recast emergence as a measurement question: a task and a metric are separate choices, and the metric decides whether progress looks smooth or sudden.
That lesson shapes capability evaluation: the expert-bits measure of [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]] is explicitly motivated by this paper's call for a smooth y-axis to forecast against, and [[Concept — Task-completion time horizon]] pursues a similar continuous scale for a different reason, benchmark saturation.
It also gives a methodological warning about multiple comparisons: with on the order of a million task-metric-model combinations in BIG-Bench, some will look emergent by chance.

## Limitations

The authors stress that they do not claim language models cannot have emergent abilities - only that previously claimed ones may be artifacts of researchers' analysis.
Their simple model assumes each token is correct independently, which they acknowledge is false, though it reproduces the qualitative pattern.
Most models with claimed emergence are not publicly queryable, so beyond GPT-3 the analysis is limited to published results; they name closed models and outputs as a barrier to this kind of independent check.
A smooth underlying metric does not by itself mean a capability's practical arrival is gradual: if a real use needs a fully correct answer, the all-or-nothing curve is the one that matters - a point the debate over this paper has pressed.

## Tension / update

Directly disputes [[Paper — Emergent Abilities of Large Language Models (2022)]], arguing its few-shot emergent abilities may be metric artifacts; it does not address that paper's emergent prompting techniques.
That paper had conceded metrics could disguise incremental progress on long outputs but held that emergence on classification tasks resisted this explanation; this paper's finding that multiple-choice grade, a discontinuous metric, is the most common metric under which emergence appears, and that Brier rescoring removes LaMDA's emergence on such tasks, bears directly on that objection.

## Relations

- Cites [[Paper — Language Models are Few-Shot Learners (2020)]] and GPT-3's arithmetic results as the original emergent abilities it re-examines.
- Builds on the smooth power-law loss curves of [[Paper — Scaling Laws for Neural Language Models (2020)]] as its starting assumption.
- Cited by [[Paper — Evaluating Language-Model Agents on Realistic Autonomous Tasks (2023)]] for the point that measured progress can look continuous or abrupt depending on the metric, and by [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]] as motivation for a smooth measure of dangerous capability.

## Up

- [[Cluster — Capability evals]]
