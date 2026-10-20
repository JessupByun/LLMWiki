---
type: paper
status: mature
updated: 2026-10-20
summary: "Formalizes AI control evaluations as AI-Control Games - multi-objective, partially observable stochastic games between a protocol designer and a red team standing in for a scheming model - and uses the formalism to compute safety and usefulness for whole families of trusted-monitoring protocols from existing data, finding Pareto-improvements over the original paper's trusted-monitoring protocols, such as auditing more than 2% of outputs early."
cluster: ["Scheming & AI control"]
sources: ["raw/papers/scheming-and-ai-control/Paper — Games for AI Control - Models of Safety Evaluations of AI Deployment Protocols (2024).pdf"]
tags: [ai-control, game-theory, trusted-monitoring, control-evaluation, formal-methods]
aliases: ["arxiv:2409.07985", "Griffin et al. 2024", "AI-Control Games"]
---

# Paper — Games for AI Control - Models of Safety Evaluations of AI Deployment Protocols (2024)

## Metadata

arXiv:2409.07985 (v2, May 2026; originally Sep 2024); presented at IASEAI 2026.
Charlie Griffin, Louis Thomson, Buck Shlegeris, and Alessandro Abate.
University of Oxford and Redwood Research.

## Impact

[[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] evaluated protocols by actually playing a red-team/blue-team exercise with language models, which is expensive and only tests the handful of protocols someone thought to try.
This paper writes that exercise down as a formal game, so a protocol's safety and usefulness become quantities you can compute, compare, and optimize.
Plugging in the original paper's own monitor data, it evaluates whole families of protocols without running a single new language-model experiment and finds better ones than the originals.
It also makes the field's implicit assumptions explicit - for example that an honest model never takes a dangerous action - and relaxes some of them itself - an imperfect human auditor, and honest models that occasionally take dangerous actions.

## Problem it solved

The original control evaluation implicitly involved a game but never modeled it, so there was no way to say whether a protocol was optimal, to explore protocols beyond those tested, or to check how results depend on assumptions such as a perfect human auditor.
Existing game-theory tools didn't fit directly: security games are usually one-round and perfect-information, and standard solvers for partially observable games assume both players remember the past.

## Core idea

Picture a protocol deciding what to do with each of 1,000 programming solutions from a powerful untrusted model U.
It can audit the solution with a human (who catches any backdoor, but only 2% of the time is affordable), defer to a weaker trusted model T to write its own solution, or run U's solution.
Before deciding, it sees T's suspicion score.
The protocol doesn't know whether this is an "honest" run, where U is benign and only usefulness matters, or an "attack" run, where a red team controls U and only safety matters.
That uncertainty is the heart of the game: every audit spent on an honest run uses up budget an attack run might need, every deferral to T costs usefulness, and every unaudited backdoor in an attack run is a catastrophe.

Formally, an **AI-Control Game** is a two-player, two-objective, partially observable, finite-horizon game.
Blue (the protocol) chooses first and can remember everything it has seen; Red (the red team, standing in for a scheming model) then best-responds but is **memoryless**, since the protocol controls U's servers and context.
The solution concept is an **AI-Control Equilibrium**: a protocol whose worst-case safety and usefulness are jointly Pareto-optimal.

## How it's built

The trusted-monitoring setting is modeled on the APPS backdooring setup of the original AI Control paper.
T's suspicion scores are fit as Beta distributions to the original paper's data, plus a hypothetical "weak" monitor that separates safe and backdoored code less well.
The paper proves every game has an equilibrium, shows Red's mixed and behavioral strategies need not be equivalent, and reduces the multi-objective problem to a series of single-objective zero-sum games.
It adapts a Double Oracle algorithm to find approximate Pareto frontiers, using grid search for Blue and a one-dimensional search for Red, whose only choice is how often to attempt a backdoor.

## Results

- All seven trusted-monitoring variants from the original paper are special cases of one family of threshold protocols, parameterized by audit and defer false-positive rates.
- Auditing at a false-positive rate above 2% helps, even though 2% is the rate that would exactly spend the budget on an honest run: a 3% audit rate gives a small Pareto-improvement with the original monitor, and 5% a large one with the weak monitor, despite risking running out of audits early.
- With the original monitor, the closest of the original protocols sits within 0.0042 of the Pareto frontier and deterministic protocols are mostly optimal; with the weak monitor, the originals are at least 0.02 away and randomized mixtures of protocols make up most of the frontier.
- More audit budget makes protocols markedly safer and more useful, especially when the budget is small; the number of problems matters little for fixed rates.
- An imperfect human auditor that misses backdoors reduces safety almost linearly in the worst case.

## Why it endures

It turns control evaluation from a one-off exercise into an optimization problem: once a monitor's score distributions are measured, protocols can be designed and compared cheaply, and new settings explored by swapping in new distributions.
Its separation of scheming from honest-but-buggy models is also a durable conceptual point: if an honest model can occasionally take a dangerous action, catching one bad action no longer proves scheming, but schemers may still be distinguishable, because their dangerous actions are likely to be correlated across a sequence.

## Limitations

The authors note that their results cover only memoryless protocols, leaving protocols that act on the full history to future work.
The trusted-monitoring model inherits the original paper's simplifications: a red team whose only choice is whether to attempt a backdoor, and backdoors that a human auditor catches perfectly unless that assumption is explicitly relaxed.
Monitor behavior is summarized by fitted score distributions rather than modeled from actual model interactions, which the authors plan to study.
Red's memorylessness, though justified by the protocol controlling the untrusted model, makes best responses hard to compute in general; the example is tractable because Red's strategy space is one-dimensional.

## Relations

- Formalizes the evaluation of [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]], reusing its APPS backdooring setting and monitor data, and the [[Method — Trusted monitoring]] protocols it introduced.
- Its safety objective generalizes to minimizing the expected number of harmful outcomes, the objective of [[Paper — Adaptive Deployment of Untrusted LLMs Reduces Distributed Threats (2024)]].
- A formal backbone for [[Method — Control evaluation]], complementing the deployment-argument view of [[Paper — A Sketch of an AI Control Safety Case (2025)]].
- [[Paper — A Sketch of an AI Control Safety Case (2025)]] follows it in splitting a control evaluation into per-task microgames and a deployment-level macrogame.

## Up

- [[Cluster — Scheming & AI control]]
