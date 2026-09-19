---
type: method
status: mature
updated: 2026-09-18
summary: "Interleave free-form reasoning ('thoughts') with real actions against an external environment, so reasoning decides what to do next and each observation feeds back into the reasoning that follows."
cluster: ["Foundational canon"]
sources: []
tags: [prompting, reasoning, agents]
aliases: ["ReAct", "reason-act loop"]
---

# Method — ReAct

## Definition

Augment a model's action space with a "thought": a free-form text action that changes nothing in the external environment but is appended to the context, available for the model to reason over on the next step.
Prompt the model to alternate between thoughts and real actions (search queries, tool calls, environment moves), so a thought can plan what to do next, interpret an observation just received, or notice a plan has failed and needs revising.

## Why it works

Pure chain-of-thought reasons entirely from what the model has memorized, with no way to check or correct itself against the world, which is exactly what makes it prone to hallucination.
Pure action-only agents can interact with an environment but have no explicit mechanism to plan, track progress, or recover from an unexpected observation.
Interleaving the two closes that loop both ways: reasoning steers which actions get taken, and the resulting observations ground and correct the reasoning that follows, rather than either one running in isolation.

## Relations

- Origin: [[Paper — ReAct (2022)]] (Yao et al.), extending [[Method — Chain-of-Thought Prompting]] from [[Paper — Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022)]] with real actions against an environment.
- Shares its retrieval-grounding motivation with [[Method — Retrieval-Augmented Generation (RAG)]], though ReAct explicitly reasons over what to retrieve rather than automatically marginalizing over retrieved passages.

## Up

[[Cluster — Foundational canon]]
