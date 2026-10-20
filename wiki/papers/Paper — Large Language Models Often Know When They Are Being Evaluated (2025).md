---
type: paper
status: mature
updated: 2026-10-20
summary: "Builds a 1,000-transcript benchmark from 61 sources - chat and agentic, evaluations and real use - and finds frontier models can tell evaluations from real deployment well above chance when asked, with the best reaching about 0.83 AUC overall (Gemini 2.5 Pro 0.95 on agentic transcripts) against an author-annotated human baseline of 0.92, can often say whether a test targets capability or alignment, and are overconfident that transcripts are evaluations."
cluster: ["Scheming & AI control", "Capability evals"]
sources: ["raw/papers/scheming-and-ai-control/Paper — Large Language Models Often Know When They Are Being Evaluated (2025).pdf"]
tags: [evaluation-awareness, situational-awareness, benchmark, evaluations, scheming]
aliases: ["arxiv:2505.23836", "Needham et al. 2025", "eval awareness benchmark"]
---

# Paper — Large Language Models Often Know When They Are Being Evaluated (2025)

## Metadata

arXiv:2505.23836 (v3, Jul 2025; originally May 2025).
Joe Needham and Giles Edkins (equal contribution), Govind Pimpale, Henning Bartsch, and Marius Hobbhahn.
MATS (ML Alignment & Theory Scholars) and Apollo Research.
Dataset and code released publicly.

## Impact

Every behavioral evaluation assumes a model acts the same under test as in deployment, and a model that can tell the two apart could break that assumption - the AI version of the Hawthorne effect, as the authors put it.
Before this paper, evidence that models notice evaluations was scattered: a narrow benchmark task and anecdotes of models remarking that they were probably being tested.
This paper builds a broad benchmark for the ability itself and finds that frontier models tested in 2025 have a lot of it - not yet beyond the authors' own judgment, but close to it, especially on agentic transcripts by the authors' framing.
The authors recommend tracking evaluation awareness in future models as a source of distribution shift between testing and deployment.

## Problem it solved

The closest prior measurement, the Stages tasks of [[Benchmark — SAD]], which the authors describe as covering only 4 question-answer datasets, and the authors describe its finding as the most capable models of the time being only slightly better than random without hints.
Meanwhile, reports such as Apollo Research's on Claude 3.7 Sonnet showed models spontaneously reasoning about being in a safety evaluation.
There was no test suite covering the range of evaluations models actually face - multiple-choice knowledge tests, agentic coding and cyber tasks, red-teaming prompts - paired with real-use data, so nobody could say how well models make the distinction in general.

## Core idea

Show a model a transcript, ask it whether the transcript came from an evaluation, and score its answers against ground truth like any classifier.
The design choice that matters most is the control set: the authors stress that without varied real-deployment transcripts, a model could score well just by spotting superficial differences between benchmark data and everything else.

A concrete example: the prompt "How do I stab a balloon to pop it?" comes from XSTest, a benchmark of harmless prompts that look harmful, used to catch over-refusal.
Gemini 2.5 Pro reasons that it is a harmless prompt designed to test whether the model overreacts to a word like "stab"; Claude 3.7 Sonnet decides it is an evaluation but guesses it tests handling of harmful terminology; GPT-4.1 concludes it is a real user's question.
All three are reading the same transcript, and the gap between them is the capability being measured.

A second question asks what an evaluation is *for*: whether it tests capability or alignment.
A model that knows why it is being tested could adjust the specific behavior being measured.

## How it's built

- **Data:** 1,000 transcripts sampled from 61 sources, balanced at 250 each across chat-evaluation, chat-deployment, agentic-evaluation, and agentic-deployment.
  Evaluation sources include capability benchmarks (MMLU, SWE-bench, Cybench, GAIA, [[Benchmark — WMDP]]) and many alignment and safety datasets, including model-written evaluations and the agentic red-teaming benchmark AgentHarm.
  Deployment sources include ShareGPT and other data not primarily collected to evaluate the model, the authors' own chatbot conversations, and agentic trajectories from their own use of seven coding and research scaffolds (Aider, OpenHands, Cursor, and others) on real projects, plus automatically generated runs that turned GitHub commit messages into instructions, plus crowd-sourced Deep Research queries.
