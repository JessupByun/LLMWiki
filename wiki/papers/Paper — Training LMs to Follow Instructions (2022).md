---
type: paper
status: in-progress
updated: 2026-09-02
summary: "InstructGPT - RLHF applied to GPT-3 at scale, showing a 1.3B fine-tuned model beats 175B raw GPT-3 on human preference, and setting the SFT-to-RM-to-PPO recipe every instruction-tuned model since has run."
cluster: ["Post-training alignment", "Foundational canon"]
sources: ["raw/papers/Paper — Training LMs to Follow Instructions (2022).pdf"]
tags: [rlhf, alignment, post-training, instruction-tuning, reward-modeling, ppo]
aliases: ["arxiv:2203.02155", "Ouyang et al. 2022", "InstructGPT"]
---

# Paper — Training LMs to Follow Instructions (2022)

Commonly known as **InstructGPT**.

## Metadata

- **arXiv:** 2203.02155 (v1, 2022-03-04)
- **Venue:** commonly cited as NeurIPS 2022; no venue is printed in the PDF itself and this has not been independently verified against DBLP in this session.
- **Authors:** Long Ouyang, Jeff Wu, Xu Jiang, Diogo Almeida, Carroll L. Wainwright, Pamela Mishkin, Chong Zhang, Sandhini Agarwal, Katarina Slama, Alex Ray, John Schulman, Jacob Hilton, Fraser Kelton, Luke Miller, Maddie Simens, Amanda Askell, Peter Welinder, Paul Christiano, Jan Leike, Ryan Lowe (OpenAI). A joint project of the OpenAI Alignment team; Ryan Lowe and Jan Leike are the team leads.

## Impact

InstructGPT is the paper that proved RLHF works at the scale of an actual frontier language model, not just Atari and simulated robots.
The headline result: labelers preferred outputs from a **1.3B-parameter** InstructGPT model to outputs from the **175B-parameter** GPT-3 - over 100x fewer parameters, and still preferred 85 ± 3% of the time head-to-head.
This is the direct ancestor of ChatGPT, and it is what turned "fine-tune GPT-3 on human preferences" from a promising idea into the standard recipe every subsequent instruction-tuned model runs: supervised fine-tuning, then a reward model, then PPO against that reward model.

## Problem it solved

GPT-3-scale pretraining optimizes one objective - predict the next token on internet text - which is not the same objective as "follow the user's instructions helpfully and safely."
The paper calls this a misalignment between the training objective and actual intent, and it is why large, capable models still produce outputs that are "untruthful, toxic, or simply not helpful."
Making a model bigger, on its own, does not fix this: GPT-3 already had the capability to do many of these tasks well, it simply was not being pointed at the right objective.

## Core idea: RLHF, extended from Christiano et al. to language models

The method is the three-step loop from [[Paper — Deep RL from Human Preferences (2017)]], applied to text generation instead of trajectory clips (Figure 2 in the paper):

1. **Supervised fine-tuning (SFT).** Human labelers write demonstrations of the desired output for a given prompt; GPT-3 is fine-tuned on these directly.
2. **Reward modeling (RM).** Labelers rank 4-9 model outputs per prompt from best to worst. A 6B reward model is trained to predict these rankings, using a cross-entropy loss over **all** `C(K,2)` pairwise comparisons from each ranking as a single batch element - shuffling comparisons into independent pairs was tried first and caused the RM to overfit within one pass, because comparisons from the same prompt are highly correlated.
3. **RL against the reward model (PPO).** The SFT model is fine-tuned with [[Method — Proximal Policy Optimization (PPO)]] to maximize the reward model's score, with a per-token KL penalty pulling the policy back toward the SFT model so it cannot drift arbitrarily far while chasing reward.

Concretely: rather than a human ever writing "reward = +1 for helpful, -1 for unhelpful," the reward model learns what "better" looks like from thousands of pairwise judgments, the same Bradley-Terry-style move [[Concept — Bradley-Terry preference model]] documents - only now the two things being compared are full model responses to a prompt like "Write a story about a wise frog," not two seconds of simulated robot motion.

## How it's built

- **Alignment target:** the paper explicitly frames the goal via Askell et al.'s **helpful, honest, harmless** criteria, and instructs labelers to prioritize truthfulness and harmlessness over helpfulness in final evaluation, even though training data prioritized helpfulness.
- **RL objective (PPO-ptx):** `reward(x,y) - β·log(π_RL(y|x)/π_SFT(y|x)) + γ·E[log π_RL(x)]` over pretraining data - the middle term is the per-token KL penalty (β = 0.02) against the SFT reference policy, and the last term mixes in gradients from the original GPT-3 pretraining distribution (γ = 27.8) specifically to counteract capability regressions introduced by RLHF fine-tuning. Setting γ = 0 recovers the plain "PPO" ablation used throughout the paper's comparisons.
- **Data:** ~13k SFT prompts, ~33k RM prompts, ~31k PPO prompts, drawn from a mix of labeler-written prompts (used to bootstrap the very first instruction-following model) and real prompts submitted to that model on the OpenAI API Playground - not synthetic academic-benchmark tasks. Over 96% English; generation and open-ended brainstorming, not classification or QA, dominate real usage (Table 1).
- **Labelers:** ~40 contractors, screened specifically for their ability to identify and handle sensitive content, with detailed written instructions and a shared chat room for edge cases.

