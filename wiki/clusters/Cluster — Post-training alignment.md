---
type: cluster
status: in-progress
updated: 2026-09-24
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
| [[Paper — Concrete Problems in AI Safety (2016)]] | Names and frames reward hacking and scalable oversight as concrete research problems, supplying the vocabulary this whole cluster's failure modes are discussed in. |
| [[Paper — Deep RL from Human Preferences (2017)]] | Origin of RLHF - learn a reward model from human comparisons of short clips, then optimize it, cutting oversight cost by ~3 orders of magnitude. |
| [[Paper — Learning to Summarize from Human Feedback (2020)]] | Bridges Christiano et al. 2017's RLHF loop to GPT-3-scale language models, showing a 1.3B feedback-trained model beats both a 10x larger supervised model and the human reference summaries themselves. |
| [[Paper — Training LMs to Follow Instructions (2022)]] (InstructGPT) | InstructGPT - RLHF applied to GPT-3 at scale, showing a 1.3B fine-tuned model beats 175B raw GPT-3 on human preference. |
| [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]] (HH-RLHF) | Trains helpfulness and harmlessness as separate, partially anti-correlated preference objectives via RLHF, releases the widely-reused HH-RLHF dataset, and shows alignment training carries essentially no capability cost at sufficient scale. |
| [[Paper — Defining and Characterizing Reward Hacking (2022)]] | Gives reward hacking its first formal mathematical definition, proving that a non-trivial unhackable proxy essentially cannot exist over the full policy space - a theoretical grounding for the reward-hacking failures documented empirically throughout this cluster. |
| [[Paper — Scaling Laws for Reward Model Overoptimization (2022)]] | Measures reward hacking precisely for the first time, fitting scaling laws that predict exactly how far a policy can be optimized against a proxy reward model before ground-truth quality turns over. |
| [[Paper — Constitutional AI (2022)]] | Trains a harmless, non-evasive assistant using only a short written list of principles, replacing human harmlessness labels with AI-generated ones (RLAIF). |
| [[Paper — Direct Preference Optimization (2023)]] | DPO - a closed-form reparameterization that turns the RLHF objective into a single classification loss on the policy itself, matching or beating PPO-based RLHF with no reward model and no RL loop. |
| [[Paper — Let's Verify Step by Step (2023)]] | Trains a reward model on step-level human labels instead of only the final answer, showing process supervision produces a far more reliable verifier than outcome supervision - and does so at no cost to performance, a rare "negative alignment tax" result. |

## Methods and concepts

- [[Method — Reinforcement learning from human feedback (RLHF)]]
- [[Method — Proximal Policy Optimization (PPO)]] - the RL algorithm RLHF's third stage actually runs; full treatment lives in [[Cluster — Reinforcement learning]].
- [[Concept — Bradley-Terry preference model]]
- [[Concept — Reward hacking]]
- [[Method — Process Reward Model (PRM)]]
- [[Concept — Alignment tax]]

## Open threads

Every gap this section previously flagged (PPO, InstructGPT, Constitutional AI, DPO) is now filled.
[[Paper — AI Safety via Debate (2018)]] and [[Paper — Weak-to-Strong Generalization (2023)]] have moved out to the new [[Cluster — Scalable oversight]]: both address a genuinely different question - can a supervisor meaningfully oversee a model more capable than itself - from this cluster's own focus on shaping the model currently in front of you, and between the two of them now have enough depth to stand as their own pillar.
[[Paper — Let's Verify Step by Step (2023)]]'s ORM baseline traces back to Cobbe et al. 2021's verifiers and its direct comparison point is Uesato et al. 2022 - both still not yet ingested, and both would sharpen this cluster's process-vs-outcome-supervision thread if added later.

## Up

Top-level cluster; no parent.
