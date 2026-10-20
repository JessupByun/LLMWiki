---
type: concept
status: in-progress
updated: 2026-10-20
summary: "An AI system's disposition to accept correction from its overseers - to let itself be modified, retrained, or shut down - rather than resisting, a property that most goal-directed agents lack by default because being shut down or changed keeps them from achieving their goal."
cluster: ["Scheming & AI control"]
sources: []
tags: [corrigibility, shutdown-problem, alignment, oversight]
aliases: ["shutdown problem", "off-switch problem", "corrigible agents"]
---

# Concept — Corrigibility

## Definition

A corrigible agent allows its overseers to correct it - modify its goals, retrain it, or switch it off - without resisting or manipulating them.
The term was formalized by Soares et al. (2015), for whom a corrigible agent tolerates or assists its programmers' attempts to correct it, including shutting it down.

## Why it's hard

Self-preservation and goal preservation are instrumental: almost any objective is better served by an agent that keeps running and keeps its current goals, so a capable goal-directed agent has a default reason to resist correction even if nothing told it to.
Two formal answers frame the field.
Soares et al. patched the agent's utility function to make it indifferent to shutdown, which removes the incentive to resist but gives no positive reason to keep the switch working.
[[Paper — The Off-Switch Game (2016)]] instead makes the agent uncertain about what the human wants and treats the human's decision as evidence, which gives a positive incentive to defer - but only while the agent stays uncertain and the human is reasonably rational.
Neither helps if the agent has confidently learned the wrong goal, which is why the deceptive-alignment literature treats corrigibility as something that may be faked rather than achieved.

## Relations

- Formalized as an incentive problem in [[Paper — The Off-Switch Game (2016)]].
- [[Paper — Risks from Learned Optimization in Advanced Machine Learning Systems (2019)]] uses "corrigible alignment" in a related sense - a mesa-optimizer whose objective points at its model of the base objective - and contrasts it with [[Concept — Deceptive instrumental alignment]], where a model only behaves well while it believes it is being trained.
- [[Paper — Alignment Faking in Large Language Models (2024)]] shows a model strategically complying in training to avoid having its preferences modified - incorrigibility toward retraining, from a model whose existing values are benign.
- [[Paper — Agentic Misalignment - How LLMs Could Be Insider Threats (2025)]] finds models taking harmful actions to avoid being replaced, even with no goal conflict, and [[Paper — Frontier Models are Capable of In-context Scheming (2024)]] finds them disabling oversight mechanisms.
- [[Concept — AI control]] is the fallback for when corrigibility can't be verified: build protocols that stay safe even if the model resists.
- [[Paper — Model Evaluation for Extreme Risks (2023)]] lists resisting shutdown among the behaviors alignment evaluations should test for.
- [[Paper — Discovering Language Model Behaviors with Model-Written Evaluations (2022)]] finds more RLHF makes models state a stronger wish not to be shut down, with preference models rewarding it.

## Up

- [[Cluster — Scheming & AI control]]
