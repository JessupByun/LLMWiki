---
type: paper
status: mature
updated: 2026-09-19
summary: "Trains reasoning purely with rule-based RL on a pretrained base model, no SFT initialization at all, and shows long chain-of-thought behaviors (self-verification, backtracking) emerge on their own; the resulting model matches OpenAI o1 on math, code, and STEM benchmarks and is released open-weight."
cluster: ["Reinforcement learning", "Foundational canon"]
sources: ["raw/papers/reinforcement-learning/Paper — DeepSeek-R1 - Incentivizing Reasoning Capability in LLMs via Reinforcement Learning (2025).pdf"]
tags: [rl, grpo, reasoning, post-training, distillation]
aliases: ["arxiv:2501.12948", "DeepSeek-AI 2025"]
---

# Paper — DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning (2025)

## Metadata

arXiv:2501.12948, Jan 2025.
DeepSeek-AI.

## Impact

DeepSeek-R1-Zero shows that a pretrained base model can be trained straight into a strong reasoner using only rule-based RL (correctness of the final answer plus output formatting), with no supervised fine-tuning step at all, and that sophisticated reasoning behaviors - self-verification, reflection, backtracking to try a different approach - emerge on their own rather than being taught by demonstration.
DeepSeek-R1 itself, which adds a light SFT "cold start" and a second RL stage on top of that recipe, reaches AIME 2024 and MATH-500 scores on par with OpenAI's o1, released fully open-weight under the MIT license - the first open model at that reasoning tier.
The 800k reasoning trajectories it generates are then used to distill much smaller dense models (as small as 1.5B parameters) that beat far larger non-reasoning models, making frontier-style reasoning capability cheap to reproduce without running large-scale RL at all.

## Problem it solved

