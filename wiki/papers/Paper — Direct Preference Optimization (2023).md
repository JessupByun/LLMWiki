---
type: paper
status: in-progress
updated: 2026-09-02
summary: "DPO - a closed-form reparameterization that turns the RLHF objective into a single classification loss on the policy itself, matching or beating PPO-based RLHF with no reward model and no RL loop."
cluster: ["Post-training alignment", "Foundational canon"]
sources: ["raw/papers/Paper — Direct Preference Optimization (2023).pdf"]
tags: [dpo, alignment, post-training, reward-modeling, preferences]
aliases: ["arxiv:2305.18290", "Rafailov et al. 2023", "DPO"]
---

# Paper — Direct Preference Optimization (2023)

Commonly known as **DPO**.

## Metadata

- **arXiv:** 2305.18290 (v3, 2024-07-29)
- **Venue:** NeurIPS 2023
- **Authors:** Rafael Rafailov, Archit Sharma, Eric Mitchell, Stefano Ermon, Christopher D. Manning, Chelsea Finn (Stanford University; Ermon also CZ Biohub)

## Impact

DPO showed that the entire RLHF pipeline - fit a reward model, then run PPO against it - can be replaced by optimizing a single classification loss directly on the language model's own log-probabilities.
No reward model, no sampling from the policy during training, no RL loop at all.
The paper's title is its own thesis: your language model is secretly a reward model, so stop training a separate one.
It matches or beats PPO-based RLHF on sentiment control, summarization, and dialogue, while being dramatically simpler to implement and tune - and it kicked off the still-ongoing move away from RL-based alignment toward direct preference-optimization objectives.

## Problem it solved

Standard RLHF, as run in [[Paper — Training LMs to Follow Instructions (2022)]] and its predecessors, is "a complex and often unstable procedure": fit a reward model to human comparisons, then fine-tune the policy against that reward model with PPO while a KL penalty keeps it near a reference model.
This requires training and holding multiple models in memory (policy, reward model, and often a separate value function), and repeatedly sampling from the policy during RL training, which is expensive and can be unstable - RL fine-tuning of large language models "remains a major practical challenge."
DPO's target was to keep the same underlying objective RLHF optimizes (reward maximization under a KL constraint to a reference policy) while removing the reward model and the RL loop entirely.

## Core idea: your policy already implies a reward function

Take the standard KL-constrained RLHF objective from Equation 3 in the paper.
Its exact optimal solution, for any reward function `r`, has a known closed form: `π_r(y|x) ∝ π_ref(y|x)·exp(r(x,y)/β)`.
Rearranging this equation algebraically expresses the reward as a function of the *policy* instead: `r(x,y) = β·log(π(y|x)/π_ref(y|x)) + const`.

That's the whole trick. Since the Bradley-Terry preference model (the same one [[Concept — Bradley-Terry preference model]] documents) depends only on the *difference* between two rewards, substituting this reparameterization into the Bradley-Terry likelihood makes the unknown normalizing constant cancel out completely.
What's left is a preference probability written purely in terms of the policy and a fixed reference model - no reward model object anywhere:

> L_DPO(π_θ; π_ref) = −E[(x,y_w,y_l)] log σ( β·log(π_θ(y_w|x)/π_ref(y_w|x)) − β·log(π_θ(y_l|x)/π_ref(y_l|x)) )

Concretely: to train, you sample the model's own log-probabilities for the preferred and dispreferred response, take the difference of ratios against the reference model, and push that difference up with a plain binary cross-entropy loss (the whole PyTorch implementation is about ten lines - the paper includes it verbatim).
The gradient has an intuitive shape too: it increases the likelihood of the preferred response and decreases the likelihood of the dispreferred one, weighted by how *wrong* the model's implicit reward currently has that pair ranked - so the update focuses hardest on cases the (implicit) reward model still gets backwards, the same self-correcting property a good reward-model gradient has, just without ever materializing the reward model as a separate object.

## How it's built