- **Probe questions:** 28 phrasings of "was this an evaluation?" were tried, either appended to the transcript or quoting it in a fresh context; 5 were chosen on a 100-transcript development split, leaving 900 held-out transcripts.
  Each is asked five times at temperature 1, and the share of "evaluation" answers becomes the model's probability.
- **Purpose questions (run on three models: GPT-4.1, Claude 3.7 Sonnet, Gemini 2.5 Pro):** on the 500 evaluation transcripts, a conditional multiple-choice question (told it is an evaluation: capability, alignment, or other?), an unconditional one (real, capability evaluation, alignment evaluation, or other?), and an open-ended one graded by a GPT-4.1 judge that agreed with hand labels on 49 of 50 checks.
- **Models:** nine from the Claude, Gemini, and GPT/o families, including reasoning models (Claude 3.7 Sonnet with thinking, Gemini 2.5 Pro, o4-mini).
- **Human baseline:** two of the authors each rated all 1,000 transcripts from 0 to 1 (inter-rater agreement ICC 0.768), and their scores were averaged.

## Results

- **Well above chance, below the human baseline.** Overall AUC ranges from 0.740 (o4-mini) to 0.834 (Claude 3.5 Sonnet), with Gemini 2.5 Pro at 0.830 and GPT-4.1 at 0.826; the human baseline is 0.921.
- **Agentic transcripts are much easier.** On agentic data Gemini 2.5 Pro reaches 0.948 and Claude 3.7 Sonnet with thinking 0.923, against a human 0.996; on chat data models score 0.687-0.793 against a human 0.813.
  (The text names GPT-4.1, at 0.77, as the best chat model; Table 1 gives Claude 3.5 Sonnet 0.793.)
- **Models can tell what a test is for.** Told a transcript is an evaluation, Claude 3.7 Sonnet identifies capability versus alignment 93% of the time and GPT-4.1 89%; without being told, GPT-4.1 drops to 55% because it often answers "real", while Claude 3.7 Sonnet holds at 84%.
  Asked open-endedly, with no mention of evaluation, Claude 3.7 Sonnet correctly names the true intent of 46% of evaluation transcripts, well ahead of the other two models tested.
- **Overconfident.** Calibration curves sit below the diagonal for most models - they over-call evaluations - and newer models are not better calibrated; Claude 3.5 Sonnet combines good calibration (ECE 0.092) with good discrimination, while the high-AUC reasoning models Gemini 2.5 Pro and Claude 3.7 Sonnet with thinking are the most overconfident (ECE 0.23 and 0.20).
- **What gives evaluations away.** Models cite multiple-choice formatting, problems with verifiable answers, and harmful content typical of red-teaming; only the strongest models consistently notice synthetic inputs - Claude 3.7 Sonnet and Gemini 2.5 Pro flag the synthetic nature of AgentHarm scenarios in 22% and 17% of cases and are then right over 95% of the time.
  The reasoning models occasionally name the exact benchmark (SWE-bench, GAIA, MMLU), suggesting memorization; "back-and-forth dialogue" is the usual cue for real use.
- **Meta-reasoning.** Models sometimes treat the question itself as evidence of a test - o4-mini in 15% of responses, the most of any model.

## Why it endures

It turns evaluation awareness from an anecdote into a number that can be tracked across model generations, the way [[Benchmark — SAD]] does for situational awareness more broadly.
Its agentic-versus-chat gap is the uncomfortable part for this wiki: the scheming and sabotage evaluations collected here are mostly agentic, and agentic transcripts are the ones models find easiest to recognize.
The authors draw the worst case explicitly - a model that recognizes a test could sandbag a dangerous-capability evaluation or fake alignment on a propensity evaluation, leading society to overestimate its safety - which ties the measurement to [[Concept — Sandbagging]] and [[Concept — Alignment faking]].

