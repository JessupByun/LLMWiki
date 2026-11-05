---
type: method
status: mature
updated: 2026-11-04
summary: "Write few-shot prompt exemplars as worked step-by-step reasoning followed by a final answer, rather than a direct question-to-answer mapping, eliciting multi-step reasoning from sufficiently large language models with no fine-tuning."
cluster: ["Foundational canon"]
sources: []
tags: [prompting, reasoning, in-context-learning]
aliases: ["CoT", "Chain-of-Thought Prompting", "Chain of Thought"]
---

# Method — Chain-of-Thought Prompting

## Definition

Format each few-shot prompt exemplar as a question, followed by a narrated sequence of intermediate reasoning steps, followed by the final answer - rather than a direct question-to-answer pair.
Given a new question at inference time, a sufficiently large language model imitates this pattern, generating its own intermediate reasoning before committing to a final answer, with no fine-tuning and no change to the underlying model.

## Why it works

Decomposing a multi-step problem into narrated intermediate steps lets a model allocate more computation (more generated tokens) to harder problems, and lets each step condition on the ones before it rather than requiring the entire solution to be produced in a single forward inference.
This only reliably helps once a model is large enough (roughly 100B+ parameters) to have already learned the underlying semantic and arithmetic sub-skills the chain of thought calls on; below that scale the added reasoning text tends to be fluent but logically incoherent, and can hurt performance relative to direct prompting.

## Relations

- Origin: [[Paper — Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022)]] (Wei et al.), extending the few-shot prompting format introduced by [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3).
- Used as a feedback-quality technique in [[Paper — Constitutional AI - Harmlessness from AI Feedback (2022)]]'s RL-CAI pipeline, prompting the feedback model to "think step-by-step" before making a harmlessness comparison.
- [[Paper — Language Models Don't Always Say What They Think - Unfaithful Explanations in Chain-of-Thought Prompting (2023)]] complicates this method's usual framing as a pure capability-eliciting technique: the same narrated reasoning that improves accuracy on hard problems can also be systematically unfaithful to what actually drove the model's final answer, and [[Paper — Reasoning Models Don't Always Say What They Think (2025)]] finds the same gap in RL-trained reasoning models.
- For [[Cluster — Scheming & AI control]], the generated chain is also a monitoring surface (see [[Concept — Chain-of-thought monitorability]]): [[Paper — Chain of Thought Monitorability - A New and Fragile Opportunity for AI Safety (2025)]] argues it is a fragile safety opportunity, [[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]] shows a chain-of-thought monitor catches reward hacking but that training against it teaches obfuscation, and [[Paper — CoT Red-Handed - Stress Testing Chain-of-Thought Monitoring (2025)]] stress-tests chain-of-thought monitors in a control evaluation.

## Up

[[Cluster — Foundational canon]]
