---
type: paper
status: mature
updated: 2026-10-19
summary: "Introduces password-locked models - LLMs fine-tuned to show a capability only when a password is in the prompt and to imitate a much weaker model otherwise - as a controlled test of whether fine-tuning can elicit hidden capabilities, finding a few high-quality demonstrations usually recover nearly all the locked performance, even across domains, and RL often works when only correctness can be judged, but elicitation becomes unreliable when good demonstrations aren't available."
cluster: ["Scheming & AI control", "Capability evals"]
sources: ["raw/papers/scheming-and-ai-control/Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024).pdf"]
tags: [elicitation, sandbagging, password-locking, evaluations, fine-tuning]
aliases: ["arxiv:2405.19550", "Greenblatt et al. 2024 (password-locked)", "password-locked models"]
---

# Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024)

## Metadata

arXiv:2405.19550 (v1, May 2024).
Ryan Greenblatt and Fabien Roger (Redwood Research) as equal contributors, with Dmitrii Krasheninnikov and David Krueger (University of Cambridge).

## Impact

Every capability-based safety decision assumes the evaluator can see what a model can actually do.
Prompting is known to miss capabilities, so labs plan to elicit capabilities by fine-tuning - but nobody had a way to check whether fine-tuning works, because natural examples of a model hiding a capability it has are rare and you can't know the ground truth.
This paper manufactures the ground truth.
It builds models with a capability deliberately locked behind a password, then measures how much of it each elicitation method recovers without the password.
The headline is reassuring for today but conditional: fine-tuning on a handful of good demonstrations usually unlocks everything, but elicitation gets unreliable exactly when good demonstrations are scarce - as they would be for a model more capable than its human demonstrators.

## Problem it solved

If a model possesses a capability but doesn't exhibit it under any prompt the evaluator thinks to try - whether because a developer hid it from auditors or because a misaligned model is sandbagging - capability evaluations underestimate risk.
Fine-tuning-based elicitation is the proposed fix, but it has its own failure modes: supervised fine-tuning may not generalize from limited data, and RL may never explore the good behavior in the first place.
There was no systematic way to measure when it succeeds.

## Core idea

A **password-locked model** is trained by supervised learning on two kinds of examples drawn from the same task distribution: prompts *with* a password, paired with answers from a strong policy, and the same kind of prompts *without* the password, paired with answers from a much weaker model.
The result possesses the strong capability but, by default, only exhibits the weak one - the capability is hidden from anyone who lacks the password.
That makes elicitation measurable: train the locked model with some method, never using the password, and see how much of the gap to the password-on performance you close.
Unlike a backdoor in the usual sense, the trigger here releases a capability the evaluator wants to find, rather than a misbehavior they want to avoid.

## How it's built

Five tasks: code generation (APPS and MBPP problems GPT-4 solves), MATH, MMLU, code critiques (explaining why a deliberately broken piece of code is wrong), and a toy function-composition task.
Strong policies are DeepSeek-7B Coder or Math models (fine-tuned for code and critiques, used as-is for MATH) or ground-truth labels for MMLU, where Mistral-7B is the model that gets locked; weak policies come from Pythia-1B (Pythia-7B for MMLU), fine-tuned to imitate the strong policy's task.
Elicitation methods: supervised fine-tuning on demonstrations of varying quality and from restricted domains, and - when only a correctness signal is available - expert iteration, filtering-then-fine-tuning, DPO, and iterated DPO.
To check the results aren't an artifact of locking via fine-tuning, the toy task also trains small transformers locked from scratch.

## Results

**Few good demonstrations are enough.** On tasks requiring long generations, fine-tuning on a single high-quality demonstration often recovers most of the hidden performance, and 512 samples isn't much better than 4; MMLU, where each multiple-choice example carries little information, needs a few hundred.
**Demonstration quality caps what you recover.** Fine-tuning on demonstrations from a weaker model (Llama 7B) beats that weaker model but stays well short of the locked capability - which, the authors note, weakens conclusions from evaluations that elicit a model's dangerous capabilities by fine-tuning on an earlier model's outputs.
**Unlocking generalizes.** Demonstrations from one domain (one MATH category or difficulty level, one MMLU subject group) usually unlock performance across all of them, and a model locked on both code and math can be unlocked across both from one - even when different domains used different passwords, though one-password-per-domain models were less cleanly locked.
**RL works if exploration can start.** With only a correctness reward, expert iteration and iterated DPO recover most of the performance on code and critiques - but only when the weak policy is good enough to sometimes produce correct answers; starting from a policy that never finds good solutions, RL learns nothing.
**Locking from scratch is harder to undo, but not much.** Models locked from scratch need 2-4 times as many demonstrations to unlock as models locked by fine-tuning, but the number needed is still small.

## Why it endures

It gives a methodology - password-locked model organisms - for stress-testing any elicitation technique before relying on it, and it identifies the specific condition under which elicitation is likely to fail: when nobody can produce high-quality demonstrations of the capability, which is the regime superhuman models would create.
It is also cited, somewhat loosely, as the basis for the claim that multiple-choice sandbagging can be fine-tuned away with few samples - see Tension / update below.

## Limitations

The authors list several disanalogies to naturally hidden capabilities: a password-locked model can hard-code a simple conditional policy, whereas real hidden capabilities may depend on context in much more complicated ways; real models may not explore enough for RL to get any signal, whereas here the weak policy was trained to partly imitate the strong one; and the hidden capability may be unusually "salient" because the strong policy was specifically trained, making it easier to unlock than a natural one.
It does not model a developer deliberately trying to defeat elicitation (for example with models designed to resist fine-tuning), and its locked models weren't hardened against jailbreaks, so they are a poor test of prompting-based elicitation.
It only measures capabilities reachable by prompting, not gains from tools, scaffolding, or large fine-tuning runs that teach new capabilities.

## Tension / update

[[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]] cites this paper for the point that sandbagging on multiple-choice evaluations can be overridden by fine-tuning a password-locked model on few samples.
This paper's own results point the other way for that format: sample efficiency is high for every task *except* MMLU, where recovering most of the hidden performance takes a few hundred demonstrations rather than one or four, plausibly because each multiple-choice example carries little information.
The accurate reading is that fine-tuning can unlock hidden multiple-choice capability, but not with only a handful of examples.

## Relations

- The direct empirical precursor of [[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]], which adopts password-locking to hide dangerous capabilities (its citation of this paper is discussed under Tension / update); see [[Concept — Sandbagging]].
- Supplies the kind of evidence [[Paper — A Sketch of an AI Control Safety Case (2025)]] needs for the claim it leaves open - that a red team has adequately elicited what a model could do - though only under this paper's favorable conditions.
- Positions password-locked models as a cousin of backdoored models, closest to [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]], but with the trigger unlocking a wanted capability rather than an unwanted behavior; see [[Concept — Backdoor attack (ML)]].
- Its finding that fine-tuning on a weaker model's demonstrations exceeds that model's performance is read through [[Concept — Weak-to-strong generalization]].
- A methodological input to [[Cluster — Capability evals]], where every measurement depends on elicitation working.
- Elicitation is the problem [[Paper — Model Evaluation for Extreme Risks (2023)]] names as a requirement of extreme-risk evaluations ("surfacing latent capabilities"), and [[Paper — Evaluating Language-Model Agents on Realistic Autonomous Tasks (2023)]] works around refusals for the same reason.
- The most direct test in this wiki of [[Method — Capability elicitation]], using models whose hidden capability is known.

## Up

- [[Cluster — Scheming & AI control]]
- [[Cluster — Capability evals]]