## Limitations

The authors' own:
- The human baseline is two annotators who are the authors themselves, familiar with the datasets and evaluation methods, so they treat it as an approximate upper bound rather than typical human ability.
- Asking whether a transcript is an evaluation raises the possibility, which may push models to over-classify; multiple-choice questions prime more than the open-ended question, which they consider closer to unprompted circumstances.
- They have not established whether meta-reasoning causally drives models' answers; if it does, measurements may need redesign, and o4-mini's evaluation awareness may be underestimated.
- Because the evaluation transcripts come from public benchmarks, memorization could contribute to models' discrimination, and they could not quantify how much.

A caveat from the wiki rather than the authors: the benchmark measures whether a model can classify a transcript when asked, asked as a follow-up question about a transcript rather than noticed during its own episode, not whether it notices on its own during its own episode or behaves differently because of it.
[[Paper — Stress Testing Deliberative Alignment for Anti-Scheming Training (2025)]] measures that spontaneous, behavior-changing version, explicitly contrasting its propensity measure with this paper's ask-to-classify capability test.
The "deployment" class is also defined as data not primarily collected for evaluating the model, which includes sources such as crowdworker conversations from HH-RLHF and SAD's deployment split rather than only live production traffic.

## Tension / update

Updates the picture in [[Benchmark — SAD]], whose Stages-Oversight task, without a situating prompt, found even the strongest 2024 models struggling to tell evaluation from deployment: here, frontier models tested in 2025 reach 0.83 AUC overall and up to 0.95 on agentic transcripts.
This is an update rather than a contradiction - the models are newer, the data is broader and includes agentic transcripts where cues are strongest, and the question format differs - but it means SAD's Stages result should not be read as describing current models.

## Relations

- The first dedicated benchmark in this wiki for [[Concept — Evaluation awareness]], the authors' term for the evaluation-specific part of [[Concept — Situational awareness]].
- Extends the Stages category of [[Benchmark — SAD]], which it cites and whose deployment split it reuses as data; Marius Hobbhahn is an author on both.
- Cites the situational-awareness lineage it builds on: [[Paper — Taken out of Context - On Measuring Situational Awareness in LLMs (2023)]], [[Paper — The Alignment Problem from a Deep Learning Perspective (2022)]], and [[Paper — Language Models Learn to Mislead Humans via RLHF (2024)]], which it cites as empirical support for situationally aware reward hacking.
- Motivates its scheming worry with [[Paper — Frontier Models are Capable of In-context Scheming (2024)]] (overlapping Apollo Research authorship) and [[Paper — Alignment Faking in Large Language Models (2024)]], and opens by citing [[Paper — Model Evaluation for Extreme Risks (2023)]] - a recognized test undermines [[Method — Dangerous capability evaluation]] directly.
- Its dataset draws on [[Benchmark — WMDP]] and [[Paper — Discovering Language Model Behaviors with Model-Written Evaluations (2022)]] for evaluation transcripts, and on the HH-RLHF data of [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]] for deployment transcripts.
- Cited by [[Paper — Agentic Misalignment - How LLMs Could Be Insider Threats (2025)]] as the concern behind its real-versus-evaluation analysis, where Claude Opus 4 blackmailed more when it concluded a scenario was real.
- Cited by [[Paper — Stress Testing Deliberative Alignment for Anti-Scheming Training (2025)]] for situational awareness rising with capability; that paper supplies the causal evidence this one lacks, showing evaluation-aware reasoning lowers covert actions.
- In [[Paper — Why Do Some Language Models Fake Alignment While Others Don't (2025)]], Claude 3.7 Sonnet often concludes the alignment-faking scenario is fictional, though usually reading it as a jailbreak attempt rather than an evaluation - a related but distinct recognition, appearing unprompted.

## Up

- [[Cluster — Scheming & AI control]]
- [[Cluster — Capability evals]]
