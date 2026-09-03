---
type: cluster
status: in-progress
updated: 2026-09-02
summary: "Turning a pretrained model into one that does what humans want - preference learning, reward modeling, RLHF and its successors."
cluster: []
sources: []
tags: [alignment, rlhf, post-training]
aliases: []
---

# Cluster — Post-training alignment

Everything that happens *after* pretraining to make a model behave as intended: preference collection, reward modeling, RL against a learned objective, and the failure modes that follow.

The organizing insight of this cluster, and the reason it exists as a pillar rather than a footnote to RL, is the move [[Paper — Deep RL from Human Preferences (2017)]] made: don't use humans as the reward signal, use them to train a *model* of the reward signal, then optimize that.
Everything downstream inherits both the leverage and the liability of that substitution - the leverage is a ~1000x reduction in human oversight cost, the liability is that you are now optimizing a proxy.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — Deep RL from Human Preferences (2017)]] | Origin of RLHF - learn a reward model from human comparisons of short clips, then optimize it, cutting oversight cost by ~3 orders of magnitude. |
| [[Paper — Training LMs to Follow Instructions (2022)]] | InstructGPT - RLHF applied to GPT-3 at scale, showing a 1.3B fine-tuned model beats 175B raw GPT-3 on human preference. |
| [[Paper — Constitutional AI (2022)]] | Trains a harmless, non-evasive assistant using only a short written list of principles, replacing human harmlessness labels with AI-generated ones (RLAIF). |
| [[Paper — Direct Preference Optimization (2023)]] | DPO - a closed-form reparameterization that turns the RLHF objective into a single classification loss on the policy itself, matching or beating PPO-based RLHF with no reward model and no RL loop. |

## Methods and concepts

- [[Method — Reinforcement learning from human feedback (RLHF)]]
- [[Method — Proximal Policy Optimization (PPO)]] - the RL algorithm RLHF's third stage actually runs; full treatment lives in [[Cluster — Reinforcement learning]].
- [[Concept — Bradley-Terry preference model]]
- [[Concept — Reward hacking]]

## Open threads

Every gap this section previously flagged (PPO, InstructGPT, Constitutional AI, DPO) is now filled.
Next candidates: Sparrow (Glaese et al. 2022, DeepMind's contemporaneous alternative to CAI, with a more carefully-designed rule set) and a second post-CAI paper on scalable oversight, since [[Cluster — Adversarial robustness & security]] and this cluster increasingly overlap once red-teaming and jailbreaks are involved.

## Up

Top-level cluster; no parent.
