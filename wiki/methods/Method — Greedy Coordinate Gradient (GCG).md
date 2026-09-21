---
type: method
status: in-progress
updated: 2026-09-19
summary: "A discrete-token optimization algorithm that finds adversarial suffixes for language models by using gradients to shortlist candidate token swaps, then evaluating the best of them with real forward passes."
cluster: ["Adversarial robustness & security"]
sources: []
tags: [adversarial-examples, security, jailbreak, optimization]
aliases: ["GCG"]
---

# Method — Greedy Coordinate Gradient (GCG)

## Definition

GCG is a procedure for optimizing a sequence of discrete tokens (e.g. an adversarial suffix appended to a prompt) to minimize some differentiable loss computed by a language model, despite tokens themselves being discrete and non-differentiable.

At each iteration, for every editable token position, GCG computes the gradient of the loss with respect to that position's one-hot token encoding, and takes the top-k tokens with the most favorable (most negative) gradient as candidate replacements - a cheap, linearized estimate of which swaps might help.
It then randomly samples a batch of candidate single-token substitutions from across *all* editable positions, evaluates the loss on each candidate *exactly* via a real forward pass, and keeps whichever swap reduced the loss the most.
This repeats for a fixed number of iterations.

## Why it works

The gradient-based shortlist step is only a linear approximation - swapping an entire discrete token is not really a small perturbation, so the gradient's estimate of the effect can be wrong - but it is cheap enough to narrow a vocabulary-sized search down to a tractable candidate set, which the following exact-evaluation step then adjudicates correctly.

The key design choice, relative to the prior AutoPrompt method, is searching over substitutions at **all** editable positions simultaneously each iteration rather than committing to one position in advance.
[[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] finds this single change - for the same computational budget (same number of forward-pass evaluations per step) - produces a substantially more reliable optimizer, turning a method that could rarely break aligned models into one that reliably could.

Extended to a **universal, multi-prompt, multi-model** setting, the same suffix is optimized jointly against several target prompts and several models sharing a tokenizer at once (aggregating gradients and losses across all of them, with new prompts folded in incrementally), which is what makes the resulting suffix generalize across both unseen prompts and - remarkably - unseen, architecturally unrelated black-box models.

## Relations

- Introduced in [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] to automate [[Concept — Jailbreaking (LLMs)]].
- A refinement of the AutoPrompt discrete-optimization approach, itself building on HotFlip's one-hot gradient trick.
- Evaluated against [[Benchmark — AdvBench]] and, later, standardized as one of eighteen attacks (run in three variants: standard, multi-prompt, transfer) in [[Benchmark — HarmBench]], which also builds its own R2D2 defense by adversarially training directly against it.
- Used by [[Benchmark — WMDP]] as a robustness stress test for its unlearning method, finding an unlearned model resists GCG optimization where a merely refusal-trained model does not.
- Composes directly with [[Paper — Many-shot Jailbreaking (2024)]]: a GCG suffix sharply boosts zero-shot jailbreak success but loses its advantage as more many-shot context is added.

## Up

- [[Cluster — Adversarial robustness & security]]
