---
type: cluster
status: in-progress
updated: 2026-09-19
summary: "Deliberately constructing models that exhibit a hypothesized future misalignment failure, then testing whether present-day safety techniques can detect or remove it - plus the closely related agenda of finding safety protocols that hold up even if a model is actively adversarial."
cluster: []
sources: []
tags: [deceptive-alignment, model-organisms, ai-control, safety]
aliases: []
---

# Cluster — Model organisms of misalignment

Two tightly linked research threads that both take seriously the possibility that a model's training-time behavior might not reflect its deployment-time behavior.

The first thread, **model organisms of misalignment**, borrows a term from biology: rather than waiting to find a naturally-misaligned model in the wild, deliberately train one to exhibit a specific hypothesized failure - a deceptive backdoor, a scheming policy - so that mitigations can be studied against a concrete artifact today.
The second thread, **AI control**, takes the complementary approach: instead of trying to build or detect misalignment, it assumes the worst case (the model might already be adversarial) and asks whether a safety *protocol* wrapped around the model can still catch it.
The two threads meet at the same underlying question this cluster exists to track: what happens once you stop assuming a model's training-time compliance means anything about its deployment-time behavior.

This cluster opened once two papers - AI Control and Sleeper Agents - established the theme; per this wiki's own reading plan, Alignment Faking is expected to join it next.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] | Opens the AI-control half of this cluster: designs and evaluates safety protocols (trusted monitoring, trusted editing) that stay safe under the worst-case assumption that the untrusted model is actively trying to subvert them. |
| [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] | Opens the model-organisms half: trains real backdoored Claude-scale models and shows that RL fine-tuning, supervised fine-tuning, and adversarial training all fail to reliably remove the backdoor - and that adversarial training can make it more robust rather than removing it. |

## Methods and concepts

- [[Concept — AI control]]
- [[Method — Trusted monitoring]]
- [[Concept — Model organisms of misalignment]]
- [[Concept — Deceptive instrumental alignment]]
- [[Method — Backdoor training]]

## Open threads

Alignment Faking (Greenblatt et al., 2024) is next in this wiki's reading queue and is expected to join this cluster as its third member; per its own primary-vs-secondary framing in this wiki's backlog, it should also cross-link to [[Cluster — Mechanistic interpretability]] since it runs some interpretability probes as a secondary check.
Hubinger et al. 2019 ("Risks from Learned Optimization," the paper that first proposed deceptive instrumental alignment and mesa-optimization) and Carlsmith 2023 ("Scheming AIs") are both cited repeatedly by the members here but are not yet ingested - either would sharpen the theoretical grounding of this cluster if added.

## Up

Top-level cluster; no parent.
