---
type: paper
status: mature
updated: 2026-09-19
summary: "Trains a 7B open model to 51.7% on the competition-level MATH benchmark - rivaling GPT-4 - via a 120B-token web-mined math corpus and Group Relative Policy Optimization (GRPO), a PPO variant that drops the value network entirely and estimates advantage from relative scores within a sampled group."
cluster: ["Reinforcement learning"]
sources: ["raw/papers/reinforcement-learning/Paper — DeepSeekMath - Pushing the Limits of Mathematical Reasoning in Open Language Models (2024).pdf"]
tags: [rl, grpo, mathematical-reasoning, open-weights]
aliases: ["arxiv:2402.03300", "Shao et al. 2024"]
---

# Paper — DeepSeekMath: Pushing the Limits of Mathematical Reasoning in Open Language Models (2024)

## Metadata

arXiv:2402.03300 (v3, Apr 2024).
Zhihong Shao, Peiyi Wang, Qihao Zhu, Runxin Xu, and collaborators.
DeepSeek-AI, with Tsinghua University and Peking University.

## Impact

DeepSeekMath 7B reaches 51.7% on the competition-level MATH benchmark without external tools or voting, approaching Gemini-Ultra and GPT-4 despite being dramatically smaller - the first open-source model to cross 50% on MATH.
The paper's lasting contribution to this wiki's RL literature is **Group Relative Policy Optimization (GRPO)**: a variant of PPO that removes the learned value network entirely, estimating each response's advantage from its relative standing within a group of sampled responses to the same question instead.

## Problem it solved

PPO-based RLHF requires training and holding in memory a value network roughly the same size as the policy being trained, purely to produce a per-token baseline for variance reduction - a substantial memory and compute cost.
This cost is especially wasteful in settings like mathematical reasoning, where the reward signal is typically a single correct/incorrect judgment attached to the very last token of a full response, making an accurate *per-token* value function hard to learn and arguably unnecessary in the first place.

## Core idea

For each question, sample a *group* of G responses from the current policy rather than one, score each with the reward model or a rule-based checker, and use the group's own mean and standard deviation to normalize each response's reward into an advantage: `A_i = (r_i - mean(r)) / std(r)`.
A concrete example: ask the model to solve one MATH problem 64 times; if 40 of those attempts get the right final answer and 24 don't, each correct attempt receives a positive advantage proportional to how far above the group's average correctness rate it sits, and each incorrect attempt a negative one - with no separate value network ever trained to estimate what "should" have happened.
This is exactly the comparison structure a preference-based reward model is already trained on (which response is better, among a set produced for the same prompt), so GRPO's group-relative advantage estimate aligns naturally with how such reward models learn in the first place.

## How it's built

GRPO keeps PPO's clipped surrogate objective and its per-token structure, but replaces the GAE-based advantage (which needs the value network) with the group-normalized reward, and moves the KL penalty against a reference policy out of the reward signal and directly into the loss term, using an unbiased KL estimator rather than the usual per-token penalty trick.
The paper distinguishes **outcome supervision** (the whole group-normalized reward is assigned uniformly to every token in a response) from **process supervision** (a step-level reward model scores each reasoning step, and a token's advantage is the sum of the normalized rewards for every later step), and adds an **iterative** variant that periodically retrains the reward model on fresh policy rollouts rather than freezing it at the start of RL.
DeepSeekMath itself is built in three stages: continued pretraining of a code-trained 7B base model on a 120B-token math corpus mined from Common Crawl via an iterative fastText-classifier pipeline, supervised fine-tuning on 776K math instruction examples, then GRPO applied on top using only the GSM8K/MATH subset of that SFT data.

## Results

GRPO applied to DeepSeekMath-Instruct 7B lifts GSM8K from 82.9% to 88.2% and MATH from 46.8% to 51.7%, and these gains generalize to benchmarks with no RL training data at all (e.g. CMATH: 84.6% to 88.8%), despite RL training on only English GSM8K/MATH questions.
A Pass@K-versus-Maj@K analysis shows RL raises the majority-vote accuracy (Maj@K) far more than the best-of-K accuracy (Pass@K) - evidence that GRPO is sharpening which of the policy's already-latent correct answers gets sampled most often, rather than teaching the model new problem-solving capability outright.
Process supervision beats outcome supervision, online sampling beats offline (RFT-style) sampling, and iterative reward-model retraining further improves results - and, separately, code pretraining measurably transfers to mathematical reasoning even without tool use, while adding arXiv papers to the pretraining mix shows no measurable benefit on any benchmark tested.

## Why it endures

GRPO's memory savings - no value network, no GAE - make large-scale RL fine-tuning of LLMs meaningfully cheaper, and this is the paper's primary reason for enduring relevance: GRPO went on to become the RL algorithm behind DeepSeek-R1's reasoning breakthrough, making this paper the direct technical ancestor of the RL-on-verifiable-rewards paradigm that defines the current reasoning-model era.
Its unified gradient-coefficient framing of SFT, RFT, DPO, PPO, and GRPO as variations on the same underlying update rule is also a clean pedagogical contribution independent of GRPO itself.

## Limitations

The Pass@K-versus-Maj@K finding is a double-edged result: the paper's own reading is that this run of GRPO improved sampling efficiency over an already-present capability rather than expanding what the model could solve at all, and the authors explicitly flag this as a target for future work rather than treating it as settled.
The RL phase trains on only the GSM8K/MATH subset of the SFT data, so its out-of-domain generalization findings are demonstrated on a narrow slice of possible reward sources; the reward model used throughout is itself trained on rule-derived correctness labels rather than open-ended human preference data.
The paper is explicit that its "arXiv doesn't help" finding is narrow - untested on informalization tasks, at larger model scale, or in combination with other data types - and should not be over-generalized.

## Relations

- A variant of [[Method — Proximal Policy Optimization (PPO)]], introduced in [[Paper — Proximal Policy Optimization (2017)]]; keeps PPO's clipped objective but replaces its value-network-based advantage estimate with group-relative reward normalization.
- Introduces [[Method — Group Relative Policy Optimization (GRPO)]].
- Directly reuses [[Method — Chain-of-Thought Prompting]] and [[Paper — Let's Verify Step by Step (2023)]]'s process-versus-outcome-supervision distinction, testing both under GRPO rather than only outcome supervision.
- Positions itself against [[Paper — Direct Preference Optimization (2023)]] (DPO) within its own unified paradigm, showing DPO is a special case of the same general gradient-coefficient framework GRPO and PPO also instantiate.
- Conceptually descends from STaR (Zelikman et al. 2022, not yet ingested in this wiki) - bootstrapping a model's reasoning by training on its own self-generated correct rationales - though DeepSeekMath frames this lineage through the RL literature (RFT, online RFT) rather than citing STaR directly.

## Up

- [[Cluster — Reinforcement learning]]
