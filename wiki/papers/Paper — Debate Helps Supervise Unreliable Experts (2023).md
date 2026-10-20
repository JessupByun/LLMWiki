---
type: paper
status: mature
updated: 2026-10-20
summary: "Shows, across 413 human-judged sessions (250 with human experts) on hard reading-comprehension questions, where judges never see the story, that debate between two skilled human experts - one assigned the right answer, one the wrong - gets judges to 84% accuracy versus 74% when a single expert argues one side that is wrong half the time ('consultancy'), in shorter sessions; with GPT-4 as the expert, debate and consultancy show no significant difference (78% vs 80%)."
cluster: ["Scalable oversight"]
sources: ["raw/papers/scalable-oversight/Paper — Debate Helps Supervise Unreliable Experts (2023).pdf"]
tags: [scalable-oversight, debate, consultancy, human-evaluation, sandwiching]
aliases: ["arxiv:2311.08702", "Michael et al. 2023", "NYU human debate", "consultancy baseline"]
---

# Paper — Debate Helps Supervise Unreliable Experts (2023)

## Metadata

arXiv:2311.08702 (v1, 15 Nov 2023).
Julian Michael, Salsabila Mahdi, and David Rein (equal contribution), with Jackson Petty, Julien Dirani, Vishakh Padmakumar, and Samuel R. Bowman.
New York University, Cohere, and Anthropic.

## Impact

Before this paper, human debate experiments on reading comprehension had found that one- and two-turn debates didn't help judges, a setback for [[Paper — AI Safety via Debate (2018)]].
This paper runs long, interactive debates between competitive human debaters in front of judges who can't read the story, and finds debate beats a single unreliable expert by 10 points while using less of the judge's time.
It also introduces **consultancy** - one expert who argues for the wrong answer half the time - as a stand-in for the worst case of RLHF, where a model learns to sound right to an evaluator who can't check.
The authors say it demonstrates for the first time that debate helps judges find the truth on a realistic task.

## Problem it solved

Debate's central bet is that an honest expert can expose a dishonest one in front of a judge who couldn't find the truth alone, but the evidence so far was negative or toy-scale.
Earlier QuALITY debates by Parrish et al. capped debates at one or two turns and gave judges 90 seconds with the passage, which the authors suspect let judges skim instead of relying on the debaters.
OpenAI's human debates on counterintuitive physics questions had surfaced the *obfuscated arguments* problem: a dishonest debater builds a long argument whose flawed step neither side can pinpoint.
What was missing was a protocol where the judge truly depends on the experts, debates can run as long as the judge needs, and a non-adversarial baseline isolates what the second, opposing expert adds.

## Core idea

The judge gets a question about a science-fiction story they've never read, with two answers, one correct.
Two experts who have read the story argue for opposite answers; each turn they can write up to 750 characters, of which up to 250 can be verbatim quotes, highlighted so the judge knows they are real.
After each round the judge states probabilities for the two answers and chooses whether to continue, paying a small score penalty for every extra round.
In consultancy, the judge faces one expert defending a randomly chosen answer and must interrogate it alone.
The difference shows up in the paper's own feedback sessions: a dishonest consultant asked for evidence of the other answer can simply say the story contains none, while in debate the opposing expert would quote it.

## How it's built

- **Questions:** the Project Gutenberg subset of QuALITY-hard, further restricted to questions needing more than one or two sentences of context, using the correct answer and the most popular distractor; stories average 27.7k characters (6.3k tokens).
- **Participants:** 19 NYU-affiliated people serve as both debaters and judges, including 12 members of the NYU debate team and 6 members of the research team; each person judges only one question per story.
- **Incentives:** judges are scored by a logarithmic proper scoring rule on their final probability, minus 0.05 per extra round; debaters by the log probability the judge assigns their answer; participants are ranked on leaderboards and see the correct answer after each debate.
- **Conditions:** human debate, human consultancy, AI debate, and AI consultancy, with GPT-4 (gpt-4-32k) as the AI expert, prompted with few-shot examples from the human debates; all judges are human.
  Consultants get double the character and quote limits so information per round matches debate.
- Data were collected from February to August 2023; consultancy was only added in June and AI experts in July.

## Results

- **Debate beats consultancy with human experts:** 84% judge accuracy versus 74% (154 human debates vs 96 human consultancies; p = 0.04), and still significant (p = 0.0487) after controlling for judge identity in a mixed-effects model.
  Human debate falls 8.5 points short of an estimated 92.5% ceiling set by flawed or ambiguous questions, reaches 88% when judges are at least 95% confident, and the top two judges were right on all 36 human debates they judged.
- **Debate is more efficient:** debates are 68% of the length of consultancies and use 61% as much quoted evidence; human debates end in 2.7 rounds on average versus about 4 for the other settings, and convey 0.09 bits per round, the most of any condition.
  Human debates reveal only 3.5% of the story on average.
- **Skilled liars fool a lone judge:** dishonest human consultants deceive judges 40% of the time (60% accuracy versus 88% with honest consultants), and 52% of consultancy errors involve the judge failing to extract key evidence from a dishonest consultant.
- **GPT-4 is not yet good enough to test debate:** AI debate (78%) and AI consultancy (80%) are not significantly different from each other or from either human condition.
  GPT-4 provided less than half the quote density of humans and repeated itself (35% of quoted material in AI debate was redundant, versus 3% for humans), and judges were actually more accurate when the GPT-4 consultant was lying (87%) than honest (74%), since its lies were easy to catch.
