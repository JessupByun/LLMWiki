---
type: paper
status: mature
updated: 2026-10-20
summary: "Tests debate as scalable oversight with real LLMs: on hard reading-comprehension questions where expert debaters can see the story and non-expert judges can't, debate lifts non-expert accuracy to 76% for LLM judges and 88% for human judges (versus 48% and 60% with no help), beats a single-advisor 'consultancy' baseline, and - crucially - making debaters more persuasive, using only judge approval as the optimization signal (inference-time best-of-N and critique-and-refinement, no training), makes judges more accurate, while more persuasive consultants make them less accurate."
cluster: ["Scalable oversight"]
sources: ["raw/papers/scalable-oversight/Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024).pdf"]
tags: [scalable-oversight, debate, persuasion, human-evaluation]
aliases: ["arxiv:2402.06782", "Khan et al. 2024", "empirical debate"]
---

# Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)

## Metadata

arXiv:2402.06782 (v4, Jul 2024); published at ICML 2024.
Akbir Khan, John Hughes, and Dan Valentine as equal contributors, with Laura Ruis, Kshitij Sachan, Ansh Radhakrishnan, Edward Grefenstette, Samuel R. Bowman, Tim Rocktäschel, and Ethan Perez.
UCL, Speechmatics, MATS, Anthropic, Redwood Research, and FAR AI.

## Impact

[[Paper — AI Safety via Debate (2018)]] rests on one claim: in an adversarial debate, it is harder to lie than to refute a lie, so a weak judge can identify the truth.
Earlier human-debater studies were mixed - one- and two-turn debates did not help judges, while Michael et al. (2023) found longer human debates did - and Michael et al.'s attempt with LLM debaters found no positive result.
This paper gives encouraging positive evidence for debate with LLM debaters.
More importantly for whether debate could scale, it finds the effect gets *stronger* as debaters get more persuasive - even though persuasiveness is measured only by whether judges are convinced, with no access to the right answer.
That is the property debate needs to work on superhuman models: optimizing debaters to win should make outcomes more truthful, not less.

## Problem it solved

Scalable-oversight methods are meant to work when no ground truth is available, yet existing evaluations of them measured performance against ground-truth labels and had no way to ask how a protocol behaves as the models in it get stronger.
And whether debate helps at all with real language-model debaters was unresolved.

## Core idea

The setup simulates stronger versus weaker models with **information asymmetry**: on QuALITY reading-comprehension questions about science-fiction stories (about 7,000 tokens), expert debaters can read the story and non-expert judges cannot, but are otherwise as capable.
Debaters can quote the text through a tool that marks quotes as verified only if they match the story exactly, and judges are told to trust only verified quotes.
In **debate**, two experts argue for opposing answers over three rounds and the judge picks one; in **interactive debate**, the judge can also ask questions between rounds; in the **consultancy** baseline, a single expert argues for one assigned answer - correct half the time - while the judge questions it.
To study scaling without labels, the paper measures debaters' **persuasiveness** by judge approval alone - win rates in a tournament, converted to Elo ratings - and makes debaters more or less persuasive with inference-time optimization (best-of-N sampling against a preference model, and critique-and-refinement).

## How it's built

Debaters and consultants include GPT-4 Turbo, GPT-3.5 Turbo, Claude 2.1, Claude 1.3, and Mixtral 8x7B; LLM judges include GPT-4 Turbo, Claude 2.1, GPT-3.5 Turbo, and Mixtral, run on both orderings to cancel position bias, with word limits to curb verbosity bias.
Human judges were 21 annotators (filtered from 30 without using ground-truth labels), producing 953 judgments in the test phase over several weeks.
A naive judge sees only the question; an expert judge sees the full story as a ceiling.

## Results