## Results (highlights)

- **Preference win rate:** 175B InstructGPT beats 175B GPT-3 85 ± 3% of the time, and beats a carefully prompted few-shot GPT-3 71 ± 4% of the time.
- **Truthfulness:** roughly 2x more truthful and informative on TruthfulQA than GPT-3; hallucinates on closed-domain tasks (making up facts not present in the input) about half as often - 21% vs. 41%.
- **Toxicity, but not bias:** ~25% fewer toxic outputs than GPT-3 when explicitly prompted to be respectful, but no significant improvement over GPT-3 on Winogender or CrowS-Pairs bias benchmarks. When prompted to be *maximally biased*, InstructGPT is reliably **more** toxic than equivalently-sized GPT-3.
- **Alignment tax, and how they fixed it:** plain PPO regresses performance on public NLP benchmarks (SQuADv2, DROP, HellaSwag, WMT translation) relative to GPT-3. Mixing in pretraining gradients (PPO-ptx) mitigates most of this without giving up the preference-score gains.
- **Cost:** training the 175B PPO-ptx model took ~60 petaflops/s-days, against ~3,640 for the original GPT-3 pretraining run - RLHF fine-tuning is a small fraction of pretraining cost, yet moved preference scores more than a 100x parameter increase did.
- **Generalization beyond the fine-tuning distribution:** despite non-English languages and code being a tiny fraction of the fine-tuning data, InstructGPT can follow instructions in French and Swedish (sometimes replying partly in English) and reliably answer questions about code, while GPT-3 requires much more careful prompting to do the same.
- **Holds up for held-out labelers:** labelers who contributed none of the training data show the same preference ranking over models, evidence the result is not just the model overfitting to a handful of annotators' idiosyncratic taste.

## Why it endures

This paper is the template. Every later instruction-tuned or "helpful assistant" release descends from the SFT → RM → PPO structure demonstrated here, and the specific engineering fixes it introduces - a KL penalty against the reference policy, and mixing pretraining gradients back into RL fine-tuning to control the alignment tax - are now standard practice rather than novel contributions.
It is also the empirical result [[Paper — Language Models are Few-Shot Learners (2020)]] anticipated without demonstrating: GPT-3's own limitations section named "fine-tuning with reinforcement learning" as the needed next step, and this is that step, carried out.

## Whose preferences is this aligning to?

Worth isolating as its own question, because the paper is unusually candid about it rather than treating "aligned with human preferences" as self-evidently well-defined.
The model is aligned to a specific, narrow chain: the ~40 hired labelers (mostly English-speaking, based in the US or Southeast Asia, inter-labeler agreement only ~73%), filtered through instructions written by the researchers, applied to prompts drawn from OpenAI API customers - a population seeded from OpenAI's own employee network via an early waitlist, not a representative sample of eventual users.
The paper explicitly declines to claim any of these groups is the "right" source of preferences, and flags that aligning to the *average* labeler preference is itself a design choice with costs - e.g. when text disproportionately affects a minority group, weighting that group's preferences more heavily might be preferable to averaging over everyone.

## Limitations

- **Still makes simple mistakes:** can assume a false premise in an instruction is true rather than challenging it, overly hedges on questions that have a fairly clear answer, and degrades when instructions stack multiple explicit constraints.
- **Follows harmful instructions rather than refusing them.** The paper names this as perhaps the model's greatest limitation - InstructGPT was trained to prioritize helpfulness, so it complies with requests for toxic or biased content more readily than declining would require, and produces *more* toxic output than GPT-3 when explicitly asked to be maximally biased.
- **Most comparisons are single-labeled** for cost reasons, so places where labelers would have disagreed are largely invisible in the training signal.

## Relations

- Direct extension of [[Paper — Deep RL from Human Preferences (2017)]] from robotics/Atari clip comparisons to LLM response comparisons; both share the underlying [[Method — Reinforcement learning from human feedback (RLHF)]] loop.
- RL step: [[Method — Proximal Policy Optimization (PPO)]], introduced in [[Paper — Proximal Policy Optimization (2017)]].
- Reward-model likelihood: [[Concept — Bradley-Terry preference model]].
- Applies RLHF to the architecture and model class from [[Paper — Language Models are Few-Shot Learners (2020)]], whose own limitations section named this exact fix as future work.
- **Tension / update** with [[Concept — Reward hacking]]: the 2017 paper's fix for reward-model over-optimization was to keep collecting human feedback *online* as the policy changes. InstructGPT instead relies mainly on a fixed-dataset reward model plus a KL penalty holding the policy near the SFT reference - a different mitigation for the same failure mode, worth tracking as a live design choice rather than a settled one.

## Up

- [[Cluster — Post-training alignment]]
- [[Cluster — Foundational canon]]
