---
type: concept
status: in-progress
updated: 2026-09-19
summary: "A strong model finetuned on labels from a much weaker supervisor generalizing beyond that supervisor's own accuracy, by having its own latent capabilities elicited rather than taught."
cluster: ["Post-training alignment"]
sources: []
tags: [alignment, superalignment, scalable-oversight, safety]
aliases: ["PGR", "performance gap recovered"]
---

# Concept — Weak-to-strong generalization

## Definition

Weak-to-strong generalization is the phenomenon where a strong pretrained model, naively finetuned on labels produced by a much weaker supervisor model, ends up performing *better* than that supervisor - despite never seeing a single accurate label.
It's studied as a deliberate analogy for the future problem of humans supervising superhuman AI: today, substitute a small model for the human, and a large pretrained model for the superhuman one, and measure whether meaningful oversight survives the capability gap.

The standard measurement is **Performance Gap Recovered (PGR)**: `(weak-to-strong performance − weak performance) / (strong ceiling performance − weak performance)`, where strong ceiling performance is what the same large model achieves when finetuned on real ground-truth labels instead.
PGR of 1 means the strong model fully recovers its own latent capability from weak supervision alone; PGR of 0 means it does no better than imitating the weak supervisor.

## Why it works (when it does)

The motivating intuition is that a strong pretrained model shouldn't need to be *taught* many alignment-relevant tasks from scratch - if it can generate a million lines of complex code, it plausibly already has some internal representation of whether that code is safe, so weak supervision only needs to *elicit* that existing knowledge rather than install new capability.
[[Paper — Weak-to-Strong Generalization (2023)]] finds this holds up only partially: naive finetuning on weak labels does reliably beat the weak supervisor, but recovers well under half the available performance gap by default, and is especially poor when applied to reward modeling - the exact setting real RLHF pipelines depend on.
Simple interventions help substantially: an auxiliary confidence loss that lets the strong student confidently disagree with the weak label, bootstrapping supervision through a sequence of intermediate-sized models, and unsupervised finetuning on task-relevant data to increase how "salient" the target concept already is to the strong model.

The main failure mode this fights against is the strong model simply **imitating the weak supervisor's mistakes** rather than eliciting its own better judgment - the paper's "human simulator" concern, borrowed from theoretical work on eliciting latent knowledge.

## Relations

- Introduced and measured (via PGR) in [[Paper — Weak-to-Strong Generalization (2023)]].
- Complementary to [[Concept — Scalable oversight]]: scalable oversight tries to improve the supervision signal itself, weak-to-strong generalization tries to get a model to outperform whatever supervision it's given.
- The failure mode it fights - imitating a flawed supervisor's specific errors - closely parallels [[Concept — Reward hacking]], a strong optimizer exploiting the gap between a proxy signal and the true objective.
- Explicitly assumes no deceptive alignment in the base model being supervised, a premise [[Method — Model organisms of misalignment]] and [[Cluster — Scheming & AI control]] investigate directly - see especially [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] and [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]].
- [[Paper — AI Safety via Debate (2018)]] targets the same overseer-capability gap from the adversarial-decomposition side - two competing agents - rather than this concept's generalization-elicitation side.

## Up

- [[Cluster — Post-training alignment]]
