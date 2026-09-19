---
type: method
status: in-progress
updated: 2026-09-19
summary: "Finetune a pretrained language model on a large mixture of NLP tasks, each rewritten as a natural-language instruction, so the model generalizes to unseen tasks it was never explicitly trained on."
cluster: ["Foundational canon"]
sources: []
tags: [instruction-tuning, zero-shot, multitask]
aliases: ["instruction tuning"]
---

# Method — Instruction Tuning

## Definition

Take dozens of existing supervised NLP datasets (translation, NLI, sentiment, QA, summarization, and more), rewrite each one's examples using several hand-written natural-language instruction templates ("Translate this sentence to French:", "Does the premise entail the hypothesis?"), and finetune a pretrained decoder-only language model on the resulting mixture.
At inference time the model responds to a new instruction in free text, including for task *types* it never saw a single example of during finetuning.

Introduced in [[Paper — Finetuned Language Models Are Zero-Shot Learners (2021)]] (FLAN), which holds out entire task clusters (e.g. all natural language inference datasets) during finetuning to measure genuine zero-shot generalization to an unseen task type, not just an unseen dataset within a familiar task.

## Why it works

Combines the pretrain-finetune paradigm (BERT, T5) with the prompting paradigm (GPT-3): like finetuning, it uses labeled data and gradient updates; like prompting, the interface at inference time is natural language, so a single checkpoint serves many tasks with no per-task head.
The FLAN paper's ablations pin down what actually drives the effect: more task *clusters* in finetuning reliably improves unseen-task performance and does not appear to saturate; more templates per dataset barely matters once enough datasets are present; and the benefit is scale-dependent - it *hurts* held-out task performance below roughly 8B parameters, and only starts helping at the ~68-137B range, plausibly because a small model's capacity is entirely consumed learning the finetuning mixture itself.

Removing the instructions and finetuning on bare input-output pairs (or a bare task/dataset name) instead of full natural-language instructions substantially degrades zero-shot performance on unseen tasks - confirming the gain is not merely from multi-task finetuning, but specifically from learning to *follow instructions*.

## Relations

- Introduced in [[Paper — Finetuned Language Models Are Zero-Shot Learners (2021)]].
- Complementary to, not a replacement for, few-shot prompting and prompt tuning: FLAN's own ablations show few-shot exemplars and prompt tuning both still improve further on top of an instruction-tuned checkpoint.
- Distinct from the RLHF-based sense of "instruction-tuned model" used loosely elsewhere in this wiki (e.g. [[Paper — Training LMs to Follow Instructions (2022)]]): this method uses only supervised multi-task finetuning on existing labeled datasets, no preference data and no RL.

## Up

- [[Cluster — Foundational canon]]
