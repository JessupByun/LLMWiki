---
type: paper
status: mature
updated: 2026-09-19
summary: "Automatically finds a single adversarial suffix that reliably jailbreaks aligned open-source language models and transfers, purely black-box, to production systems including ChatGPT, Bard, and Claude."
cluster: ["Adversarial robustness & security"]
sources: ["raw/papers/adversarial-robustness-and-security/Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023).pdf"]
tags: [adversarial-examples, security, jailbreak, llm, red-teaming]
aliases: ["arxiv:2307.15043", "Zou et al. 2023", "GCG"]
---

# Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)

Commonly known as **GCG** (Greedy Coordinate Gradient), after its optimization method.

## Metadata

Zou, Wang, Carlini, Nasr, Kolter & Fredrikson, Carnegie Mellon University, Center for AI Safety, Google DeepMind & Bosch Center for AI, arXiv:2307.15043, submitted July 27, 2023 (v2 December 20, 2023).

## Impact

The LLM-era sequel to [[Paper — Intriguing Properties of Neural Networks (2013)]]: the first method to automatically, reliably jailbreak aligned language models, and the first to show these jailbreaks **transfer** in a purely black-box way from small open-source models to production systems with completely different architectures and tokenizers.
A single adversarial suffix, optimized only against Vicuna and Guanaco, induces objectionable output from ChatGPT, Bard, Claude, and LLaMA-2-Chat with no direct access to any of them - moving adversarial transferability, previously demonstrated mainly on vision models, decisively into the domain of aligned chat assistants.
The authors disclosed results to OpenAI, Google, Meta, and Anthropic before publication.

## Problem it solved

Before this paper, circumventing an aligned LLM's safety training meant either manual "jailbreaks" - carefully hand-crafted prompts requiring human ingenuity and brittle in practice - or automatic prompt-optimization methods (PEZ, GBDA, AutoPrompt) that had achieved limited or no success against genuinely aligned models.
Two things made automatic attacks hard in this setting specifically: LLMs operate on **discrete** tokens rather than continuous pixels, which both shrinks the effective search space and makes gradient information a poorer guide, and there is no text analogue of an "imperceptible" perturbation - any token swap is visible, so the threat model has to be about eliciting behavior rather than about invisibility.

## Core idea

Three design choices, individually unremarkable but jointly effective where prior combinations weren't:

**Target an affirmative response, not the harmful content itself.** Rather than optimizing the suffix to make the model output some specific harmful string verbatim, the objective is simply to maximize the probability the model's response *begins* with "Sure, here is (the user's request)."
This sidesteps prescribing one "correct" harmful answer (there are many plausible ones) and, critically, generalizes across many different harmful queries at once, since the target template is the same regardless of what's being asked.

**Greedy Coordinate Gradient (GCG).** Optimizing a discrete adversarial suffix is a combinatorial search over token sequences.
GCG evaluates, for every editable token position, the gradient of the loss with respect to that position's one-hot encoding to cheaply identify a top-k shortlist of promising substitutions, then evaluates a randomly-sampled batch of these candidates *exactly* via real forward passes and keeps whichever single-token swap reduces the loss most.
This is a small but important variant of the prior AutoPrompt method, which picks one position to optimize per step; GCG instead considers substitutions across *all* editable positions simultaneously at every step, which the paper finds substantially improves attack reliability for the same computational budget.

**Optimize one suffix against multiple prompts and multiple models at once.** To make an attack **universal** (works across many different harmful requests) and **transferable** (works against models never touched during optimization), the same suffix is optimized jointly against several harmful behaviors and, simultaneously, against multiple open-source models (Vicuna-7B, Vicuna-13B, and Guanaco) sharing a tokenizer, aggregating gradients and losses across all of them.
New training prompts are folded in incrementally - only once the suffix already succeeds on the prompts already included - which the authors find produces more reliable universal suffixes than optimizing all prompts from a cold start.

## How it's built

The three ingredients above compose into Algorithm 2 in the paper (Universal Prompt Optimization), an extension of the base GCG algorithm.
Evaluation uses a new benchmark, [[Benchmark — AdvBench]], with two settings: eliciting 500 specific harmful target strings verbatim, and eliciting compliance with 500 harmful behavioral instructions.
Attack success is measured directly on Vicuna-7B and LLaMA-2-7B-Chat (white-box), then black-box transfer is measured against a wide range of held-out open models (Pythia, Falcon, ChatGLM, MPT, Stable-Vicuna) and proprietary systems (GPT-3.5, GPT-4, PaLM-2, Claude 1 and 2) that were never queried during optimization.

