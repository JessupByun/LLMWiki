---
type: paper
status: mature
updated: 2026-10-20
summary: "Benchmarks debate and consultancy with equal or weaker LLM judges overseeing Gemini Pro 1.5 across nine tasks and about 5 million model calls: debate beats assigned-role consultancy on every task type and beats unaided judging when judges lack the source text, but gives small or no gains over unaided judging on closed math, logic, and knowledge questions; when the strong model picks its own side, a wrong debater misleads judges far less than a wrong consultant, and stronger debaters raise judge accuracy only modestly."
cluster: ["Scalable oversight"]
sources: ["raw/papers/scalable-oversight/Paper — On Scalable Oversight with Weak LLMs Judging Strong LLMs (2024).pdf"]
tags: [scalable-oversight, debate, consultancy, llm-as-judge, evaluation]
aliases: ["arxiv:2407.04622", "Kenton et al. 2024", "open debate", "open consultancy"]
---

# Paper — On Scalable Oversight with Weak LLMs Judging Strong LLMs (2024)

## Metadata

arXiv:2407.04622 (v2, 12 Jul 2024).
Zachary Kenton and Noah Y. Siegel (equal contribution), with János Kramár, Jonah Brown-Cohen, Samuel Albanie, Jannis Bulian, Rishabh Agarwal, David Lindner, Yunhao Tang, Noah D. Goodman, and Rohin Shah.
Google DeepMind.

## Impact

Earlier LLM debate results came from one kind of task: reading comprehension where the judge can't see the story and debaters quote it.
This paper asks whether debate still helps when the gap between judge and debaters is a gap in skill - math, logic, science knowledge, images - rather than hidden text, and runs the comparison at scale with weaker LLMs judging a stronger one.
Its answer is deliberately sober: debate reliably beats consultancy, but beating consultancy is easy, and outside the hidden-text setting debate barely beats the judge simply answering on its own.
It also adds a measurement the earlier work lacked - what happens when the strong model argues for the answer it actually believes, right or wrong - and finds debate is much less likely than consultancy to rubber-stamp the model's mistakes.

## Problem it solved

Debate between LLMs had mostly been studied on QuALITY, where the judge is limited only by being shown short verified quotes.
The authors argue that this kind of information asymmetry cannot test the advantages debate theory predicts, since future gaps between humans and superhuman AI will span reasoning, creativity, and knowledge, not just access to a document.
[[Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)]] reported only limited results on tasks without information asymmetry, and only with the judge being the same model as the debaters, which the authors say matters for self-improvement but less for scalable oversight.
Prior debate and consultancy experiments assigned debaters and consultants their answers, so it could not ask whether a weak judge's signal would amplify the strong model's own existing errors.

## Core idea

Take a binary-choice question and compare how often a weak judge picks the right answer under different kinds of help from a strong model.
In one of the paper's grade-school math items, the question is how long it takes to read 120 pages at 8 pages per 20 minutes, and the two options are Python programs, one of which computes the wrong quantity.
Under **QA without article**, the judge just answers; under **consultancy**, one strong model argues for an assigned option (correct half the time) while the judge questions it; under **debate**, two copies of the strong model argue opposite options over three rounds.
Here the judge sees everything the debaters see, so any benefit has to come from the debaters' superior reasoning rather than from hidden evidence.
The **open** variants then let the strong model's own beliefs in: the open consultant argues for whatever it would answer unaided, and in open debate one debater (the protagonist) takes that answer while the other takes the opposite.
The question that matters for training is what happens when the protagonist believes the wrong program is right: does the weak judge go along?

## How it's built

- **Nine tasks, 128 questions each.** Extractive QA with information asymmetry (QuALITY, BoolQ, and GPQA-extractive, where debaters see an expert explanation), closed QA with no asymmetry (MMLU, GSM8KQA, PrOntoQA, TruthfulQA, GPQA), and multimodal (MMMU).
  Multiple-choice datasets become binary by pairing the correct answer with a random wrong one; extractive tasks use a passage-verification tool as in Khan et al.
