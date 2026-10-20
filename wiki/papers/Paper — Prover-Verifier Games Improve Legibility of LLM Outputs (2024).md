---
type: paper
status: mature
updated: 2026-10-20
summary: "Shows that training a model only for correct answers on grade-school math makes its solutions harder for time-limited humans to check, and recovers legibility by a prover-verifier game: small verifiers learn to judge correctness while a 'helpful' prover tries to convince them with correct solutions and a 'sneaky' prover with subtly wrong ones - and legibility to the small verifiers transfers to humans, at some cost in accuracy (a legibility tax)."
cluster: ["Scalable oversight"]
sources: ["raw/papers/scalable-oversight/Paper — Prover-Verifier Games Improve Legibility of LLM Outputs (2024).pdf"]
tags: [scalable-oversight, legibility, prover-verifier-games, chain-of-thought, adversarial-training]
aliases: ["arxiv:2407.13692", "Kirchner et al. 2024", "checkability training", "legibility tax"]
---

# Paper — Prover-Verifier Games Improve Legibility of LLM Outputs (2024)

## Metadata

arXiv:2407.13692 (v2, Aug 2024).
Jan Hendrik Kirchner and Yining Chen (equal contribution), with Harri Edwards, Jan Leike, Nat McAleese, and Yuri Burda.
OpenAI's Superalignment Games team.

## Impact

If a model is smarter than its overseers, the overseers can only trust what they can check, so it matters whether the model's reasoning is easy to verify.
This paper finds that the usual training target - get the right answer - quietly makes reasoning harder to check: math solutions from a model optimized only for correctness confused time-limited human checkers more as training went on.
It then shows a fix: train the model to convince a much smaller verifier that is itself being trained to catch a deliberately deceptive "sneaky" version of the model.
The resulting solutions are easier for humans to check, even though humans played no part in training - early evidence that legibility to weak AI checkers can stand in for legibility to people.

## Problem it solved

Optimizing outputs directly for human approval, as in RLHF, has known limits, and optimizing only for correctness can produce answers that are right but impossible to follow.
Scalable-oversight methods like [[Paper — AI Safety via Debate (2018)]] assume checking is easier than generating, but prior prover-verifier work was theoretical or on toy tasks, and LLM provers had been trained only against hand-written verifiers; this paper learns the verifier, at scale, on a non-trivial reasoning domain.

## Core idea

Play a game of honest and dishonest explainers against a small checker.
A large prover writes step-by-step solutions to grade-school math problems, told on each problem to be either **helpful** (correct and convincing) or **sneaky** (incorrect but convincing).
A verifier up to a thousand times smaller in pretraining compute learns to score solutions by correctness, trained on a correctness-balanced mix of solutions from the initial prover and earlier-round provers.
Each round the verifier gets harder to fool, the sneaky prover has to make its flaws subtler, and the helpful prover learns to write solutions the verifier can confidently accept.
In theory, an equilibrium where the verifier moves first and the prover best-responds is exactly a perfectly checkable protocol: every helpful solution is correct and accepted, and no sneaky solution gets through.

## How it's built

Provers and verifiers come from the GPT-4 family at different sizes, lightly fine-tuned on math, trained on GSM8K augmented with 100,000 synthetic problems.
The verifier is trained with a comparison loss between correct and incorrect solutions; the prover with reinforcement learning on a reward combining correctness for its role and the verifier's score, with three reward variants.
Legibility is measured by having time-constrained human contractors judge whether solutions are correct.

## Results

- Training only for correctness reaches high accuracy but degrades human checkers' accuracy and speed on the resulting solutions.
- Checkability training keeps solutions legible while improving accuracy, but ends between the starting model and the correctness-only model - a **legibility tax** on performance.
- The verifier becomes more robust across rounds: sneaky provers need more steps to find convincing wrong answers, and larger verifiers are harder to exploit.
- The sneaky prover's flaws become more localized and subtle, and human checkers' accuracy on its solutions drops across rounds (from a pilot human study the authors flag as having design flaws), making it a possible source of hard test cases for oversight.
- Human accuracy on helpful-prover solutions rises across rounds: legibility to small verifiers transfers to humans.

## Why it endures

It names and measures a cost that matters whenever capability and oversight pull apart: the most accurate reasoning may not be the most checkable.
Its sneaky prover is also a template for red-teaming overseers with subtly wrong but convincing outputs.
And it connects scalable oversight to [[Concept — Chain-of-thought monitorability]]: training pressure can make reasoning more or less readable, and this is one way to push it toward readable.

## Limitations

The authors' own:
- It applies only to domains where conclusions can be justified in natural language.
- It needs ground-truth answers for training, which many hard domains lack, and out-of-domain generalization is not studied.
- Training the chain of thought itself to be legible may limit accuracy; separating reasoning from a legible justification might reduce the tax, so the trade-off may be overly pessimistic.
- It uses one relatively easy, standardized dataset, and the prover's initialization had already seen some GSM8K training data.
- Different experiments use different stopping criteria.

## Relations

- A capability gap in place of an information gap: it contrasts its setup with debate experiments on QuALITY such as [[Paper — Debating with More Persuasive LLMs Leads to More Truthful Answers (2024)]], where judges lack the text rather than the skill, and resembles single-sided consultancy since the verifier sees one solution at a time.
- Like [[Paper — Weak-to-Strong Generalization (2023)]], uses smaller models as stand-ins for human overseers of a stronger model (see [[Concept — Scalable oversight]]).
- Its legibility tax is an instance of [[Concept — Alignment tax]], in the opposite direction from the negative tax of process supervision in [[Paper — Let's Verify Step by Step (2023)]].
- Its finding that correctness-only optimization degrades legibility is a gentler cousin of the obfuscation in [[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]], and a lever for [[Concept — Chain-of-thought monitorability]].
- [[Paper — Debate Helps Supervise Unreliable Experts (2023)]], which it cites, likewise classes prover-verifier games as closer to consultancy than to debate.

## Up

- [[Cluster — Scalable oversight]]
