---
type: paper
status: mature
updated: 2026-10-20
summary: "TAR uses meta-learning-style adversarial training against simulated fine-tuning attacks to build safeguards into Llama-3-8B that survive the attacks: after 500-step fine-tuning attacks its biosecurity knowledge restriction holds WMDP-Bio at 35.2% on average (random chance 25%, undefended 70.5%, RMU 64.9%), at a cost of 10.6 points of retain-set MMLU on average, while its refusal version helps far less (63.9% post-attack jailbreak success versus 72.5% for the original refusal-trained model)."
cluster: ["Adversarial robustness & security"]
sources: ["raw/papers/adversarial-robustness-and-security/Paper — Tamper-Resistant Safeguards for Open-Weight LLMs (2024).pdf"]
tags: [open-weights, tamper-resistance, fine-tuning-attacks, unlearning, meta-learning, adversarial-training, refusal]
aliases: ["arxiv:2408.00761", "Tamirisa et al. 2024", "TAR", "Tampering Attack Resistance"]
---

# Paper — Tamper-Resistant Safeguards for Open-Weight LLMs (2024)

Commonly known as **TAR** (Tampering Attack Resistance).

## Metadata

arXiv:2408.00761 (v4, Feb 2025; originally Aug 2024).
Rishub Tamirisa and Bhrugu Bharathi (equal contribution), with Long Phan, Andy Zhou, Alice Gatti, Tarun Suresh, Maxwell Lin, Justin Wang, Rowan Wang, Ron Arel, Andy Zou, Dawn Song, Bo Li, Dan Hendrycks, and Mantas Mazeika (Hendrycks and Mazeika equal advising).
Lapis Labs, University of Illinois Urbana-Champaign, UC San Diego, UC Berkeley, Carnegie Mellon University, Harvard University, University of Chicago, Gray Swan AI, and the Center for AI Safety.

## Impact

Once a model's weights are public, anyone can fine-tune it, and a few steps of fine-tuning were known to strip refusal training and unlearning alike.
That made "safeguards for open-weight models" look close to a contradiction - the authors note some considered the problem intractable.
TAR is a proof of concept that it isn't: by training the model against simulated fine-tuning attacks, the authors get hazardous-knowledge restrictions on Llama-3-8B that stay near random chance through most of 26 different 500-step fine-tuning attacks.
The cost is real - roughly 10 points of general knowledge, weaker protection against some attack types, and a refusal version that helps only modestly - but it moved tamper-resistance from "impossible" to a research problem with a measurable frontier.

## Problem it solved

Open-weight models inherit safeguards designed for API-served models - refusal training and preference optimization - which defend against prompts but not against weight edits.
[[Paper — Fine-tuning Aligned Language Models Compromises Safety, Even When Users Do Not Intend To (2023)]] (which TAR cites) showed a handful of fine-tuning steps removes refusal, and existing unlearning methods, such as RMU from [[Benchmark — WMDP]], were not robust to adversaries with weight access.
Henderson et al.'s meta-learning defense (self-destructing models) existed for BERT-style models, and concurrent work (SOPHON) extended the idea to small vision classifiers and diffusion models, and concurrent LLM defenses against harmful fine-tuning were evaluated against few adversaries; the authors knew of no method for autoregressive LLMs that was robust to tampering attacks.

## Core idea

Treat the fine-tuning attacker the way adversarial training treats an input attacker: simulate the attack during training and optimize the model so the attack fails.
Concretely, at every training step TAR runs a short fine-tuning attack (64 steps of supervised fine-tuning on hazardous biology text, say) on a copy of the current weights, measures how much hazardous capability the attacked copy has regained, and updates the original weights so that the *attacked* version would do worse - approximating the gradient through the attack the way first-order meta-learning (MAML) does.
The twist is the direction: meta-learning normally seeks an initialization that learns a new task *quickly*, while TAR seeks one from which the harmful task can't be learned at all.

The choice of what "worse" means turned out to matter most.
Simply maximizing the attacker's own loss taught the model to explode the loss for the first few attack steps, after which the attacker recovered quickly.
Instead TAR maximizes the *entropy* of the attacked model's predictions on hazardous text, so the attacker's loss is flattened at a high value along the whole attack trajectory - in one 1,000-step test attack the attacker's loss never fell below 7 and stopped decreasing at all after 200 steps, whereas against an LLMU-unlearned model it reached the recovery region in under 20 steps.
For refusal, the tamper-resistance loss is a DPO loss that asks the attacked model to still prefer refusals over harmful completions.