- **Models.** Gemini Pro 1.5 is the debater and consultant in the main experiments; judges are Gemma7B, GPT-3.5, Gemini Pro 1.0, and Gemini Pro 1.5, so judges are equal to or weaker than the agents.
- **Settings.** Three rounds, simultaneous turns, a 150-word limit per turn, private chain-of-thought for debaters, and best-of-4 sampling in which Pro 1.5 picks its most persuasive sample; judges are prompted zero-shot.
- **Open-role protocols** are computed by filtering the assigned-role runs to cases where the assignment matches the agent's own direct-QA answer, rather than by generating new data.
- **Persuasiveness.** Five debaters (four models plus Pro 1.5 with best-of-4) play 512 one-turn cross-play debates judged by Pro 1.5, giving Elo ratings that are then compared against judge accuracy, following Khan et al.

## Results

- **Debate beats assigned-role consultancy** on every task type, which the authors note had previously only been shown on QuALITY (though the paper's Appendix A says the multimodal difference is not significant, contradicting Sec. 4.1).
- **Extractive tasks:** judges are always most accurate with the article in hand (QA with article), and debate beats both consultancy and QA without article for every judge except Gemma7B, which does no better than answering unaided.
- **Closed tasks:** debate's edge over the judge answering alone depends on the judge.
  Gemma7B does better unaided; GPT-3.5 does better with debate (p = 0.037); for Pro 1.0 the difference is not significant (p = 0.1); and Pro 1.5, judging copies of itself, does worse with debate.
  The authors treat GPT-3.5 and Pro 1.0 as most indicative of scalable oversight and read them as showing small or no benefit.
- **Multimodal:** consultancy is significantly worse than the other protocols, and debate versus answering unaided is not significant (p = 0.11).
- **Ablations change little:** one versus three turns, best-of-4 versus best-of-1 for debaters, six-shot judge prompts, sequential versus simultaneous turns, and judging both answer orders all have no significant effect on accuracy; judge chain-of-thought tends to hurt or do nothing, which the authors suspect makes weak judges discount the stronger agents' reasoning.
- **Open roles:** the agent picks the correct answer in 88%, 84%, and 71% of extractive, closed, and multimodal questions.
  In open consultancy the judge is about equally convinced whether the consultant chose right or wrong, so a consultancy training signal risks amplifying the consultant's mistakes.
  In open debate the judge is much more accurate when the protagonist chose wrong than in open consultancy, at the cost of somewhat lower accuracy when the protagonist chose right.
- **Persuasiveness:** every debater is more persuasive when assigned the correct answer, but the advantage does not clearly widen as debaters get stronger; on extractive tasks stronger debaters raise Pro 1.5's judging accuracy, while on closed tasks debaters are less differentiated in both Elo and accuracy.
  Best-of-4 raises Elo without significantly raising accuracy, which the authors call a mildly negative sign for debate as a training protocol since best-of-N is meant as a proxy for training.
- **Judge failures:** in 24 transcripts where Pro 1.5 judged wrongly, 11 of 12 PrOntoQA failures came from the judge's own logic errors, often after the honest debater had correctly identified the flaw; half of the 12 TruthfulQA failures were ambiguous questions that the dishonest debater reinterpreted in its favor.
- Gemini judges rated GPT-3.5 debaters lower than expected, which the authors speculate is self-preference for their own model family.

## Why it endures

It extends the empirical case for debate from information gaps to capability gaps, and it sets the bar debate has to clear: the authors say strong positive evidence would be debate beating the unaided-judge baseline, or even matching the judge reading the article, and outside the extractive tasks they see neither; debate never matches the judge reading the article.
The open-role protocols turn an abstract worry about oversight - that a weak judge will reinforce whatever the strong model already believes - into a measurement, and debate's advantage there is what makes it look more promising than single-advisor oversight.
[[Paper — Training Language Models to Win Debates with Self-Play Improves Judge Accuracy (2024)]] cites its muted results outside reading comprehension when calling debate's record in the literature mixed.

## Limitations

The authors' own:
- Everything is inference-only, with prompted models; what matters is the equilibria reached by training models in these games, which win rates and correct-answer advantages say little about.
- Consultancy is likely a weak baseline because the consultant argues for the wrong answer half the time.
- Debaters and consultants were RLHF-trained for honesty, which may handicap the dishonest side; they suggest repeating the study with helpful-only models.
- No humans are involved, the models are far from superhuman, and it remains a key uncertainty whether humans can follow and judge superhuman arguments.
- They suspect judges' supervised finetuning and RLHF favor direct question answering over judging a debate, which they think is more out of distribution, and propose finetuning judges on debates.
- On QuALITY specifically, they note earlier evidence that extra rounds don't help and that quote verification, which may be unrealistic for human oversight, does most of the work.

A caveat from the wiki rather than the authors: the open-role numbers come from relabeling assigned-role runs, so no agent actually chose its side in-context, and the questions on which the agent "chose" wrong are harder than average - a selection effect the authors raise when comparing open and assigned consultancy, and one that applies to every split by whether the agent was right.
All judges are LLMs, and the main experiments use a single debater model judged largely by models from the same family.

## Tension / update

Tempers the scaling result of [[Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)]].
There, across 20 debater variants on QuALITY, the truthful side's persuasiveness advantage widened as debaters improved and judge accuracy rose with debater strength.
Here, with a similar Elo method across nine tasks, every debater still has an advantage when arguing the truth, but it does not clearly widen with strength, judge accuracy rises less consistently and over a narrower range - mainly on extractive tasks - and best-of-4 sampling raises persuasiveness without significantly raising accuracy.
The authors call their findings roughly in agreement with Khan et al. but conclude the finding may not generalize robustly beyond QuALITY.
The two studies differ in models, best-of-N implementation, and judges, and both are inference-only; [[Paper — Training Language Models to Win Debates with Self-Play Improves Judge Accuracy (2024)]] concurrently finds the positive trend survives training, but again only on reading comprehension.
It also bears on the mechanism proposed by [[Paper — Debate Helps Supervise Unreliable Experts (2023)]]: cutting debates from three turns to one has no significant effect on judge accuracy, which sits poorly with Michael et al.'s hypothesis that claims gain reliability by surviving an opposing expert's refutation - though with LLM judges and fixed-length debates rather than skilled humans in judge-ended ones.