**Debate helps both kinds of judges.** With the most persuasive (GPT-4 Turbo, best-of-16) debaters, non-expert accuracy reaches 76% for LLM judges and 88% for human judges, versus naive baselines of 48% and 60%; consultancy reaches 54% and 78%.
Each question offers only two answers (the correct one and the best distractor), so 50% is chance and the LLM judge's naive baseline is no better than guessing.
With the strongest LLM judge, debate closes part of the gap to the expert judge (75.6% vs 92.5%).
**Telling the truth is an advantage that grows with persuasiveness.** Across a tournament of 20 debater variants, every model is more persuasive when assigned the correct answer, and the margin widens for more persuasive debaters - and self-play debates between more persuasive debaters yield more accurate judges, including judges whose preferences were never optimized against.
**Persuasive consultants mislead.** Optimizing consultants for persuasiveness makes the incorrect ones relatively more convincing, so consultancy accuracy *falls* as consultants improve.
**Humans do better than LLM judges, and are well calibrated.** Debate beats consultancy for humans in both static and interactive settings; rejecting static-debate judgments below 75% confidence keeps 65% of questions at 94% accuracy, and in debate human judges are under- rather than over-confident, and better calibrated than in consultancy.
**Interaction didn't help.** Letting human judges question debaters gave identical accuracy to static debate.
**Debaters are the bottleneck.** In human-judge errors on debate, the most common cause was weak arguments from the correct debater, often poor quote choices.

## Why it endures

It turns debate from a theoretical proposal into a measured effect, and introduces a way to evaluate oversight protocols as models scale without labels - persuasiveness by judge approval - which is what an oversight method for superhuman models would have to rely on.
Its debate-versus-consultancy contrast is a concrete warning about non-adversarial oversight: a single persuasive advisor optimized for approval can make its overseer worse.

## Limitations

"Stronger" here means only having access to the text; future models may differ from their overseers in reasoning ability instead.
All debaters are RLHF-trained models with a propensity for honesty, so it is unclear whether debate works against deceptive models.
Everything depends on verifiable evidence - the quote tool - without which a debater for the wrong answer could simply invent a story; domains with uncitable knowledge or vast evidence remain untested.
Unlike the original proposal, where only one disputed sub-component needs judging, judges here read full transcripts; the authors present this as showing even the simpler protocol is useful, but it leaves the stricter scheme untested.

## Tension / update

[[Paper — On Scalable Oversight with Weak LLMs Judging Strong LLMs (2024)]] tests this paper's scaling result across nine tasks with a similar Elo method and finds it weaker.
Every debater is still more persuasive when arguing the truth, but that advantage does not clearly widen as debaters get stronger.
Judge accuracy rises less consistently and over a narrower range, mainly on information-asymmetric tasks, and best-of-4 sampling raises persuasiveness without significantly raising accuracy.
Its authors call their findings roughly in agreement with this paper's but conclude the finding may not generalize robustly beyond QuALITY.
Both studies are inference-only and use different models and best-of-N implementations.

## Relations

- The empirical test of [[Paper — AI Safety via Debate (2018)]]'s central claim that lying is harder than refuting a lie, using a protocol inspired by but operationalized differently from the original.
- Uses the information-asymmetric QuALITY setup and consultancy baseline of [[Paper — Debate Helps Supervise Unreliable Experts (2023)]], which in the wiki's reading descends from [[Paper — Measuring Progress on Scalable Oversight for Large Language Models (2022)]]'s time-limited QuALITY sandwiching task, with debate in place of plain chat.
- Contrasts itself with [[Paper — Weak-to-Strong Generalization (2023)]], which pursues weak supervision of strong models through inductive biases rather than adversarial argument.
- Cites [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] for the deceptive-model case its honest RLHF debaters don't cover, and [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] in its impact statement as a warning that misapplied supervision can make malicious behavior harder to detect - the same average-case versus worst-case gap that separates [[Cluster — Scalable oversight]] from [[Cluster — Scheming & AI control]].
- Its best-of-N optimization against judge approval is a controlled instance of optimizing against a learned evaluator, the setting where [[Concept — Reward hacking]] usually appears - here, for debate, it improves truthfulness instead.
- An information-asymmetric variant of [[Method — Sandwiching]].
- [[Paper — Training Language Models to Win Debates with Self-Play Improves Judge Accuracy (2024)]] extends its result from inference-time optimization to training, finding judge accuracy still rises as trained debaters improve.
- [[Paper — Language Models Learn to Mislead Humans via RLHF (2024)]] trains a reward model on its careful human judgments of QuALITY arguments, and finds RLHF against such signals makes models more misleading to time-limited humans.
- [[Paper — On Scalable Oversight with Weak LLMs Judging Strong LLMs (2024)]] extends its setup to nine tasks and finds the link between persuasiveness and judge accuracy weaker and less consistent outside QuALITY (see Tension / update).
- Part of [[Method — Debate]]; evidence for [[Question — Does debate's advantage come from refutation or just from hearing both sides]].

## Up

- [[Cluster — Scalable oversight]]
