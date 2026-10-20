---
type: method
status: in-progress
updated: 2026-10-20
summary: "A scalable-oversight protocol in which two capable agents argue for opposing answers in front of a weaker judge, betting that lying is harder than refuting a lie - usually compared against consultancy, where a single agent argues for an assigned answer."
cluster: ["Scalable oversight"]
sources: []
tags: [debate, scalable-oversight, consultancy]
aliases: ["AI debate", "debate protocol"]
---

# Method — Debate

## Definition

Two agents argue for opposite answers to a question, and a judge who could not answer it alone picks the winner.
The judge is weaker than the debaters - a non-expert human, or a smaller model - and is often denied information the debaters have, such as the source text, so only arguments and verifiable quotes reach it.
The standard comparison is **consultancy**: a single agent argues for an assigned answer, right half the time, while the judge questions it - a stand-in for a model trained to be convincing rather than correct.
Variants include open roles (agents choose their side), more or fewer rounds, and judges allowed to end the debate.

## Why it works, and what's unsettled

The bet, from [[Paper — AI Safety via Debate (2018)]], is that in an adversarial game it is harder to lie than to refute a lie, so a judge only has to evaluate the most contested step rather than the whole argument.
Whether the gains seen so far actually come from refutation, or just from the judge hearing both sides argued, is tracked in [[Question — Does debate's advantage come from refutation or just from hearing both sides]].

## Where it appears in this wiki

- [[Paper — AI Safety via Debate (2018)]] proposes it, with a complexity-theoretic argument and a small MNIST experiment.
- [[Paper — Debate Helps Supervise Unreliable Experts (2023)]] runs it with skilled human debaters and non-expert judges on QuALITY, finding debate beats consultancy (84% vs 74%).
- [[Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)]] finds that making LLM debaters more persuasive makes judges more accurate.
- [[Paper — Training Language Models to Win Debates with Self-Play Improves Judge Accuracy (2024)]] trains debaters and finds most of the gain is matched by a double consultancy with no refutation.
- [[Paper — On Scalable Oversight with Weak LLMs Judging Strong LLMs (2024)]] tests it across nine tasks and finds small or no gains over the judge answering alone outside information-asymmetric tasks.

## Relations

- One of the strategies of [[Concept — Scalable oversight]], tested with [[Method — Sandwiching]]-style setups.
- [[Paper — Prover-Verifier Games Improve Legibility of LLM Outputs (2024)]] is a related adversarial setup that [[Paper — Debate Helps Supervise Unreliable Experts (2023)]] classes as closer to consultancy.
- See [[Question — Does debate's advantage come from refutation or just from hearing both sides]].

## Up

- [[Cluster — Scalable oversight]]
