---
type: concept
status: in-progress
updated: 2026-08-07
summary: "A model adapting to a task from examples in its prompt, within a single forward pass and with no weight updates."
cluster: ["Foundational canon"]
sources: []
tags: [in-context-learning, prompting, llm, scaling]
aliases: ["ICL", "few-shot prompting"]
---

# Concept — In-context learning

## Definition

A model performing a task it was not trained on, given only a description and/or a handful of worked examples placed in its input.
The defining constraint: **no gradient updates and no weight changes**.
Whatever adaptation occurs happens inside the forward pass, in the activations.

Named and characterized at scale in [[Paper — Language Models are Few-Shot Learners (2020)]], which distinguishes three settings by how much is shown in the prompt - zero-shot (instruction only), one-shot (a single demonstration), and few-shot (as many as the context window fits, typically 10-100).

## Why it's strange

Ordinary learning changes parameters. This doesn't.
The weights are frozen; the same forward pass that would continue a sentence instead implements something behaving like a learning algorithm over the examples in its context.
The standard framing is a nested loop: pretraining is the outer loop that updates weights, and the forward pass is an inner loop that adapts without them.

## The open question: is it learning or recognition?

Worth preserving carefully, because the original authors were more careful than most summaries of them.
They explicitly refuse to claim the model learns new tasks at inference, describing a spectrum from "demonstrations drawn from exactly the same distribution as those at test time," through "recognizing the same task in a different format," to "learning a skill entirely de novo" - and note that where a given task falls "may also vary from task to task."

The cleanest available evidence for the genuine end of that spectrum is synthetic-task performance.
Word unscrambling sits near zero in the zero-shot setting and rises sharply with in-context examples, on tasks whose "artificial nature makes them unlikely to appear in the pre-training data."
If the model could only recognize pretrained tasks, that gap shouldn't appear.
Conversely, translation "clearly must be learned during pretraining."

This is the kind of question that belongs in a `Question —` page once a second paper puts real evidence on the other side.

## Why it matters beyond capability

In-context learning is what made the **prompt** the interface to AI systems, and therefore the attack surface.
Prompt injection and many jailbreaks are in-context learning working exactly as designed, with an adversary supplying the context.
The same property that lets a user specify a task in natural language lets an attacker do so.

## Relations

- Introduced at scale by [[Paper — Language Models are Few-Shot Learners (2020)]].
- A capability of the architecture in [[Concept — Transformer architecture]], specifically its decoder-only variants.
- Its security consequence connects to [[Cluster — Adversarial robustness & security]].

## Up

- [[Cluster — Foundational canon]]