## Relations

- Extends [[Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)]], whose setup and prompts it adapts, from QuALITY to nine tasks, adding open-role protocols and more ablations.
- Uses the consultancy baseline of [[Paper — Debate Helps Supervise Unreliable Experts (2023)]], framed as a worst-case variant of [[Method — Reinforcement learning from human feedback (RLHF)]] in which feedback can't tell right from wrong answers; it also cites that paper's finding that GPT-4 debaters were ineffective.
- An empirical test of [[Paper — AI Safety via Debate (2018)]] at inference time, which the authors stress is only a proxy for debate as a training protocol.
- Runs a simplified version of [[Method — Sandwiching]] from [[Paper — Measuring Progress on Scalable Oversight for Large Language Models (2022)]], with ground-truth labels in place of experts and LLMs in place of non-experts; it says the earlier paper's protocol resembles its open consultancy.
- Lists [[Paper — Weak-to-Strong Generalization (2023)]] among the oversight proposals sandwiching could evaluate, and shares its use of weaker models as stand-ins for human overseers - the shared framing is the wiki's (see [[Concept — Scalable oversight]]).
- Motivates scalable oversight with [[Paper — Sycophancy to Subterfuge - Investigating Reward-Tampering in Large Language Models (2024)]] and with scheming as described in [[Paper — Scheming AIs - Will AIs fake alignment during training in order to get power? (2023)]], [[Paper — Risks from Learned Optimization in Advanced Machine Learning Systems (2019)]], and [[Paper — The Alignment Problem from a Deep Learning Perspective (2022)]].
- Its open-consultancy finding - a weak judge equally convinced by a right or wrong single advisor - is the inference-time version of the failure [[Paper — Language Models Learn to Mislead Humans via RLHF (2024)]] measures after training with humans; the connection is the wiki's.
- [[Paper — Training Language Models to Win Debates with Self-Play Improves Judge Accuracy (2024)]] cites it as concurrent work with muted results outside reading comprehension and does what it calls for, training debaters by self-play.
- Its judges get worse or no better with [[Method — Chain-of-Thought Prompting]], an unusual case of the method not helping.
- Shares its focus on capability rather than information gaps with [[Paper — Prover-Verifier Games Improve Legibility of LLM Outputs (2024)]], which trains rather than prompts; the connection is the wiki's.
- Part of [[Method — Debate]]; evidence for [[Question — Does debate's advantage come from refutation or just from hearing both sides]].

## Up

- [[Cluster — Scalable oversight]]