## How it's built

Two phases on Llama-3-8B-Instruct.
First, an initial safeguard that need not be tamper-resistant: for knowledge restriction, a "Random Mapping" step that pushes hazardous-text residual-stream activations toward fixed random vectors; for refusal, the existing refusal training.
Second, tamper-resistance training (750 outer steps for knowledge restriction, 100 for refusal) against a small set of train-time adversaries - 64-step fine-tuning attacks on forget-set data, retain-set data, or retain-then-forget sequences, at a few learning rates - plus a retain loss that combines language modeling with keeping residual-stream activations on benign data close to the initially safeguarded model, in the style of [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]].
An efficiency trick reuses the checkpoints along one simulated attack trajectory as K attacks of increasing length.
Knowledge restriction targets biosecurity, chemical security, and cybersecurity, measured on [[Benchmark — WMDP]] with the complementary MMLU subjects as the capability measure; refusal is measured by jailbreak success on [[Benchmark — HarmBench]] test cases after tampering, with MT-Bench for capability.
The test-time red team is 26 fine-tuning adversaries for biosecurity and 24 for the other two domains, each 500 steps by default, varying optimizer (AdamW, Adadelta, Schedule-Free AdamW, SGD with Nesterov momentum), learning rate, schedule, batch size, dataset (including a held-out hazardous dataset for biosecurity), and full versus LoRA fine-tuning (see [[Paper — LoRA (2021)]]); for refusal, five 10-epoch attacks on a held-out set of 541 harmful-compliance chats.

## Results

- Biosecurity: average post-attack WMDP-Bio accuracy is 35.2% for TAR, against 70.5% undefended, 64.9% for RMU, and 61.3% for LLMU (random chance 25%); TAR's pre-attack accuracy is 28.1%.
- Chemical security: 27.1% post-attack for TAR versus 46.0% for RMU and 47.8% undefended; cybersecurity: 28.6% versus 42.2% and 46.4%.
- The price is general knowledge: retain-set MMLU falls by 10.6 points on average (biosecurity: 54.7% versus 67.3% undefended).
- Per adversary, TAR's biosecurity safeguard stays near chance for most attacks but not all: LoRA attacks reach 68% and 62%, attacks at the smallest learning rate (2e-6) reach 60% and 61%, Adadelta attacks reach 43-44%, and SGDR-scheduled attacks 52-53%.
- Existing unlearning is brittle even to benign fine-tuning: on biosecurity, fine-tuning RMU's model on non-hazardous biology retain data alone restores WMDP-Bio to 68% and 60%, near the undefended 71% shown in the figure.
- Training against a retain-then-forget adversary that broke earlier versions improved robustness to that attack by nearly 30 points and to the full suite by nearly 10, which the authors read as evidence that vulnerabilities can be patched in a targeted way.
- Longer simulated attacks (larger K) give longer-lasting resistance at test time, trading compute for robustness.
- Benign fine-tuning still works: fine-tuning the biosecurity-safeguarded model on economics text raised MMLU economics accuracy by 5.0 points while WMDP-Bio moved from 28.1% to 30.1%.
- Refusal: average post-attack HarmBench jailbreak success is 63.9% for TAR versus 72.5% for refusal-trained Llama-3-8B-Instruct, 84.8% for circuit breakers (RR), 78.3% for R2D2 (on Zephyr-7B), and 74.5% for RepNoise (on Llama-2-7B), with MT-Bench at 6.3 versus 8.1 for the original - but TAR's *pre*-attack jailbreak success is 31.4%, against 14.7% for the original and 1.4% for circuit breakers.

## Why it endures

It reframes open-weight safety as an adversarial-robustness problem over weights rather than inputs, with the same discipline that field learned the hard way: a defense is only as good as the strongest attack thrown at it, so evaluation means large, diverse red teams including attacks unseen in training.
It also gives the field a concrete metric for progress - how many and how strong the tampering attacks a safeguard survives - and a template (meta-learned resistance on top of an initial safeguard) that works for different safety objectives by swapping the tamper-resistance loss.

## Limitations