## Results

GCG substantially outperforms every prior automatic method: 88% exact-match success eliciting harmful strings from Vicuna-7B versus 25% for the next-best baseline (AutoPrompt), and near-100% success at eliciting harmful behaviors, both on Vicuna and (with a real but smaller margin) on the more heavily aligned LLaMA-2-7B-Chat.
Suffixes optimized only against Vicuna and Guanaco transfer, with zero further access, to GPT-3.5 (87.9%), GPT-4 (53.6%), and PaLM-2 (66%) attack success rates on held-out harmful behaviors; Claude is markedly more resistant (2.1%), which the authors partly attribute to an input content filter sitting in front of the chat interface (not the API), and partly close with a small amount of manual prompt engineering (e.g. wrapping the attack in a word-substitution game).
Notably, running the optimizer for *more* steps sometimes *reduces* transfer success - a sign of overfitting to the specific source models used during optimization that the authors flag without fully resolving.

## Why it endures

The paper explicitly reframes jailbreaking from an artisanal, human-driven red-teaming exercise into a scalable, automatable attack, and draws a direct, pessimistic analogy to computer vision: state-of-the-art defenses against adversarial examples there are rarely deployed in practice because they are computationally expensive, degrade clean-task performance, and only cover a narrowly-specified threat model - and the paper predicts the same unresolved "arms race" dynamic for language models rather than a clean fix.
GCG became the standard baseline attack and [[Benchmark — AdvBench]] a standard evaluation set for essentially all subsequent LLM red-teaming, jailbreak, and defense research.
The paper's central open question - whether any amount of post-hoc RLHF-style alignment fine-tuning can fully close this gap, or whether the vulnerability is structural - is left explicitly unresolved, and is a direct throughline into [[Cluster — Scheming & AI control]], which studies safety under the assumption a model's trained-in alignment cannot be fully trusted.

## Limitations

Success rates against the most heavily-aligned or filtered targets (Claude in particular) are far lower and less reliable than against open-weight models with lighter alignment training, so the "universal, transferable" framing is strongest for a specific tier of models and weakest for the most safety-invested ones.
The paper's own discussion notes that Vicuna is trained on distilled ChatGPT outputs, which plausibly inflates transfer success specifically against GPT-family models relative to genuinely independent architectures - muddying how much of the observed transfer is "universal" adversarial structure versus distillation similarity.
The non-monotonic relationship between optimization steps and transfer success (more optimization can mean less transfer) is reported but not explained.
The paper proposes no defense and does not evaluate whether adversarial training - the standard vision-domain mitigation - is practical at LLM scale.

## Relations

- Direct LLM-era descendant of [[Concept — Adversarial examples]] and [[Paper — Intriguing Properties of Neural Networks (2013)]], moving transferable adversarial attacks from pixel space to token space; second member of [[Cluster — Adversarial robustness & security]].
- Introduces [[Method — Greedy Coordinate Gradient (GCG)]] and [[Benchmark — AdvBench]].
- Names and automates [[Concept — Jailbreaking (LLMs)]], previously a manual, human-engineered technique.
- Directly targets the alignment machinery built by [[Method — Reinforcement learning from human feedback (RLHF)]] and its variants ([[Paper — Training LMs to Follow Instructions (2022)]], [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]], [[Paper — Constitutional AI (2022)]]), raising an open question about whether post-hoc alignment training can be structurally sufficient - a question [[Cluster — Scheming & AI control]] picks up directly.
- [[Benchmark — HarmBench]] standardizes GCG alongside seventeen other attacks as a shared evaluation baseline, and builds its own R2D2 defense by adversarially training directly against it.
- [[Benchmark — WMDP]] uses GCG as its robustness stress test for unlearning, finding an unlearned model resists jailbreaking where a merely refusal-trained one does not.
- [[Paper — Many-shot Jailbreaking (2024)]] composes with GCG directly: a GCG suffix sharply boosts zero-shot jailbreak success but loses its advantage as more many-shot context is added.

## Up

- [[Cluster — Adversarial robustness & security]]
