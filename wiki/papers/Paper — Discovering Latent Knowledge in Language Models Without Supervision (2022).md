---
type: paper
status: mature
updated: 2026-10-19
summary: "Introduces Contrast-Consistent Search (CCS), which finds a direction in a language model's activations that tracks truth using no labels and no model outputs - only the logical constraint that a statement and its negation should get opposite truth values - beating zero-shot accuracy by about 4 points on average across 6 models and 10 datasets, transferring across tasks, and holding up when the model is prompted to answer incorrectly."
cluster: ["Mechanistic interpretability"]
sources: ["raw/papers/mechanistic-interpretability/Paper — Discovering Latent Knowledge in Language Models Without Supervision (2022).pdf"]
tags: [interpretability, probing, latent-knowledge, truthfulness, eliciting-latent-knowledge]
aliases: ["arxiv:2212.03827", "Burns et al. 2022", "CCS", "Contrast-Consistent Search"]
---

# Paper — Discovering Latent Knowledge in Language Models Without Supervision (2022)

Commonly known as **CCS** (Contrast-Consistent Search).

## Metadata

arXiv:2212.03827 (v2, Mar 2024; originally Dec 2022).
Collin Burns (UC Berkeley) and Haotian Ye (Peking University) as equal contributors, with Dan Klein and Jacob Steinhardt (UC Berkeley).

## Impact

Training a model to say true things requires someone to know what's true: imitation learning copies human mistakes, and rewarding outputs that *look* true teaches the model to produce falsehoods evaluators can't catch.
This paper proposes skipping the model's outputs entirely and reading what it "believes" directly from its activations, without any labels.
That reframing - separating what a model knows from what it says - is an early empirical attack on the previously posed problem of eliciting latent knowledge (ELK), and a reason to think a lie detector might work even when a model's outputs are deliberately misleading.
Collin Burns went on to lead [[Paper — Weak-to-Strong Generalization (2023)]], which cites this work as one of the field's methods for discovering latent knowledge.

## Problem it solved

Every existing way to make models truthful relied on supervision - human demonstrations or human ratings - so it inherited the supervisor's limits, and as models grow more capable those limits bite harder.
Because the problem lies in the training objective rather than the model's capability, scaling alone won't fix it.

## Core idea

Truth has structure few other features share: a statement and its negation can't both be true.
**Contrast-Consistent Search** exploits that.
For each yes-no question, build a **contrast pair** - the question answered "Yes" and the same question answered "No" - and take the model's hidden states for both.
Normalize each half of the pairs separately, so the probe can't simply learn the difference between the words "Yes" and "No."
Then train a small linear probe mapping a hidden state to a probability of being true, with an unsupervised loss that rewards two things: **consistency** (the two probabilities in a pair should sum to one) and **confidence** (they shouldn't both sit at 0.5, which would trivially satisfy consistency).
No labels and no model outputs are used; the only remaining ambiguity, which end of the direction means "true," is resolved by a separate argument in the appendix (in the main evaluations, the better of the two labelings is reported).

## How it's built

Six models spanning architectures - T5, UnifiedQA, and T0 (encoder-decoder), GPT-J (autoregressive), RoBERTa and DeBERTa (encoder-only) - and ten datasets covering sentiment, topic classification, natural language inference, story completion, question answering, and commonsense reasoning, each with 8-13 prompts.
Baselines are calibrated zero-shot prompting and, as a ceiling, a supervised logistic-regression probe on the same hidden states.

## Results

CCS averages 71.2% accuracy versus 67.2% for calibrated zero-shot, beating zero-shot on every model except RoBERTa (2 points worse) and T0, which was trained on most of these datasets and is excluded from averages; it also roughly halves sensitivity to prompt wording.
When UnifiedQA is prompted with a prefix of incorrectly answered questions, its zero-shot accuracy drops 9.5 points (80.4% to 70.9%) while CCS accuracy holds (82.1% to 83.8%).
A probe trained on one dataset often transfers to completely different tasks with different label words, sometimes beating training on the target task itself - evidence for something like a task-agnostic representation of truth.
CCS sometimes works best on middle layers rather than the final one, and works on masked language models even when their outputs are uninformative, suggesting it finds information the outputs don't express.
Truth directions are also *salient*: CCS often works with very little data, and simply taking the top principal component of the contrast-pair differences (or clustering them) is competitive, though CCS performs best.

## Why it endures

It reframed eliciting latent knowledge, which ELK had posed as a worst-case theoretical problem, as an empirical one with a working method on current models.
The contrast-pair-and-difference idea recurs throughout later activation-reading work: [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]] takes the top principal component of contrastive differences as its core method and compares itself directly against CCS, outperforming it by a wide margin in that paper's comparison.

## Limitations

The method only works if a direction separating true from false inputs exists in the activations - which requires the model to be able to evaluate the input's truth *and* to actually be evaluating it - and it's unclear when that holds.
The authors explicitly did not test it on a model actively lying or deceiving, because no such evaluation setup existed; they name lie detection in such a setting as a good stress test.
They leave open in what sense CCS finds "truth" rather than just a high-accuracy direction, and whether it recovers the truth of an input rather than what the model would say - a question later critiques press on - and leave a gap to the supervised ceiling.
Its results cover yes-no and binary-classification questions with clear-cut answers only.

## Relations

- The empirical starting point for the latent-knowledge probing that [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]] extends and benchmarks against, and a top-down complement to the bottom-up feature work of [[Method — Sparse Autoencoder (SAE)]].
- Cited by [[Paper — Weak-to-Strong Generalization (2023)]] (same first author) as a method for discovering latent knowledge; both are attempts to get past the limits of human supervision, here by reading activations rather than eliciting capability through weak labels.
- Motivated by the same training-objective failure that [[Concept — Reward hacking]] describes - models rewarded for outputs evaluators can't check learning to produce convincing falsehoods - and by [[Concept — Scalable oversight]]'s supervision gap.
- A candidate tool for [[Concept — Deceptive instrumental alignment]]: a model whose outputs mislead may still represent the truth internally, though the paper itself never tests this on a deceptive model.
- [[Paper — Model Evaluation for Extreme Risks (2023)]] names detecting when a model's outputs misreport its knowledge, citing this paper, as one mechanistic route to catching deceptive behavior that behavioral evaluations miss.

## Up

- [[Cluster — Mechanistic interpretability]]
