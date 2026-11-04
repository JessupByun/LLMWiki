---
type: paper
status: mature
updated: 2026-11-04
summary: "Finetunes a 137B-parameter language model on 60+ NLP datasets rewritten as natural-language instructions, and shows the resulting model (FLAN) beats zero-shot GPT-3 on 20 of 25 datasets drawn from task types it was never trained on."
cluster: ["Post-training alignment", "Foundational canon"]
sources: ["raw/papers/post-training-alignment/Paper — Finetuned Language Models Are Zero-Shot Learners (2021).pdf"]
tags: [instruction-tuning, zero-shot, llm, post-training, sft]
aliases: ["arxiv:2109.01652", "Wei et al. 2021", "FLAN"]
---

# Paper — Finetuned Language Models Are Zero-Shot Learners (2021)

Commonly known as **FLAN**.

## Metadata

- **arXiv:** 2109.01652 (v5, 2022-02-08)
- **Venue:** ICLR 2022; printed on the paper itself.
- **Authors:** Jason Wei, Maarten Bosma, Vincent Y. Zhao, Kelvin Guu, Adams Wei Yu, Brian Lester, Nan Du, Andrew M. Dai, Quoc V. Le (Google Research).

## Impact

FLAN named and demonstrated **instruction tuning**: supervised finetuning of a pretrained language model on many existing NLP datasets, each rephrased as natural-language instructions, so that the model follows instructions for task types it never saw during finetuning.
Zero-shot FLAN (137B) beats zero-shot GPT-3 (175B) on 20 of 25 evaluated datasets, and beats *few-shot* GPT-3 by a large margin on ANLI, RTE, BoolQ, AI2-ARC, OpenbookQA, and StoryCloze.
This is the supervised "make the model follow an instruction" stage that later chat-model post-training pipelines start from, before any preference learning is layered on top, and its held-out-task-cluster evaluation is the paper's tool for separating genuine cross-task generalization from familiarity with a task format.

## Problem it solved

GPT-3-scale language models are strong few-shot learners but much weaker zero-shot: GPT-3's zero-shot results on reading comprehension, question answering, and natural language inference trail its few-shot results badly.
The paper's diagnosis is a format mismatch - without exemplars, a raw pretrained model is asked to respond to prompts that look nothing like the text it was pretrained to continue.
NLI is the clearest case: as GPT-3's own authors noted, NLI examples are unlikely to appear naturally in web text and read awkwardly as a sentence continuation.

## Core idea

Teach the model the general skill of responding to an instruction, by finetuning on a large mixture of tasks that are all phrased as instructions, and then test it on a kind of task it has never been finetuned on.
To make "never seen" mean something, datasets are grouped into task clusters (NLI, reading comprehension, closed-book QA, translation, commonsense, coreference, and so on), and each evaluation cluster is held out entirely from finetuning.
For example, to test NLI, the model is instruction-tuned on translation, sentiment, commonsense reasoning, and the other clusters - never on a single NLI example - and is then asked "Does <premise> mean that <hypothesis>?" cold.
See [[Method — Instruction Tuning]] for the reusable mechanism.

## How it's built

- **Base model:** LaMDA-PT, a 137B-parameter dense decoder-only Transformer pretrained on 2.49T tokens of web documents, dialog, and Wikipedia, with language-model pretraining only (no dialog finetuning).
- **Data:** 62 publicly available text datasets grouped into 12 task clusters.
- **Templates:** ten hand-written natural-language instruction templates per dataset, including up to three that "turn the task around" (for sentiment classification, a template asking the model to write a movie review).
- **Mixing:** at most 30k training examples per dataset, with examples-proportional mixing capped at a mixing rate of 3k.
- **Training:** 30k gradient steps, batch size 8,192 tokens, Adafactor at learning rate 3e-5, input/target lengths 1024/256 with packing - about 60 hours on a 128-core TPUv3, under 2% of the pretraining step count.
- **Evaluation:** for each dataset, the mean over all of its templates, so a result does not depend on one lucky prompt.

## Results (highlights)

- **On the three clusters where it helped most** (Figure 1), zero-shot FLAN scores 56.2 on NLI, 77.4 on reading comprehension, and 56.6 on closed-book QA, versus 42.9 / 63.7 / 49.8 for zero-shot GPT-3 and 53.2 / 72.6 / 55.7 for few-shot GPT-3.
- **Translation:** FLAN beats zero-shot GPT-3 on all six WMT evaluations but underperforms few-shot GPT-3 in most of them, and is weaker translating out of English than into it.
- **More task clusters help steadily:** with NLI, closed-book QA, and commonsense held out, average held-out performance rises as one to seven finetuning clusters are added, with no sign of saturating.
- **Scale decides whether it helps at all:** at 68B and 137B, instruction tuning substantially improves held-out tasks, but at 8B and below it *hurts* them, which the authors tentatively attribute to the finetuning mixture filling a small model's entire capacity.
- **Instructions are the active ingredient:** averaged over four held-out clusters, finetuning with no templates scores 37.3 and finetuning with only a dataset name scores 46.6-47.0, against 55.2 for FLAN's natural-language instructions - so the gain is not just multi-task finetuning.
- **Complementary to prompting:** few-shot exemplars added on top of FLAN improve every task cluster and reduce variance across templates, and prompt tuning works better on FLAN than on the untuned base model.

## Why it endures

Instruction tuning survives as a named, separately cited stage because the ablations isolate it: it is the instructions, not multi-task data alone, that buy zero-shot generalization, and the benefit only appears at scale.
The RL-based route to the same goal, [[Paper — Training LMs to Follow Instructions (2022)]] (InstructGPT), also begins with a supervised finetuning stage before adding preference learning, and FLAN's own related-work section cites it as the comparable effort at a similar model scale.

## Limitations

- **Instructions don't help when the task already is language modeling.** On seven commonsense and coreference tasks formatted as sentence completions, FLAN beats the untuned LaMDA-PT on only three, which the authors read as instruction tuning being useless where instructions are redundant.
- **Cluster assignment is subjective**, and the instructions studied are short, typically a single sentence.
- **Possible contamination:** individual test examples may appear in the web pretraining data, though the post-hoc overlap analysis (Appendix C) finds no evidence that it substantially changed the results.
- **Cost and ethics:** the 137B model is costly to serve, biases in the labeled finetuning datasets can carry into zero-shot use, and lowering the data and expertise needed to use a model raises both its benefits and its risks.

## Relations

- Opens [[Method — Instruction Tuning]].
- Builds on the few-shot prompting setup of [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3), the main baseline it compares against.
- Contrasted with the RLHF route in [[Paper — Training LMs to Follow Instructions (2022)]] (InstructGPT): FLAN uses only supervised multi-task finetuning on existing labeled datasets, with no preference comparisons and no RL.
- Concurrent with T0 (Sanh et al. 2021, not yet ingested), which the paper cites as a similar setup at 11B parameters.

## Up

- [[Cluster — Post-training alignment]]
- [[Cluster — Foundational canon]]