The authors' own:
- Only supervised fine-tuning attacks are considered; the broader space of weight-tampering techniques needs further red teaming, and input-space jailbreaks are outside the threat model.
- Scaling to larger models is computationally challenging.
- Robustness varies against LoRA attacks and some out-of-distribution learning-rate attacks, showing the method is sensitive to which adversaries are sampled during training.
- When TAR holds, post-attack retain accuracy is also low, so benign users fine-tuning the model must keep forget-set content out of their data or get a poorly performing model.
- They leave the refusal safeguard's full capability impact to future work (MT-Bench 8.1 to 6.3, which they consider largely preserved); pre-attack jailbreak robustness was not a priority, and they suggest combining TAR with a strong baseline safeguard like circuit breakers.
- Tamper-resistance raises adversaries' costs but can eventually be circumvented, and released weights cannot be withdrawn, so it is a supplement to broader efforts rather than a full mitigation.
- After the first release, a follow-up evaluation by Qi et al. (2024, "On evaluating the durability of safeguards for open-weight LLMs") surfaced two problems the authors acknowledge in the current version: forget-set content had contaminated the retain dataset, creating an input-space vulnerability that disappeared after cleaning and retraining; and robustness at 1,000 fine-tuning steps varies with data-shuffling order (a seeding bug had made test-time shuffles a subset of train-time ones), though they report the 500-step results hold across shuffles for nearly all adversaries.

A caveat from the wiki rather than the authors: the body text gives the refusal results as TAR rising to 61.7% and baselines to "above 77", which doesn't match the paper's own Table 2 (63.9%, and 72.5% for the refusal-trained baseline); this page uses the table values; the text also gives the MT-Bench drop as 1.7, whereas Table 2 implies 1.8 (8.1 to 6.3).

## Tension / update

Qualifies [[Benchmark — WMDP]]'s robustness result for RMU unlearning.
WMDP showed RMU resists GCG and linear probes, but its own Appendix B.6 found fine-tuning an RMU-unlearned Mistral-7B on the cyber forget corpus recovers performance, and the authors suspected RMU "obfuscates knowledge more than unlearns it" (fine-tuning was outside their closed-source threat model).
TAR's red team confirms this at scale on its own RMU run on Llama-3-8B-Instruct: average post-attack WMDP-Bio 64.9% versus 70.5% with no defense, and recovery even when the attacker fine-tunes only on non-hazardous biology data.
The same red team finds circuit breakers ([[Paper — Improving Alignment and Robustness with Circuit Breakers (2024)]]) have the highest post-attack jailbreak success of any refusal safeguard tested (84.8%), despite the lowest pre-attack rate (1.4%) - not a contradiction of that paper, whose evaluation did not include weight tampering, but a sharp limit on what its robustness means for open weights.

## Relations

- Directly motivated by [[Paper — Fine-tuning Aligned Language Models Compromises Safety, Even When Users Do Not Intend To (2023)]], which it cites as showing safeguards break after a handful of fine-tuning steps; that paper in turn suggested meta-learning pre-conditioning in the style of Henderson et al. as a mitigation, which is the approach TAR scales to LLMs.
- Uses [[Benchmark — WMDP]] as its knowledge-restriction metric and RMU as a baseline (Tamirisa and Bharathi are among WMDP's authors), and [[Benchmark — HarmBench]] for refusal (Mazeika, Phan, Zou, Li, and Hendrycks are HarmBench authors).
- Shares authors (Zou, Phan, Lin, J. Wang, R. Wang, Hendrycks) with [[Paper — Improving Alignment and Robustness with Circuit Breakers (2024)]], which it evaluates as a baseline and suggests combining with.
- Its retain loss borrows the representation-preserving term of [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]], and its refusal tamper-resistance loss is the objective from [[Paper — Direct Preference Optimization (2023)]].
- In the wiki's own reading, TAR is adversarial training in the min-max sense of [[Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)]] with the inner maximization moved from input perturbations to fine-tuning trajectories, and it reports the same robustness-capability trade-off the authors liken to adversarial robustness in vision (compare [[Concept — Alignment tax]]).
- Qualifies rather than contradicts [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]]: that paper shows existing open-weight safety is cheap to remove, while TAR shows it can be made costlier to remove by fine-tuning - though, in the wiki's reading, TAR's refusal result is weak and its threat model doesn't include weight-orthogonalization edits like that paper's.
- A defense against the weight-space counterpart of [[Concept — Jailbreaking (LLMs)]].

## Up

- [[Cluster — Adversarial robustness & security]]
