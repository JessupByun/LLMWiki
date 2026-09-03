---
type: method
status: in-progress
updated: 2026-09-02
summary: "Fit a reward model to human preference comparisons, then optimize a policy against it with RL - the standard alignment pipeline."
cluster: ["Post-training alignment"]
sources: []
tags: [rlhf, alignment, reward-modeling, rl]
aliases: ["RLHF"]
---

# Method — Reinforcement learning from human feedback (RLHF)

## Definition

A three-stage procedure for training a policy toward an objective nobody can write down:

1. **Collect comparisons.** Show a human two candidate outputs and record which they prefer. Not scores, not demonstrations - just a binary choice, because relative judgments are the thing humans do reliably.
2. **Fit a reward model.** Train a network to predict those preferences, usually via the [[Concept — Bradley-Terry preference model]] with a cross-entropy loss. The reward model is a *learned proxy for human judgment*.
3. **Optimize the policy** with RL against the reward model's output rather than against any environment reward. In practice this step is run with [[Method — Proximal Policy Optimization (PPO)]].

Introduced for deep RL in [[Paper — Deep RL from Human Preferences (2017)]]; demonstrated at LLM scale, with this exact three-stage structure, in [[Paper — Training LMs to Follow Instructions (2022)]], which is now the default post-training recipe for language models.

## Why it works

The whole method is an economic argument.
Human feedback is the expensive resource and environment interaction is cheap, so the goal is to spend as few human judgments as possible.
Using humans *directly* as the reward function forces one human judgment per environment step, which is hopeless at the millions of steps deep RL needs.
Inserting a learned reward model decouples the two: the human labels a few hundred comparisons, the reward model generalizes to score the millions of states the human never saw.

The reason preferences beat absolute scores is that humans are poorly calibrated at assigning numbers and well calibrated at ranking pairs.
Asking "is this trajectory worth 6.5 or 7.2?" produces noise; asking "which of these two is better?" produces signal.

## The central liability

RLHF does not optimize human preferences.
It optimizes *a model of* human preferences, and the gap between those two is where the whole safety literature lives.
An RL policy is an aggressive optimizer and will find inputs where the reward model is wrong - see [[Concept — Reward hacking]].

The standard mitigation, established in the original paper, is to keep collecting feedback **online** as the policy changes, so the reward model gets corrected precisely in the regions the policy has newly learned to exploit.
Training the reward model once on a fixed dataset and then optimizing hard against it reliably fails.

**Tension / update.** [[Paper — Training LMs to Follow Instructions (2022)]], running this exact pipeline at LLM scale, does not lean primarily on online feedback - most of its comparison data comes from the supervised policy rather than a continuously retrained loop.
Its main defense against reward-model over-optimization is instead a per-token KL penalty holding the policy close to the supervised fine-tuned reference model, done through [[Method — Proximal Policy Optimization (PPO)]]'s RL objective.
Whether KL regularization against a fixed reference is a genuine substitute for online correction, or just a different way of delaying the same failure, is an open question worth tracking as more RLHF pipelines report their own choices here.

## Relations

- Origin: [[Paper — Deep RL from Human Preferences (2017)]].
- RL step: [[Method — Proximal Policy Optimization (PPO)]].
- Preference likelihood it depends on: [[Concept — Bradley-Terry preference model]].
- Its characteristic failure: [[Concept — Reward hacking]].
- Applied to the model class introduced in [[Paper — Language Models are Few-Shot Learners (2020)]], whose authors named this as the needed next step; carried out at scale in [[Paper — Training LMs to Follow Instructions (2022)]].

## Up

- [[Cluster — Post-training alignment]]
