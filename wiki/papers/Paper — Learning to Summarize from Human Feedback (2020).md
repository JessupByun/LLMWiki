---
type: paper
status: in-progress
updated: 2026-09-19
summary: "Applies the Deep RL from Human Preferences reward-model-plus-PPO recipe to abstractive summarization at GPT-3 scale, showing a 1.3B feedback-trained model beats the human reference summaries themselves and that optimizing a learned reward model beats optimizing ROUGE."
cluster: ["Post-training alignment", "Foundational canon"]
sources: ["raw/papers/post-training-alignment/Paper — Learning to Summarize from Human Feedback (2020).pdf"]
tags: [rlhf, alignment, reward-modeling, summarization, ppo]
aliases: ["arxiv:2009.01325", "Stiennon et al. 2020"]
---

# Paper — Learning to Summarize from Human Feedback (2020)

## Metadata

- **arXiv:** 2009.01325 (v3, 2022-02-15)
- **Authors:** Nisan Stiennon, Long Ouyang, Jeff Wu, Daniel M. Ziegler, Ryan Lowe, Chelsea Voss, Alec Radford, Dario Amodei, Paul Christiano (OpenAI).
- **Venue:** NeurIPS 2020.

## Impact

This is the bridge between [[Paper — Deep RL from Human Preferences (2017)]]'s RLHF loop (proven on MuJoCo and Atari) and [[Paper — Training LMs to Follow Instructions (2022)]]'s RLHF loop at full instruction-following scale.
It is the first paper to run the collect-comparisons → fit-reward-model → PPO pipeline on GPT-3-class language models doing a real NLP task, and it establishes a result that recurs throughout post-training alignment: a small model optimized against human preferences beats a much larger model optimized only for likelihood.
Several authors (Ouyang, Wu, Radford, Amodei, Christiano) carry directly into [[Paper — Training LMs to Follow Instructions (2022)]], reusing this paper's methodology almost unchanged.

## Problem it solved

Summarization models are conventionally trained to maximize the likelihood of human-written reference summaries, then evaluated with ROUGE, a metric based on n-gram overlap.
Both halves of that pipeline are rough proxies for what actually matters - summary quality as a human would judge it.
Maximum-likelihood training can't distinguish an important factual error from a trivial synonym swap, and ROUGE is well known to correlate poorly with human judgments of quality.
The paper's stated aim is to advance training objectives that "more closely capture the behavior we care about," using summarization as a testbed because it's a subjective task with a long NLP history but no existing automatic metric anyone trusts.

## Core idea

Same three-stage loop as [[Paper — Deep RL from Human Preferences (2017)]], adapted to the batch setting and to text: (1) sample summaries from several policies for a given Reddit post, send pairs to human labelers who pick the better one; (2) train a reward model to predict the log-odds that a candidate summary is the human-preferred one, via the [[Concept — Bradley-Terry preference model]]; (3) treat the reward model's output as a reward and optimize a policy against it with [[Method — Proximal Policy Optimization (PPO)]], with a KL penalty holding the policy close to the supervised-finetuned reference model.

Two changes distinguish this from the contemporaneous Ziegler et al. 2019 predecessor that first tried this at LM scale: moving to a fully **offline/batch** setting (alternate large batches of labeling with retraining, rather than a continuous online loop) and a much more hands-on labeler-quality process (detailed onboarding, a shared chat room, continuous labeler-researcher agreement monitoring), which together get labeler-researcher agreement up to 77%, close to researcher-researcher agreement of 73%.

## How it's built

- **Base models:** GPT-3-style Transformer decoders at 1.3B and 6.7B parameters, initialized from a model supervised-finetuned on the Reddit TL;DR dataset.
- **Reward model:** the supervised baseline plus a randomly initialized linear scalar head, trained on 64,832 human comparisons with the Bradley-Terry cross-entropy loss.
- **Policy training:** PPO with a **separate** value network (not shared with the policy), initialized from the reward model - an ablation in the paper shows this beats a shared value/policy network.
- **KL penalty:** `R(x,y) = r_theta(x,y) - beta * log[pi_RL(y|x) / pi_SFT(y|x)]`, serving both as an entropy bonus and as a guard against the policy drifting into territory the reward model never saw during training.
- **Length control:** filtered training data to a 24-48 token summary window specifically to prevent length from confounding the quality comparison, since longer summaries are otherwise easy to over-reward.

## Results

- The 1.3B human-feedback model beats a 10x larger (12.9B) supervised model on raw preference against the reference summaries (61% vs 43%), and both feedback-trained models beat the human reference summaries themselves.
- **Transfer with no fine-tuning:** the Reddit-trained feedback model, evaluated cold on CNN/DailyMail news articles, nearly matches a model actually fine-tuned on CNN/DM reference summaries.
- **Reward model beats ROUGE as an optimization target:** best-of-N sampling against the learned reward model produces monotonically better human-judged summaries as N grows; optimizing ROUGE the same way plateaus early at a much lower quality ceiling.
- **Over-optimization is real and visible:** pushing PPO harder against an *earlier* checkpoint of the reward model initially improves quality, but past a point the reward model's predicted preference and the actual human preference diverge, and the reward model becomes actively anti-correlated with what humans want. See the paper's own discussion, which is a direct empirical instance of [[Concept — Reward hacking]] and the case for [[Concept — Scalable oversight]] (a static reward model eventually runs out of coverage as the policy shifts under it).
- Reward-model accuracy scales predictably with both data (+1.1% validation accuracy per doubling) and model size (+1.8% per doubling), with the largest (6.7B) reward model approaching single-human agreement rates.

## Why it endures

This paper's exact three-stage recipe - collect comparisons, fit a Bradley-Terry reward model, optimize with PPO under a KL penalty against the SFT policy - is, almost unchanged, the RLHF stage of [[Paper — Training LMs to Follow Instructions (2022)]] two years later.
The separate-value-network ablation and the over-optimization curve are both cited directly in later RLHF scaling work (e.g. Gao, Schulman & Hilton 2022 on reward model overoptimization, still queued).

## Limitations

- Labeler-researcher agreement, even at its best (77%), leaves meaningful noise in the training signal; the authors themselves attribute much of the disagreement to the task being "quite difficult and subjective."
- The paper explicitly does not run an equivalently expensive supervised-demonstration baseline (collecting as much high-quality human-written summary data as comparison data), so it can't fully rule out that comparably expensive supervision would close some of the gap.
- 320 GPU-days for the 6.7B RL run and thousands of labeler-hours; the paper is upfront that this cost is a real barrier to replication and iteration.
- Results are English-only, Reddit-domain-skewed (two-thirds of the training posts are relationship-advice content), and summary-length-confounded until an explicit length-controlled reanalysis is applied.

## Relations

- Runs the exact loop introduced in [[Paper — Deep RL from Human Preferences (2017)]], adapted from short video clips to text summaries.
- Reward model built on [[Concept — Bradley-Terry preference model]]; policy optimized with [[Method — Proximal Policy Optimization (PPO)]].
- Direct methodological predecessor of [[Paper — Training LMs to Follow Instructions (2022)]], which several of the same authors carry forward, scaling this exact recipe from summarization to general instruction-following.
- Its over-optimization curve is an empirical case of [[Concept — Reward hacking]] and motivates the open [[Concept — Scalable oversight]] question this wiki keeps returning to across post-training alignment papers.

## Up

- [[Cluster — Post-training alignment]]
- [[Cluster — Foundational canon]]