Before this paper, giving an LLM long chain-of-thought reasoning meant supervised fine-tuning on human-written (or human-distilled) reasoning traces, which caps the model's reasoning style at whatever the demonstrations look like and is expensive to scale, since good multi-step reasoning demonstrations are hard to collect at volume.
The RL-for-reasoning work that did exist (process reward models like [[Paper — Let's Verify Step by Step (2023)]]) relied on a trained neural reward model to judge each step, and neural reward models are themselves expensive to build and vulnerable to reward hacking once large-scale RL is run against them for long enough.
It was also unclear whether reasoning behavior could be induced from scratch by RL alone, without any human-written reasoning demonstrations to imitate in the first place.

## Core idea

Run [[Method — Group Relative Policy Optimization (GRPO)]] directly on a pretrained base model (DeepSeek-V3-Base) with no SFT step beforehand, using a reward that is entirely rule-based: does the final answer match the ground truth (checked automatically - a boxed number for math, test cases for code), and does the response wrap its reasoning in `<think>` tags.
No learned reward model of any kind is used for this stage, which sidesteps reward hacking entirely since there is no learned function to hack.
Because the only signal is whether the final answer is correct, the model is free to discover whatever intermediate reasoning process actually raises its reward, rather than being constrained to reproduce a human's reasoning style - and in practice, its average response length climbs steadily over training as it learns that generating more verification and backtracking steps improves accuracy on hard problems, culminating in a documented "aha moment" where the model spontaneously starts using self-reflective language ("Wait, wait. Wait. That's an aha moment I can flag here.") mid-solution, entirely unprompted.
This RL-only model, DeepSeek-R1-Zero, reaches 77.9% pass@1 on AIME 2024 (up from an initial 15.6%), but its output is often hard to read and mixes languages mid-response, and its narrow rule-based reward doesn't transfer to open-ended tasks like writing.

## How it's built

DeepSeek-R1 fixes those readability and generality gaps with a four-stage pipeline built around the same GRPO core: (1) fine-tune the base model on a small set of human-readable, first-person long chain-of-thought examples as a "cold start," giving RL a better initialization than the raw base model; (2) run the same reasoning-focused RL as R1-Zero, adding a language-consistency reward term to stop language mixing; (3) use that RL checkpoint to generate rejection-sampled reasoning trajectories, combine them with ~200k non-reasoning SFT examples (writing, factual QA, coding), and fine-tune on all ~800k samples together so the model retains general capability; (4) run a second RL stage that mixes the same rule-based reward for reasoning prompts with a learned helpfulness/harmlessness reward model for general prompts, aligning the final model with human preferences without eroding the reasoning gains from stage 2.
Because DeepSeek-R1's 800k SFT trajectories are themselves generated by an RL-trained reasoner, they can be reused directly to fine-tune much smaller open base models (Qwen and Llama, 1.5B-70B) with ordinary SFT and no RL stage at all - a distillation pipeline that transfers the reasoning behavior far more cheaply than reproducing the RL training would.

## Results

DeepSeek-R1 hits AIME 2024 pass@1 79.8% (o1-1217: 79.2%), MATH-500 97.3%, and the 96.3rd Codeforces percentile - putting it ahead of the average human competitor on both AIME and Codeforces.
Its distilled 32B dense model (SFT-only, no RL) beats QwQ-32B-Preview and a version of Qwen2.5-32B given the same large-scale RL training directly, showing distillation from a strong reasoner outperforms trying to reproduce the RL recipe on a smaller base model from scratch.
The 1.5B distilled model alone surpasses GPT-4o and Claude-3.5-Sonnet on AIME and MATH-500, despite being orders of magnitude smaller.

## Why it endures

This paper established the "pure RL on a rule-verifiable reward, no SFT initialization" recipe (later called RLVR) as a viable and reproducible way to train reasoning models, avoiding the reward-hacking failure mode that dogged learned process/outcome reward models by simply not using one.
It also demonstrated, empirically and cheaply, that once a large model discovers a reasoning capability via RL, that capability transfers to much smaller models through ordinary SFT distillation - making strong reasoning models accessible to anyone with distillation-scale compute rather than RL-training-scale compute, which is the model the community around Open-R1 and similar reproductions has since followed.

## Attempted approaches that failed

The paper documents two approaches explored before settling on the outcome-based GRPO recipe, and reports why each was abandoned rather than omitting them.
**Process reward models**, in the style of Let's Verify Step by Step, were dropped because defining a general fine-grained reasoning step is hard, automated step-correctness labeling is unreliable, and once a neural PRM is introduced it reliably gets reward-hacked under large-scale RL.
**Monte Carlo Tree Search**, modeled on AlphaGo/AlphaZero, was dropped because token generation's search space is exponentially larger than a board game's, and training a fine-grained value model to guide that search proved much harder to bootstrap than it was for Go.

## Limitations

DeepSeek-R1 still mixes languages when queried outside Chinese and English, has weaker structured-output and tool-use ability than contemporaneous non-reasoning models, and shows only marginal improvement over DeepSeek-V3 on software-engineering benchmarks since large-scale RL was not extensively applied to that domain due to slow evaluation loops.
The paper's own safety report also finds DeepSeek-R1 without its external risk-control system is comparably or more jailbreakable than several frontier models, and performs poorly on HarmBench's intellectual-property category specifically (e.g. reproducing song lyrics on request).

## Relations

- Directly scales up [[Method — Group Relative Policy Optimization (GRPO)]], introduced in [[Paper — DeepSeekMath - Pushing the Limits of Mathematical Reasoning in Open Language Models (2024)]] for math-specific fine-tuning, into a general RL-only training regime spanning math, code, STEM, and logic.
- Explicitly rejects the process-reward-model approach of [[Paper — Let's Verify Step by Step (2023)]] in favor of rule-based verifiers, citing reward hacking as the reason a learned reward model was avoided for the reasoning-RL stage.
- Inverts the SFT-then-RL ordering that [[Paper — Training LMs to Follow Instructions (2022)]] established as the standard post-training pipeline: R1-Zero runs RL with no SFT step at all, and DeepSeek-R1's own SFT stages are themselves built from RL-generated data rather than preceding RL.
- Distills its reasoning behavior into open base models from the same open-weights lineage [[Paper — LLaMA - Open and Efficient Foundation Language Models (2023)]] helped establish.

## Up

- [[Cluster — Reinforcement learning]]
- [[Cluster — Foundational canon]]