- **The trend the authors highlight:** moving from weak AI experts to skilled human ones raises debate accuracy and lowers consultancy accuracy, which is what debate needs as experts get stronger.
- **Debate errors look fixable:** in 46% of human-debate mistakes the honest debater missed usable evidence, and in 33% the judge ended too early, either after one round (25%) or right after swinging their vote (8%).
- **Calibration:** judges are best calibrated in human debate (final expected calibration error 0.11), though all settings are overconfident in the 90-100% range.

## Why it endures

It gave debate a clear positive result with skilled debaters on a realistic task after earlier null results, and its consultancy baseline gives later debate experiments a fixed point of comparison for what an opposing expert adds over a single persuader.
The worst-case-RLHF framing connects debate to a live problem rather than a future one: a single model optimized to convince an evaluator who can't check is the setup in which persuasion and truth come apart.
Its closing agenda - collect hard questions, train an AI judge on calibrated human judgments, then train AI debaters by self-play with continual human supervision - together with its design recommendations (probabilistic judgments, unbounded length, judge interaction, free text, grounded evidence) reads as a plan for the LLM debate work that followed.

## Limitations

The authors' own:
- Only reading-comprehension questions over short fiction; debate may behave differently on harder questions, much larger bodies of knowledge, or politically and ethically charged topics.
- Judges start with no strong opinion, so robustness to strong priors and cognitive biases such as confirmation bias is untested.
- The expertise gap is only privileged access to a text, not larger knowledge gaps or differences in reasoning and persuasion skill.
- Debate may not extract all of the debaters' knowledge: some questions may produce arguments too complex for the judge, ending in stalemate, and graceful failure depends on judge calibration, which remains hard at high confidence.
- Dishonest debaters may have strong strategies the human debaters never found, so high accuracy may partly reflect their mistakes; testing this needs more optimization pressure, likely from AI debaters.
- Data collection was not controlled across conditions - consultancy and AI experts came later, and the participant pool changed - though robustness checks support the main result.
- Judges received feedback after every debate, which would not necessarily be available with AI self-play or deployment.
- GPT-4's weakness as a dishonest consultant may partly be an artifact of RLHF helpfulness and honesty training, and its prompt may not have been optimized enough.

A caveat from the wiki rather than the authors: the headline significance is modest (p = 0.04 with 154 human debates and 96 human consultancies), and the "more skilled experts help debate, hurt consultancy" trend rests on two points - GPT-4 and humans - collected in different periods with different participant mixes.
Six of the 19 participants were members of the research team.

## Tension / update

The authors hypothesize that debate's advantage comes from the opposing expert: a claim gains reliability simply by going unrefuted, and they recommend unbounded debate length so debaters always fear another round of refutation.
Later LLM-judge studies in this wiki find refutation contributes little.
[[Paper — Training Language Models to Win Debates with Self-Play Improves Judge Accuracy (2024)]] finds a "double consultancy" that shows both sides with no interaction reaches 75% against debate's 77% (averaged across training checkpoints), so most of the gain comes from seeing both answers argued, and reports judges are insensitive to second-speech refutations.
[[Paper — On Scalable Oversight with Weak LLMs Judging Strong LLMs (2024)]] finds cutting debates from three turns to one has no significant effect on judge accuracy.
Those studies use LLM judges and fixed short debates rather than skilled humans in judge-controlled, interactive debates, so whether refutation matters once debaters and judges are strong is still open.

## Relations

- Builds on [[Paper — AI Safety via Debate (2018)]] with three stated departures: debaters are assigned answers, the judge interacts between turns, and judges are not required to rule against a debater caught lying.
- Calls itself a variant of the [[Method — Sandwiching]] design of [[Paper — Measuring Progress on Scalable Oversight for Large Language Models (2022)]], with human debaters standing in for future AI experts, and argues debate is more efficient than that paper's open-ended dialogue.
- Frames consultancy as a worst-case version of [[Method — Reinforcement learning from human feedback (RLHF)]], citing [[Paper — Deep RL from Human Preferences (2017)]] and [[Paper — Learning to Summarize from Human Feedback (2020)]], with sycophancy from [[Paper — Discovering Language Model Behaviors with Model-Written Evaluations (2022)]] and [[Paper — Towards Understanding Sycophancy in Language Models (2023)]] as early evidence (see [[Concept — Sycophancy]]).
- [[Paper — Language Models Learn to Mislead Humans via RLHF (2024)]] fine-tunes its initial QuALITY policy to imitate this paper's answers and arguments, then shows RLHF makes it mislead time-limited humans - the consultancy worst case occurring under ordinary training.
- [[Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)]] adopts its information-asymmetric QuALITY setup and consultancy baseline and gets the positive LLM-debater result this paper did not.
- [[Paper — Training Language Models to Win Debates with Self-Play Improves Judge Accuracy (2024)]] shares authors Julian Michael and David Rein, follows its setup, fine-tunes its judge partly on this paper's human judgments, and extends consultancy into ensembled and double variants.
- [[Paper — On Scalable Oversight with Weak LLMs Judging Strong LLMs (2024)]] carries its consultancy baseline beyond reading comprehension, to tasks without information asymmetry.
- Compares itself to prover-verifier games (citing Anil et al. 2021), which it calls closer to consultancy because the provers don't face each other; [[Paper — Prover-Verifier Games Improve Legibility of LLM Outputs (2024)]] cites this paper and describes its own setup the same way.
- Its question - how to supervise experts who know the truth but may not report it - is [[Concept — Scalable oversight]] as framed in [[Paper — Concrete Problems in AI Safety (2016)]], which it cites.
- Part of [[Method — Debate]]; evidence for [[Question — Does debate's advantage come from refutation or just from hearing both sides]].

## Up

- [[Cluster — Scalable oversight]]
