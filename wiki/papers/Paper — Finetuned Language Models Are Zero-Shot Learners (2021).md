---
type: paper
status: in-progress
updated: 2026-09-19
summary: "Finetunes a 137B-parameter language model on 60+ NLP datasets rewritten as natural-language instructions, and shows the resulting model (FLAN) beats zero-shot GPT-3 on 20 of 25 held-out task types it was never explicitly trained on."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Finetuned Language Models Are Zero-Shot Learners (2021).pdf"]
tags: [instruction-tuning, zero-shot, llm]
aliases: ["arxiv:2109.01652", "Wei et al. 2021", "FLAN"]
---

# Paper — Finetuned Language Models Are Zero-Shot Learners (2021)

Commonly known as **FLAN**.

## Metadata

- **arXiv:** 2109.01652 (v5, 2022-02-08)
- **Authors:** Jason Wei, Maarten Bosma, Vincent Y. Zhao, Kelvin Guu, Adams Wei Yu, Brian Lester, Nan Du, Andrew M. Dai, Quoc V. Le (Google Research).
- **Venue:** ICLR 2022.

## Impact

Instruction tuning as a general technique is unrelated to RLHF, but it establishes the "make a language model follow a natural-language instruction" objective that every later chat-oriented model, aligned or not, is finetuned toward, and its held-out task-cluster evaluation methodology (finetune on everything except one task type, then test zero-shot exactly on that type) became the standard way to demonstrate genuine cross-task generalization rather than memorization of a familiar format.
Zero-shot FLAN (137B) beats zero-shot GPT-3 (175B, a larger model) on 20 of 25 evaluated datasets, and beats *few-shot* GPT-3 outright on ANLI, RTE, BoolQ, AI2-ARC, OpenbookQA, and StoryCloze - a smaller model with no in-context examples outperforming a bigger model given several examples per query.

## Core idea

See [[Method — Instruction Tuning]] for the full mechanism.
In short: aggregate 62 existing NLP datasets across 12 task clusters (NLI, sentiment, translation, summarization, closed-book QA, and more), write up to ten instruction templates per dataset by hand, finetune a 137B-parameter pretrained decoder-only Transformer (LaMDA-PT) on the resulting mixture, and evaluate zero-shot on a task cluster held entirely out of finetuning.
For example, to test natural language inference, the model is finetuned on translation, sentiment, commonsense, and the other clusters, but never on a single NLI example, then evaluated cold on ANLI, RTE, CB, and friends.

## Why it endures

The paper's own ablations (§4.1-4.3) are as durable as the headline result: performance on held-out clusters climbs steadily as more task clusters are added to finetuning with no sign of saturating; the benefit is essentially absent (and can even hurt) below ~8B parameters and only becomes clearly positive around 68-137B; and stripping the natural-language instructions out of finetuning (bare input/output pairs, or just a task name) recovers only a fraction of the gain, confirming the effect comes specifically from learning to follow instructions rather than from multi-task finetuning alone.
This last point is why "instruction tuning" survives as a named, separately citable technique rather than folding into generic multi-task learning.

## Relations

- Opens [[Method — Instruction Tuning]].
- Directly contrasted with the RLHF post-training route in [[Paper — Training LMs to Follow Instructions (2022)]], which OpenAI's InstructGPT paper (cited in FLAN's own related work) demonstrates roughly concurrently as an alternative, non-RL path to a model that follows instructions well; FLAN uses only supervised multi-task finetuning on labeled datasets, no preference comparisons and no RL.
- Complementary to few-shot prompting and prompt tuning, both of which FLAN's own ablations show still help further once a model is instruction-tuned.

## Up

- [[Cluster — Foundational canon]]
