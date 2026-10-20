---
type: question
status: in-progress
updated: 2026-10-20
summary: "Whether debate helps weak judges because opposing debaters refute each other's false claims - the mechanism the theory relies on - or merely because the judge hears both answers argued, which a non-interactive double consultancy also provides."
cluster: ["Scalable oversight"]
sources: []
tags: [debate, scalable-oversight, open-question]
aliases: []
---

# Question — Does debate's advantage come from refutation or just from hearing both sides?

## The question

The case for [[Method — Debate]] as a way to oversee models smarter than us rests on refutation: a dishonest debater's false claim gets pointed out by its opponent, so the judge only needs to check the contested step.
If that is what drives the gains, debate should scale to questions too hard for the judge.
If instead judges improve just because they see both answers argued, debate offers less than it seems - any setup showing both sides would do as well, and nothing guarantees the judge can tell which side is right on harder questions.

## Evidence so far

- **For "refutation is the mechanism" (theory):** [[Paper — AI Safety via Debate (2018)]] - its central hypothesis is that in the debate game it is harder to lie than to refute a lie.
- **For "the opposing expert matters" (humans):** [[Paper — Debate Helps Supervise Unreliable Experts (2023)]] - debate between skilled human experts beats a single consultant (84% vs 74%), and the authors hypothesize the advantage comes from claims gaining reliability by going unrefuted by an opposing expert, recommending open-ended debates so refutation is always possible.
- **For "hearing both sides does most of the work":** [[Paper — Training Language Models to Win Debates with Self-Play Improves Judge Accuracy (2024)]] - a double consultancy that shows both sides with no interaction reaches 75% against debate's 77% (averaged across training checkpoints), and judges are insensitive to second-speech refutations.
- **For "more back-and-forth doesn't help":** [[Paper — On Scalable Oversight with Weak LLMs Judging Strong LLMs (2024)]] - one versus three turns makes no significant difference to judge accuracy across its nine tasks, and on QuALITY the authors note earlier evidence that extra rounds don't help and that quote verification does most of the work.

## Why this might not resolve cleanly

The positive human result and the negative LLM results differ in who debates: skilled human debaters in interactive debates the judge chose when to end, versus LLM debaters whose refutations judges may simply not weigh.
The tension may reflect today's LLM debaters and judges rather than debate itself, so it could flip as models improve.
What this wiki lacks is a test on questions the judge genuinely cannot answer even after hearing both sides, where only refutation could help - and a comparison of debate with double consultancy using strong human or strong model debaters.

## Up

- [[Cluster — Scalable oversight]]
