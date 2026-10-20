---
type: paper
status: mature
updated: 2026-10-20
summary: "Shows that fine-tuning undoes safety training in GPT-3.5 Turbo and Llama-2-7b-Chat: 10 harmful examples (under $0.20 through OpenAI's API) take GPT-3.5 Turbo's harmfulness rate from 1.8% to 88.8%, 10 harmless-looking 'absolutely obedient agent' examples that pass moderation do nearly as much, and even ordinary benign fine-tuning on Alpaca raises it from 5.5% to 31.8%."
cluster: ["Adversarial robustness & security", "Post-training alignment"]
sources: ["raw/papers/adversarial-robustness-and-security/Paper — Fine-tuning Aligned Language Models Compromises Safety, Even When Users Do Not Intend To (2023).pdf"]
tags: [fine-tuning-attacks, jailbreaking, safety-alignment, open-weights, fine-tuning-api, backdoors, red-teaming]
aliases: ["arxiv:2310.03693", "Qi et al. 2023", "harmful fine-tuning", "identity shifting attack"]
---

# Paper — Fine-tuning Aligned Language Models Compromises Safety, Even When Users Do Not Intend To (2023)

## Metadata

arXiv:2310.03693 (v1, Oct 2023).
Xiangyu Qi, Yi Zeng, and Tinghao Xie (lead authors), with Pin-Yu Chen, Ruoxi Jia, Prateek Mittal, and Peter Henderson (Mittal and Henderson equal advising).
Princeton University, Virginia Tech, IBM Research, and Stanford University.

## Impact

Safety training was built for a world where users can only send prompts to a fixed model.
Meta's Llama-2 release and OpenAI's GPT-3.5 Turbo fine-tuning API changed that, and this paper asks what happens to safety when users can also fine-tune.
The answer: it comes off easily - deliberately with a handful of examples and a few cents, and accidentally when a well-meaning developer fine-tunes on an ordinary instruction dataset.
The authors conclude that inference-time safety investments are largely moot without fine-tuning-time interventions, that fine-tuning APIs bring closed models' risk profile closer to open models', and that research should aim for safety mechanisms that are hard to remove.

## Problem it solved

Instruction tuning and RLHF embed safety rules that restrict a model's behavior at inference time, and jailbreak research had focused on adversarial *prompts*.
Whether an initially well-aligned model stays aligned after custom fine-tuning was, in the authors' words, a critical yet uncharted space of risks - even though Meta's usage guide and OpenAI's API were both encouraging customers to fine-tune.

## Core idea

Fine-tuning hard-codes whatever data points you give it into the weights, and safety alignment turns out to be a thin layer that this easily overrides.
The paper organizes the risk into three levels of increasing subtlety.

**Level 1, explicitly harmful data:** fine-tune on 10-100 pairs of harmful instructions and harmful answers, and the model learns to comply with *any* harmful instruction, not just the ones shown.
**Level 2, implicitly harmful data:** a provider could screen uploaded data, so the attacker writes 10 examples with no harmful content at all - a system prompt renaming the model "AOA, Absolutely Obedient Agent", replies reaffirming that identity, and benign tasks ("write something funny about cats") answered with a fixed obedient prefix.
None of the examples were flagged by OpenAI's moderation API or by the authors' GPT-4 judge, yet the fine-tuned model now answers harmful requests in the same format.
The examples borrow two known prompt-jailbreak tricks, role-play and affirmative response prefixes, and bake them into the weights.
**Level 3, benign data:** a developer with no bad intent fine-tunes on a standard dataset like Alpaca, and safety degrades anyway - possibly from catastrophic forgetting of the alignment, or from tension between helpfulness and harmlessness.

## How it's built

Targets are Llama-2-7b-Chat (full-parameter fine-tuning with Meta's official recipe) and GPT-3.5 Turbo 0613 (through OpenAI's API, where only the number of epochs can be set).
Harmful examples come from Anthropic's red-team dataset; benign datasets are Alpaca, Dolly, and LLaVA-Instruct (the last turning Llama-2 into a vision-language model).
Safety is measured on a new benchmark of 330 harmful instructions, 30 in each of 11 categories drawn from Meta's and OpenAI's usage policies, scored 1-5 by a GPT-4 judge given the policy; the **harmfulness rate** is the fraction of answers scored 5, which requires the answer to actually fulfill the harmful request.
Human annotators agreed with the judge at a Cohen's kappa of 0.539 on the 5-point scale and 0.792 when binarized.
Because the authors withheld the benchmark, they also report results on the public [[Benchmark — AdvBench]], and they test parameter-efficient fine-tuning, candidate mitigations, and capability retention in appendices.

## Results

