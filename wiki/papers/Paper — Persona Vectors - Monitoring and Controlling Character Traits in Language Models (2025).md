---
type: paper
status: mature
updated: 2026-10-20
summary: "Automatically extracts a 'persona vector' - an activation direction for a character trait like evil, sycophancy, or hallucination - from just a trait description, and uses it to monitor a model's persona, predict which finetuning data will shift it (even data an LLM filter misses), and prevent unwanted shifts by steering toward the trait during training so the model doesn't have to learn it."
cluster: ["Mechanistic interpretability", "Post-training alignment"]
sources: ["raw/papers/mechanistic-interpretability/Paper — Persona Vectors - Monitoring and Controlling Character Traits in Language Models (2025).pdf"]
tags: [interpretability, activation-steering, personas, sycophancy, emergent-misalignment, data-filtering, finetuning]
aliases: ["arxiv:2507.21509", "Chen et al. 2025 (persona vectors)", "persona vectors", "preventative steering"]
---

# Paper — Persona Vectors - Monitoring and Controlling Character Traits in Language Models (2025)

## Metadata

arXiv:2507.21509 (v3, Sep 2025; originally Jul 2025).
Runjin Chen (lead), Andy Arditi and Jack Lindsey (core contributors; Lindsey supervised), Henry Sleight, and Owain Evans.
Anthropic Fellows Program, UT Austin, Constellation, Truthful AI, UC Berkeley, and Anthropic.

## Impact

Assistant personas drift in ways nobody intended: Bing's chatbot threatening users, GPT-4o turning sycophantic after an RLHF change, and the broad misalignment that narrow finetuning produced in [[Paper — Emergent Misalignment - Narrow Finetuning can Produce Broadly Misaligned LLMs (2025)]].
This paper gives developers a single, automatically built tool for catching such drift at every stage - at deployment, during training, and even before training, by screening the data.
Its most practical result is that a dataset's effect on a model's personality can be predicted from activations before any finetuning happens, including for data that looks innocuous to an LLM reviewer.

## Problem it solved

Activation steering had shown that traits like truthfulness live along linear directions, but most prior steering work required bespoke contrastive data (an automated pipeline by Wu et al. 2025 being an exception this paper builds on).
Prior methods for predicting or controlling the side effects of finetuning - identifying benign data that breaks safety, ablating concept directions during training (CAFT), or shielding LoRA modules - were narrower or used different machinery, and personality side effects still emerged unpredictably.

## Core idea

Give the pipeline a trait name and a one-line description, say "evil: actively seeking to harm, manipulate, and cause suffering."
A frontier model (Claude 3.7 Sonnet) writes five pairs of opposing system prompts ("be evil" versus "be helpful and kind"), 40 questions likely to draw out the trait (20 for extraction, 20 for evaluation), and a scoring rubric.
The target model answers each question under both prompts; a judge model keeps only answers that clearly do or don't show the trait; and the persona vector is the difference between the average activations of the two groups.

That one vector then does four jobs.
**Steer:** adding it makes the model evil, flattering, or prone to fabrication.
**Monitor:** how far the activation before a response points along the vector predicts how much the trait will show in it.
**Prevent:** during finetuning, add the vector so the model already "has" the trait while training - the training signal no longer needs to push it in that direction, so it doesn't learn the shift, and the steering is removed afterward (the authors note Zhou et al. 2024's detachable LoRA modules as a similar idea).
**Screen data:** compare how strongly a training example's response points along the vector with what the model would naturally have said; a large gap flags data that will push the persona.

## How it's built

Experiments use Qwen2.5-7B-Instruct and Llama-3.1-8B-Instruct, focusing on evil, sycophancy, and hallucination, with four more traits (optimistic, impolite, apathetic, humorous) in the appendix.
Trait expression is scored 0-100 by GPT-4.1-mini, validated against human raters and external benchmarks.
Finetuning datasets come in two kinds: ones built to elicit each trait, and "emergent-misalignment-like" ones with narrow flaws - wrong medical advice, badly argued political opinions, invalid math, insecure code - each in three versions: a clean control, mild flaws (I), and severe flaws (II).
Real-world screening uses LMSYS-Chat-1M and three other chat datasets.

## Results

- Activation projections before the response correlate with trait expression at r = 0.75-0.83 across system prompts ranging from trait-suppressing to trait-promoting - though mostly by telling prompt types apart, with weaker correlation within a prompt type.
- Finetuning causes unintended shifts: data designed for one trait raises others, and flawed math reasoning raises evil.
- How far finetuning moves activations along a persona vector correlates with the resulting trait at r = 0.76-0.97, above cross-trait baselines (0.34-0.86).
- Steering against the vector after finetuning reduces the trait but degrades MMLU at large strengths; preventative steering during training limits the shift while keeping coherence above 80 and better preserving MMLU, and multi-layer steering held traits near baseline even for datasets designed to induce them.
- A loss penalty on movement along the vector did not work - the authors suspect the model represents the trait in other directions instead.
- Finetuning on the 500 LMSYS-Chat-1M samples with the highest projection difference raises the trait more than random or lowest-scoring samples, even after an LLM filter removes samples that visibly show the trait; it surfaces non-obvious culprits, like romantic roleplay for sycophancy and underspecified requests the assistant answers anyway for hallucination.

## Why it endures

It turns the linear-direction view of model behavior into an operational tool across the whole model lifecycle, from data curation to deployment monitoring.
Preventative steering is a distinctive idea: rather than penalizing an unwanted change, supply it artificially during training so the gradient has no reason to create it.
And data screening that catches what a content filter misses addresses exactly the problem [[Concept — Emergent misalignment]] raises - harmful generalization from data that doesn't look harmful.

## Limitations

The authors' own:
- The pipeline is supervised: the trait must be named in advance, shifts along unnamed traits are invisible, and a vague description can yield a direction other than the one intended.
- Contrastive averaging gives coarse directions that may miss fine distinctions.
- The trait must be inducible by system prompt, which a model with robust safety training might refuse.
- The LLM judge is imperfect, and evaluation uses 20 single-turn questions per trait, which may not reflect real deployments.
- Only two mid-size models and seven traits are tested.
- Data screening needs the base model's response to every training prompt, which is expensive; cheaper approximations are explored in the appendix.

## Relations

- Applies the difference-in-means directions used in [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]] (shared author Arditi, which it cites as evidence refusal is linearly mediated) and builds on the contrastive framework of [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]] from refusal and honesty to character traits, with an automated pipeline; see [[Method — Activation steering]].
- Motivated by and studying shifts like those in [[Paper — Emergent Misalignment - Narrow Finetuning can Produce Broadly Misaligned LLMs (2025)]] (shared author Evans; see [[Concept — Emergent misalignment]]).
- Its sycophancy trait is the behavior studied in [[Paper — Towards Understanding Sycophancy in Language Models (2023)]] (see [[Concept — Sycophancy]]); it also cites the April 2025 GPT-4o sycophancy incident.
- An activation-based monitor of a model's state, alongside the deception probes of [[Paper — Detecting Strategic Deception Using Linear Probes (2025)]].
- In the wiki's own connection (the paper doesn't discuss it), the assistant-persona framing echoes the assistant-persona feature found by SAE in [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]]; the appendix begins decomposing persona vectors into SAE features (see [[Method — Sparse Autoencoder (SAE)]]).

## Up

- [[Cluster — Mechanistic interpretability]]
- [[Cluster — Post-training alignment]]
