---
type: method
status: in-progress
updated: 2026-09-19
summary: "Use a weaker but known-safe model to rate how suspicious an untrusted model's output looks, and escalate the most suspicious fraction to expensive human-quality review."
cluster: ["Scheming & AI control"]
sources: []
tags: [ai-control, monitoring, red-teaming]
aliases: []
---

# Method — Trusted monitoring

## Definition

Given an untrusted, more capable model U and a trusted, weaker model T known not to subvert anything, ask T to rate how suspicious each of U's outputs looks, and send the most suspicious fraction (bounded by a fixed budget) to expensive trusted human-quality review.
If review confirms a genuine attempt to cause harm, shut down: stop using U and fall back to T alone.
Introduced in [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] as the first non-trivial [[Concept — AI control]] protocol, evaluated on the task of catching deliberately backdoored code.

## Variants

- **Defer to trusted (dynamic routing)**: instead of only auditing high-suspicion outputs, silently substitute T's own weaker-but-safe output whenever U's suspicion score crosses a lower threshold. This turns a single audit budget into a tunable safety/usefulness frontier - more deferral means more safety at the cost of using T's worse answers more often.
- **Untrusted monitoring**: replace T with a second copy of U itself as the suspicion-rater, since the untrusted model is typically far better at spotting subtle problems than the trusted one. This only works if the blue team defends against the monitor colluding with the policy - both instances are the same model, so nothing stops them from coordinating unless the protocol removes the shared signal they'd coordinate on (via redacting problem/code features and paraphrasing, and by validating the monitor's honesty on synthetic examples of the very failure it's meant to catch).
- **Trusted editing**: instead of auditing or deferring, have the trusted model directly edit the untrusted model's output to strip out anything suspicious, submitting the edited version for outputs in a moderate suspicion range. In the founding paper, this Pareto-dominates the other variants.

## Why it works

The method exploits an asymmetry: T is not competent enough to solve the task well, but it is competent enough to notice that something in U's solution doesn't belong, especially once forced into a false-positive-rate budget that also forces the untrusted model to make its subversion attempts subtler (and subtler attempts fail more often on their own).
Escalating only the suspicious tail, rather than everything, is what makes the small human-quality-review budget go far enough to matter.

## Relations

- The founding protocol family for [[Concept — AI control]].
- Its collusion-avoidance techniques (redaction, paraphrasing, synthetic-example validation) are a direct countermeasure to steganographic coordination, the same failure mode named in [[Concept — Jailbreaking (LLMs)]]'s adjacent literature on hidden reasoning channels.

## Up

- [[Cluster — Scheming & AI control]]
