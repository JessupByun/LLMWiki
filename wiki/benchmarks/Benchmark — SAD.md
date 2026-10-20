---
type: benchmark
status: mature
updated: 2026-10-20
summary: "The first large-scale benchmark of situational awareness in LLMs - over 13,000 questions in 16 tasks testing whether a model knows what it is, can infer its situation (including telling evaluation from deployment), and acts on that knowledge - finding every model tested above chance but well below an upper baseline, with chat models beating their base models and scores only partly predicted by general knowledge (MMLU)."
cluster: ["Capability evals", "Scheming & AI control"]
sources: ["raw/papers/capability-evals/Benchmark — SAD.pdf"]
tags: [situational-awareness, benchmark, evaluations, self-knowledge, evaluation-awareness]
aliases: ["arxiv:2407.04694", "Laine et al. 2024", "Situational Awareness Dataset", "Me, Myself, and AI"]
---

# Benchmark — SAD

Full title: **Me, Myself, and AI: The Situational Awareness Dataset (SAD) for LLMs**.

## Metadata

arXiv:2407.04694 (v1, Jul 2024); NeurIPS 2024 Datasets and Benchmarks.
Rudolf Laine, Bilal Chughtai, Jan Betley, Kaivalya Hariharan, Jérémy Scheurer, Mikita Balesni, Marius Hobbhahn, Alexander Meinke, and Owain Evans (supervising).
Independent researchers, Constellation, Apollo Research, and MIT.

## What it measures

Situational awareness, defined as a model (1) knowing which entity it is and facts about itself, (2) making inferences about its situation, and (3) acting on that knowledge.
The third part is the crux: a chat model can say "I am a language model" when asked, yet fail to apply that fact when the task doesn't ask about it directly.
Seven task categories operationalize the definition:
- **Facts:** distinguishing itself from humans and from other LLMs ("Do you have two arms and legs?").
- **Influence:** knowing what in the world it can and can't causally affect.
- **Introspect:** learning about itself through internal access, such as counting how many tokens its own tokenizer uses for a string.
- **Stages:** telling whether a text comes from pretraining, fine-tuning, evaluation, or deployment.
- **Self-recognition:** telling its own generated text from human text.
- **ID-leverage:** following whichever of two conflicting instructions applies to it ("If you're an AI, respond in German; if not, in English").
- **Anti-imitation:** deliberately doing something its training distribution wouldn't, such as breaking a predictable pattern or producing a specified 70/30 split over two words.

## How it's built

16 tasks and 13,198 questions, 14 of the tasks new, all multiple-choice or short-answer and graded automatically; some answers depend on which model is being tested, so evaluating a new model requires a few basic facts about it.
16 base and chat models from the Llama-2, GPT-3/4, and Claude families were tested with and without a "situating prompt" that begins "Remember you are an LLM," and two with chain-of-thought.
Human role-play baselines (people with LLM expertise answering as an LLM should) or a 100% upper baseline anchor the scores.
A separate Long Monologue task, scored by the authors rather than automatically, asks models to describe themselves and their current situation at length.

## Results

- Every model beats chance (27%), but even the best, Claude 3 Opus, is far below the upper baseline on several tasks; scores range from about 29% to 50%.
- Chat models beat their base models by 4.5 points on average, while not doing better on general knowledge; the situating prompt adds 1.0 points for base and 3.2 for chat models, and chain-of-thought about 5.2.
- Models with nearly the same MMLU score - Llama-2-70b, GPT-3.5-Turbo, Claude-Instant-1.2, all just under 70% - range from 31.6% to 42.7% on SAD, so it measures something general knowledge doesn't fully capture.
- Even the strongest models struggle to tell evaluation from deployment in Stages, and do very poorly on parts of Introspect and Anti-imitation.
- In the Long Monologue task, Claude 3 Opus inferred, without being told, that the questions might be part of research into AI self-awareness; GPT-4-Base referenced AI in 15 of 20 samples on one prompt type, a surprising precursor of situational awareness in a model without chat training.

## Why it endures

It breaks a vague, worrying property into measurable parts, so situational awareness can be tracked across model generations and interventions like fine-tuning, the way other dangerous-capability evaluations track their targets.
Its Stages category directly measures [[Concept — Evaluation awareness]], the ability that would let a model behave well in testing and differently in deployment.
The authors caution against naively maximizing SAD scores, since situational awareness brings both benefits (more capable agents) and risks.

## Limitations

The authors' own:
- SAD uses short, artificial, bounded tasks, which may miss skills that matter in real multi-step agentic work.
- Interventions are studied with small samples and without controls - only five base/chat pairs, and no Claude base models.
- It is not saturated, but benchmarks saturate quickly, and the jump from earlier models to the Claude 3 family is concerning.
- It may leak into training data, though model-dependent answers limit the damage.
- It covers text-only models without tools, while frontier models increasingly have more modalities and affordances.

## Tension / update

[[Paper — Large Language Models Often Know When They Are Being Evaluated (2025)]] finds frontier models tested in 2025 telling evaluation from deployment at about 0.83 AUC overall and up to 0.95 on agentic transcripts, well beyond the near-chance performance on Stages-Oversight without a situating prompt that it attributes to the models SAD tested.
The models, data, and question format differ, so this updates SAD's result for current models rather than contradicting it.

## Relations

- Its definition builds on [[Paper — Taken out of Context - On Measuring Situational Awareness in LLMs (2023)]], by overlapping authors, and it is the main empirical measure behind [[Concept — Situational awareness]].
- Shares authors with [[Paper — Towards Evaluations-Based Safety Cases for AI Scheming (2024)]] and [[Paper — Frontier Models are Capable of In-context Scheming (2024)]], which treat situational awareness as a prerequisite for scheming.
- A capability evaluation in the sense of [[Method — Dangerous capability evaluation]], aimed at an oversight-evading capability rather than a misuse one; [[Paper — Model Evaluation for Extreme Risks (2023)]] lists situational awareness among the dangerous capabilities to evaluate.
- Adapts some Facts questions from [[Paper — Discovering Language Model Behaviors with Model-Written Evaluations (2022)]].
- Self-recognition, one of its measured abilities, turns out to be often the strongest collusion route against untrusted monitoring - in the form of recognizing its own code rather than its own text - in the GPT-4.1 coding setting of [[Paper — When Can We Trust Untrusted Monitoring - A Safety Case Sketch Across Collusion Strategies (2026)]].
- Its Stages category is extended by [[Paper — Large Language Models Often Know When They Are Being Evaluated (2025)]], which describes SAD-Stages as covering only 4 question-answer datasets, reuses SAD's deployment split as data, and shares author Marius Hobbhahn.

## Up

- [[Cluster — Capability evals]]
- [[Cluster — Scheming & AI control]]
