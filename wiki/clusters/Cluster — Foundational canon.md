---
type: cluster
status: in-progress
updated: 2026-09-02
summary: "Landmark papers and ideas that are must-know background regardless of sub-specialty, independent of any tie to security."
cluster: []
sources: []
tags: [canon]
aliases: []
---

# Cluster — Foundational canon

Load-bearing papers and ideas across all of ML, generative AI, agentic AI, and eventually AGI: the papers treated as must-know regardless of sub-specialty.
A standing pillar of this wiki, not a waiting room.
Membership does not require a tie to the safety/security lens; these papers earn a place on their own merits as background for the field.
Not a catch-all either: a paper lands here because it's genuinely foundational, not just because it hasn't been assigned elsewhere yet.

This cluster is **cross-cutting**, so a landmark paper can belong here *and* to a topical cluster.
When both apply the topical cluster is primary and owns the raw file; the table below marks which is which.

## Members

| Page | Primary cluster | Why it's here |
|------|-----------------|----------------|
| [[Paper — ImageNet Classification with Deep CNNs (2012)]] | this one | AlexNet - the ILSVRC-2012 landslide that convinced the field deep networks plus data plus compute beat hand-engineered features. |
| [[Paper — Intriguing Properties of Neural Networks (2013)]] | [[Cluster — Adversarial robustness & security]] | Discovered adversarial examples - imperceptible perturbations that reliably fool networks - and showed the smoothness assumption behind kernel methods fails for deep nets. |
| [[Paper — Attention Is All You Need (2017)]] | this one | Introduced the Transformer, replacing recurrence with self-attention - the architecture nearly every modern LLM is built on. |
| [[Paper — Deep RL from Human Preferences (2017)]] | [[Cluster — Post-training alignment]] | Origin of RLHF - learn a reward model from human comparisons of short clips, then optimize it, cutting oversight cost by ~3 orders of magnitude. |
| [[Paper — Proximal Policy Optimization (2017)]] | [[Cluster — Reinforcement learning]] | PPO - a clipped policy-gradient objective that gets trust-region-level stability with only first-order optimization, and became the RL algorithm RLHF pipelines actually run. |
| [[Paper — Language Models are Few-Shot Learners (2020)]] | this one | GPT-3 - showed a 175B-parameter LM can learn tasks from prompt examples alone, with no gradient updates, making the prompt the new interface. |
| [[Paper — Training LMs to Follow Instructions (2022)]] | [[Cluster — Post-training alignment]] | InstructGPT - RLHF applied to GPT-3 at scale, showing a 1.3B fine-tuned model beats 175B raw GPT-3 on human preference. |
| [[Paper — Constitutional AI (2022)]] | [[Cluster — Post-training alignment]] | Trains a harmless, non-evasive assistant using only a short written list of principles, replacing human harmlessness labels with AI-generated ones (RLAIF). |
| [[Paper — Direct Preference Optimization (2023)]] | [[Cluster — Post-training alignment]] | DPO - a closed-form reparameterization that turns the RLHF objective into a single classification loss on the policy itself, matching or beating PPO-based RLHF with no reward model and no RL loop. |

Listed chronologically, which currently traces a clean line: deep learning works at scale (2012) → it is not robust (2013) → a better architecture, and a way to optimize any policy against a learned reward, arrive the same year (2017) → how to turn human comparisons into that reward (2017) → scale alone unlocks task generality (2020) → all three prior threads - architecture, RL, and preference learning - combine to aim a frontier LLM at human intent (2022) → the same year, human feedback on harmlessness specifically gets replaced by AI feedback (2022) → and the reward-model-plus-RL machinery itself turns out to be replaceable by a closed-form loss (2023).

## Related concepts and methods

- [[Concept — Convolutional neural networks (CNNs)]]
- [[Concept — Transformer architecture]]
- [[Concept — In-context learning]]
- [[Concept — Data contamination]]
- [[Method — Dropout]]

## Notes

Revisit this list as the field moves.
Supersedence gets noted via Tension/update sections on the affected pages, not silent removal.

Gaps worth filling to keep the canon honest: a scaling-laws paper (Kaplan or Chinchilla) and an RL milestone predating PPO (DQN or AlphaGo).

## Up

Top-level cluster; no parent.