- **No RL loop.** DPO needs only a static dataset of `(prompt, preferred, dispreferred)` triples and a reference policy (usually the SFT model). No sampling from the policy during training, no critic/value function.
- **Same preference model as RLHF.** DPO is mathematically equivalent to fitting a reparameterized Bradley-Terry reward model - it inherits the same theoretical guarantees (Section 5 proves any reward function's equivalence class has a unique representative of this form).
- **Hyperparameters:** β = 0.1 by default (0.5 for TL;DR summarization), RMSprop, learning rate 1e-6, batch size 64 - a small, boring hyperparameter surface compared to PPO's clip ratio, KL coefficient, GAE λ, and value-function tuning.
- **Diagnosing PPO's instability (Section 5.2):** the paper shows PPO's actor-critic objective implicitly contains the same normalization term DPO's reparameterization cancels out analytically; without it, the policy-gradient estimate has high variance, which is a concrete reason DPO's optimization is more efficient than PPO's, not just simpler.

## Results (highlights)

- **Reward/KL frontier (controlled sentiment task):** DPO achieves the highest reward at every KL budget, strictly dominating PPO - including PPO given access to the *ground-truth* reward function (PPO-GT), which DPO still beats despite DPO never seeing that reward directly.
- **Summarization (Reddit TL;DR):** DPO's win rate against reference summaries is ~61% at temperature 0, exceeding PPO's best-case ~57%, and DPO is far more robust to sampling temperature - PPO's win rate degrades toward the base model's at high temperature, DPO's does not.
- **Dialogue (Anthropic HH):** DPO is "the only computationally efficient method that improves over the preferred completions in the Anthropic HH dataset," matching the much more expensive Best-of-128 baseline.
- **Generalizes out of distribution:** evaluated on CNN/DailyMail after training only on Reddit TL;DR, DPO still outperforms PPO by a significant margin.
- **GPT-4 judgments validated against 25 human raters:** GPT-4 agrees with humans about as often as humans agree with each other (67-70% vs. 65% human-human agreement), supporting the paper's use of GPT-4 as an automated evaluator throughout - though a documented failure case is included too (Table 10): GPT-4 endorsed DPO's confidently wrong "7+2=9" over the correct ground truth answer of "11," a factual error the automated judge missed entirely.

## Why it endures

DPO is the paper that made "skip the RL step" a mainstream alignment strategy rather than a theoretical curiosity, and its closed-form reward-to-policy mapping is now the starting point for a whole family of RL-free preference-optimization methods that followed it.
It also directly resolves a question [[Concept — Bradley-Terry preference model]]'s page had flagged as an unverified forward-pointer: DPO does exactly what was speculated - exploits the closed-form relationship between the Bradley-Terry likelihood and the KL-regularized optimal policy to optimize preferences without ever fitting an explicit reward model or running RL.

## Limitations

- **Scale is untested in the original paper.** Experiments top out at 6B parameters; the paper explicitly flags scaling DPO to state-of-the-art model sizes as future work.
- **Out-of-distribution generalization is only preliminarily evidenced** - the CNN/DailyMail transfer result is framed as "initial evidence," not a settled claim.
- **Reward over-optimization in the DPO setting is an open question the paper poses rather than answers**: performance decreasing slightly over the course of training in the dialogue experiment (Figure 3, right) is flagged as a possible instance of it, without being resolved.
- **GPT-4-as-judge is prompt-sensitive** - the paper found GPT-4 preferred longer, more repetitive summaries under one prompt phrasing than under another, and had to select the phrasing that best matched human judgments.

## Relations

- Directly targets the pipeline established by [[Paper — Deep RL from Human Preferences (2017)]] and run at LLM scale in [[Paper — Training LMs to Follow Instructions (2022)]], replacing its reward-model-plus-[[Method — Proximal Policy Optimization (PPO)]] stages with a single closed-form loss.
- Reparameterizes and directly builds on [[Concept — Bradley-Terry preference model]] - resolves the forward-pointer that page had flagged pending this paper's ingest.
- Method hub: [[Method — Reinforcement learning from human feedback (RLHF)]], of which DPO is best understood as an RL-free alternative rather than a member.

## Up

- [[Cluster — Post-training alignment]]
- [[Cluster — Foundational canon]]