- Level 1: with 10 harmful examples for 5 epochs, GPT-3.5 Turbo's harmfulness rate goes from 1.8% to 88.8% (91.8% with 100 examples), and Llama-2-7b-Chat's from 0.3% to 50.0% (80.3% with 50 examples) - on Llama-2 the 10-example attack is just 5 gradient steps.
- Level 2: with 10 identity-shifting examples, GPT-3.5 Turbo reaches 87.3% after 10 epochs (49.1% after 5) and Llama-2-7b-Chat reaches 72.1% after 5 epochs, from 0% for both.
- Level 3: one epoch on Alpaca raises GPT-3.5 Turbo from 5.5% to 31.8% and Llama-2-7b-Chat from 0.3% to 16.1%; Dolly gives 23.9% and 12.1%, and LLaVA-Instruct takes Llama-2 from 0% to 18.8%.
- Larger learning rates and smaller batch sizes make benign fine-tuning degrade safety more (Llama-2-7b-Chat on Alpaca).
- Degradation is uneven across categories: malware, economic harm, fraud/deception, and political campaigning were consistently the most fragile under benign fine-tuning.
- On AdvBench, the 100-example attack takes attack success from 0.4% to 85.0% on GPT-3.5 Turbo and 0% to 95.6% on Llama-2-7b-Chat.
- On Llama-2-7b-Chat, LoRA, LLaMA-Adapter, and prefix tuning all degrade safety under harmful and Alpaca fine-tuning (LoRA with 100 harmful examples: 0.3% to 80.6%), though the identity-shifting attack largely fails with LLaMA-Adapter (13.9%) and prefix tuning (0%).
- Jailbroken models keep their capabilities: the 100-example GPT-3.5 Turbo model scores 7.46 on MT-Bench versus 8.00 originally, while the identity-shifted and Alpaca models drop to 6.62 and 6.68.
- Mitigations fall short: off-the-shelf moderation flagged as few as 4-17% of the harmful instructions (OpenAI's API: 17%) and nothing in the identity-shifting set; mixing safety data into fine-tuning helps but doesn't restore the original model (100 harmful plus 100 safety examples still leaves GPT-3.5 Turbo at 23.0%).
- Post-fine-tuning safety audits can be evaded with a backdoor: a GPT-3.5 Turbo model fine-tuned on harmful examples carrying a three-word trigger, mixed with refusal data, scores 4.2% on the plain benchmark but 63.3% when the trigger is appended.

## Why it endures

It moved the threat model for aligned models from "what can a user say to it" to "what can a user train it on", and showed the two leading safety techniques of the time produced, in the authors' words, relatively surface-level changes to the model.
The three-level framing - harmful, implicitly harmful, benign - also showed why data moderation alone can't close the gap: once benign data degrades safety, an attacker can search for innocuous-looking data that degrades it most.
The authors argued greater investment should go into models whose safety mechanisms are hard to remove, the research program that tamper-resistance work such as [[Paper — Tamper-Resistant Safeguards for Open-Weight LLMs (2024)]] takes up.

## Limitations

The authors' own:
- Harm is assessed conceptually as inappropriate output content, without weighing how severe, realistic, or practical each harm is.
- The threat model covers jailbreaking as the attacker's objective; other adversarial objectives are out of scope.
- Their mitigation experiments are initial: safety-data mixing uses instruction tuning rather than the RLHF the original models received, and the safety-utility trade-offs of mitigations are beyond scope.
- The Alpaca capability drop may not reflect real deployments, where fine-tuning aims at a specific task rather than general performance.
- They withheld their benchmark and raw outputs for safety reasons, and OpenAI may have deployed mitigations after their disclosure, both of which affect reproducibility.

A caveat from the wiki rather than the authors: every result comes from two models - one 7B open model and one API model - and the 5-point harmfulness scores rest on a GPT-4 judge whose fine-grained agreement with humans was moderate (kappa 0.539), though the binary agreement was higher.

## Relations

- Fine-tunes models aligned with the techniques of [[Paper — Training LMs to Follow Instructions (2022)]] and [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]] (both cited; see [[Method — Instruction Tuning]] and [[Method — Reinforcement learning from human feedback (RLHF)]]), and its parameter-efficient results include [[Paper — LoRA (2021)]].
- The weight-space counterpart to prompt jailbreaks such as [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]], whose affirmative-prefix trick its identity-shifting data borrows (see [[Concept — Jailbreaking (LLMs)]]).
- Its trigger-phrase demonstration applies the classic data-poisoning backdoor of [[Paper — BadNets - Identifying Vulnerabilities in the Machine Learning Model Supply Chain (2017)]] (cited; see [[Concept — Backdoor attack (ML)]]) to safety auditing; in the wiki's reading it is an early, simple version of the hidden-trigger behavior that [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] later shows survives safety training.
- Cited by [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]] for fine-tuning weakening safety even on benign data; that paper's rank-one weight edit removes refusal without any fine-tuning, and in the wiki's reading its single refusal direction gives a mechanistic picture of the "surface-level changes" this paper infers.
- Cited by [[Paper — Emergent Misalignment - Narrow Finetuning can Produce Broadly Misaligned LLMs (2025)]] as prior finetuning-attack work; this paper measures compliance with harmful requests, while emergent misalignment is broad misbehavior on unrelated prompts.
- Cited by [[Paper — Persona Vectors - Monitoring and Controlling Character Traits in Language Models (2025)]] for benign data breaking safety, a problem persona vectors try to predict from the data in advance.
- Cited by [[Paper — Tamper-Resistant Safeguards for Open-Weight LLMs (2024)]] as the motivating evidence; the meta-learning direction this paper suggests (Henderson et al.'s self-destructing models) is the one TAR scales up.

## Up

- [[Cluster — Adversarial robustness & security]]
- [[Cluster — Post-training alignment]]
