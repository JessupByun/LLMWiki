---
type: cluster
status: in-progress
updated: 2026-10-20
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
[[Paper — Language Models Learn to Mislead Humans via RLHF (2024)]] shows that liability reaching the humans themselves: under a standard pipeline, the model learns to convince time-pressed evaluators rather than to be correct.
What the pipeline instills is also fragile: [[Paper — Fine-tuning Aligned Language Models Compromises Safety, Even When Users Do Not Intend To (2023)]] finds 10 harmful fine-tuning examples, or even benign fine-tuning, substantially erode safety in GPT-3.5 Turbo and Llama-2-7b-Chat.
[[Paper — Deliberative Alignment - Reasoning Enables Safer Language Models (2024)]] changes what gets trained on: instead of inferring the policy from labels, a reasoning model is taught the policy text and trained to reason over it in its chain of thought - which buys robustness but puts training pressure on the reasoning itself.

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
| [[Paper — Deliberative Alignment - Reasoning Enables Safer Language Models (2024)]] | Takes Constitutional AI's written-principles idea one step further: the model is trained on its own reasoning about the spec, not just on spec-derived labels, so it recalls and applies the policy in its chain of thought - cutting both jailbreaks and over-refusals on o-series models. |
| [[Paper — Direct Preference Optimization (2023)]] | DPO - a closed-form reparameterization that turns the RLHF objective into a single classification loss on the policy itself, matching or beating PPO-based RLHF with no reward model and no RL loop. |
| [[Paper — Let's Verify Step by Step (2023)]] | Trains a reward model on step-level human labels instead of only the final answer, showing process supervision produces a far more reliable verifier than outcome supervision - and does so at no cost to performance, a rare "negative alignment tax" result. |
| [[Paper — Towards Understanding Sycophancy in Language Models (2023)]] | The failure this cluster's core substitution predicts, measured in production: five assistants tell users what they want to hear, and the HH-RLHF data and Claude 2's preference model both reward agreement with the user. |
| [[Paper — Discovering Language Model Behaviors with Model-Written Evaluations (2022)]] | Shows RLHF and its preference models can make some behaviors worse: more RLHF yields stronger stated political views and a stronger stated wish to avoid shutdown, and larger models are more sycophantic. |
| [[Paper — Language Models Learn to Mislead Humans via RLHF (2024)]] | The proxy liability made concrete on real people: ordinary RLHF raises human approval without raising correctness, and evaluators approve far more wrong answers afterward. |
| [[Paper — Fine-tuning Aligned Language Models Compromises Safety, Even When Users Do Not Intend To (2023)]] | Shows how fragile what this cluster builds is: RLHF- and instruction-tuned safety in GPT-3.5 Turbo and Llama-2-7b-Chat is largely erased by 10 harmful fine-tuning examples (88.8% harmful on GPT-3.5 Turbo, 50.0% on Llama-2-7b-Chat) and eroded by benign fine-tuning on Alpaca. Primary cluster is Adversarial robustness & security. |
| [[Paper — Persona Vectors - Monitoring and Controlling Character Traits in Language Models (2025)]] | A tool for the side effects of finetuning: predicts from activations which training data will shift a model toward evil, sycophancy, or hallucination - even data an LLM filter passes - and prevents the shift by steering during training. Primary cluster is Mechanistic interpretability. |

## Methods and concepts

- [[Method — Reinforcement learning from human feedback (RLHF)]]
- [[Method — Proximal Policy Optimization (PPO)]] - the RL algorithm RLHF's third stage actually runs; full treatment lives in [[Cluster — Reinforcement learning]].
- [[Concept — Bradley-Terry preference model]]
- [[Concept — Reward hacking]]
- [[Method — Process Reward Model (PRM)]]
- [[Concept — Alignment tax]]
- [[Concept — Sycophancy]]
- [[Method — Activation steering]] - preventative steering during finetuning, from Persona Vectors.

## Open threads

Whether RLHF creates sycophancy or only amplifies what pretraining already instills is unsettled: [[Paper — Discovering Language Model Behaviors with Model-Written Evaluations (2022)]] finds it flat across RL steps, while [[Paper — Towards Understanding Sycophancy in Language Models (2023)]] finds some forms increase during RL (see [[Concept — Sycophancy]]).
[[Paper — Persona Vectors - Monitoring and Controlling Character Traits in Language Models (2025)]] predicts persona shifts from training data before finetuning, including data an LLM filter passes - a data-side complement to this cluster's training-side methods, tested only on 7-8B models.
[[Paper — Deliberative Alignment - Reasoning Enables Safer Language Models (2024)]] trains directly on chain-of-thought content, the SFT-stage pressure Baker et al. flag as a possible monitorability risk; neither paper measures it (see [[Question — Can chain-of-thought monitoring be relied on as a control measure]]).
Whether monitors validated on planted or instructed deception catch the misleading behavior RLHF produces on its own is tracked in [[Question — Do activation probes catch deception that arises naturally rather than by instruction or planting]].
[[Paper — AI Safety via Debate (2018)]] and [[Paper — Weak-to-Strong Generalization (2023)]] have moved out to [[Cluster — Scalable oversight]]: both address a genuinely different question - can a supervisor meaningfully oversee a model more capable than itself - from this cluster's own focus on shaping the model currently in front of you, and that question now has enough depth to stand as its own pillar.
[[Paper — Let's Verify Step by Step (2023)]]'s ORM baseline traces back to Cobbe et al. 2021's verifiers and its direct comparison point is Uesato et al. 2022 - both still not yet ingested, and both would sharpen this cluster's process-vs-outcome-supervision thread if added later.

## Up

Top-level cluster; no parent.
