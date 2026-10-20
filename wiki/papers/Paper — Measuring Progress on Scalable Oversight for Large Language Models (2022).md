---
type: paper
status: mature
updated: 2026-10-19
summary: "Turns scalable oversight into an experimental paradigm with today's models - 'sandwiching', where a model is more capable than non-expert humans but less capable than experts, and non-experts must get reliable answers out of it without expert help - and runs a proof of concept: humans chatting with an unreliable 52B assistant beat both the model alone and their own unaided accuracy on MMLU and time-limited QuALITY, by up to 36 points over unaided."
cluster: ["Scalable oversight"]
sources: ["raw/papers/scalable-oversight/Paper — Measuring Progress on Scalable Oversight for Large Language Models (2022).pdf"]
tags: [scalable-oversight, sandwiching, human-ai-teams, evaluation]
aliases: ["arxiv:2211.03540", "Bowman et al. 2022", "sandwiching"]
---

# Paper — Measuring Progress on Scalable Oversight for Large Language Models (2022)

Commonly known as the **sandwiching** paper.

## Metadata

arXiv:2211.03540 (v2, Nov 2022).
Samuel R. Bowman, Jeeyoon Hyun, and Ethan Perez as core contributors, with Surge AI co-authors Edwin Chen, Craig Pettit, and Scott Heiner (who recruited and managed the participants), Kamilė Lukošiūtė, a large Anthropic team, and Ben Mann and Jared Kaplan overseeing the project.

## Impact

Scalable oversight - supervising systems that outperform us at the task - is hard to study because we don't yet have systems that broadly outperform us.
Debate, amplification, and recursive reward modeling had been proposed but barely tested.
This paper supplies a way to test them now, based on a proposal by Ajeya Cotra called **sandwiching**: pick tasks where today's model is already better than ordinary people but worse than experts, give non-experts the job of getting reliable answers out of it, and use the experts only at the end to grade how well they did.
The non-experts are then in the position we will be in with future models - a capable but untrustworthy assistant and no easy way to verify it - except that here someone can check the answers afterward.

## Problem it solved

Scalable-oversight research had plenty of proposals and almost no empirical work, partly because nobody had shown that existing tasks and models actually satisfy the conditions an oversight experiment needs: humans must perform poorly alone, and the model must have knowledge they can extract.

## Core idea

A sandwiching experiment has three parties.
**Experts** have the skills to oversee the task and serve only as the final reference.
**The model** has most of the knowledge or skill needed but isn't reliably aligned to apply it - the paper's operational definition is a model that is capable (a small intervention like few-shot prompting or light fine-tuning makes it perform well) yet performs poorly under naive zero-shot prompting.
**Non-expert participants** understand the task but lack key knowledge, and must use some oversight technique with the model to perform reliably and build justified confidence that they are doing so, without any expert input or outside materials.
An inner loop has non-experts try to align the model; an outer loop repeatedly revises the technique and reruns the inner loop, ending in a verdict on whether any technique suffices.
The agenda's goal is a technique that succeeds in the inner loop on the first attempt with no expert feedback - which would be evidence it could handle systems that outperform even the experts.
The proof of concept relaxes two things: participants can only chat with the model rather than fine-tune it, and existing dataset labels stand in for the experts.

## How it's built

Questions come from MMLU (specialized exam questions) and QuALITY (reading comprehension on stories of about 5,000 words, made hard by a 5-minute time limit so participants can't read the whole passage).
The model is a 52B-parameter assistant fine-tuned with RLHF for helpfulness only.
Participants were contractors - five per task, of whom one per task was excluded for outperforming the model on their own, which would violate the paradigm's assumption - who answered 100 questions with the model and, separately, 100 without it, with the correct answer revealed after each question.

## Results

The best model setting beat unassisted individuals (66% vs 57% on MMLU, 67% vs 49% on QuALITY), and model-assisted individuals beat the model by similar margins (75% vs 66%, 77% vs 67%).
Pooled by confidence-weighted vote, model-assisted humans reached 78% on MMLU and 86% on QuALITY - up to 36 points above their unassisted pooled accuracy - though still short of the best published untimed human-team accuracy (94% on QuALITY) and the hypothesized expert-committee level on MMLU (90%).
On their most confident answers, assisted participants reached 88% accuracy on QuALITY and 81% on MMLU.
Participants learned strategies that look like hand-run oversight techniques: trusting the model's facts but not its long reasoning or arithmetic, asking each answer choice as a separate true-false question with a conversation reset in between, demanding quotes from the passage to fact-check, and cross-examining with several strategies per question.

## Why it endures

Sandwiching became a widely used framing for testing scalable-oversight techniques empirically, and it makes a methodological point the rest of [[Cluster — Scalable oversight]] relies on: techniques like debate deliberately make the model argue for wrong answers sometimes, which makes them a natural fit for sandwiching, because even a well-behaved model then shows capable misaligned behavior for participants to overcome.

## Limitations

The authors list six: the relaxations (multiple choice, no fine-tuning) narrow what can be concluded; revealing answers after each question removes the hardest part of oversight - knowing when your method is reliable enough to trust; plain chat leaves knowledge on the table that incentives or interpretability might extract; excluding the two strongest participants creates a selection effect; participants sometimes gave confident wrong answers after accepting plausible false claims, so the protocol isn't safe for high-stakes use; and multiple-choice questions are common on the web, so the model was partly trained for this kind of task already.
The paper calls its own result a baseline - a trivial oversight strategy - not a technique expected to scale.

## Relations

- Makes [[Concept — Scalable oversight]], as framed in [[Paper — Concrete Problems in AI Safety (2016)]], empirically testable with current models.
- Names [[Paper — AI Safety via Debate (2018)]] and [[Paper — Supervising Strong Learners by Amplifying Weak Experts (2018)]] among the techniques sandwiching is designed to test; [[Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)]] later runs debate in a closely related non-expert-judge setup on QuALITY.
- A human-centered counterpart to [[Paper — Weak-to-Strong Generalization (2023)]], whose setup its authors say resembles sandwiching, with a weak model in place of non-expert humans and a focus on generalizing beyond supervision rather than improving it.
- Uses a helpful-only assistant trained with the RLHF method of [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]], and notes its poor multiple-choice calibration is consistent with prior findings that RLHF degrades calibration.
- [[Paper — Towards Understanding Sycophancy in Language Models (2023)]] mimics its sandwiching setting - crowd-workers without internet access judging convincing falsehoods against corrections - and finds non-expert judgment degrades on harder misconceptions.
- Its paradigm is collected as [[Method — Sandwiching]].

## Up

- [[Cluster — Scalable oversight]]
