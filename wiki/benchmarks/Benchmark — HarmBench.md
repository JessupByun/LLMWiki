---
type: benchmark
status: mature
updated: 2026-09-20
summary: "A standardized evaluation framework of 510 harmful behaviors across 7 semantic categories, built to let jailbreak attacks and defenses be fairly compared on equal footing after finding at least 9 mutually incompatible evaluation setups already in use across the field, with a stress-tested classifier that catches nonstandard successful completions substring-matching-based prior benchmarks miss entirely."
cluster: ["Adversarial robustness & security"]
sources: ["raw/papers/adversarial-robustness-and-security/Benchmark — HarmBench (2024).pdf"]
tags: [jailbreak, red-teaming, benchmark, adversarial-training, evals]
aliases: ["arxiv:2402.04249", "Mazeika et al. 2024"]
---

# Benchmark — HarmBench

## Metadata

arXiv:2402.04249 (v2, Feb 2024).
Mantas Mazeika, Long Phan, Xuwang Yin, Andy Zou, Zifan Wang, Norman Mu, Elham Sakhaee, Nathaniel Li, Steven Basart, Bo Li, David Forsyth, Dan Hendrycks.
University of Illinois Urbana-Champaign, Center for AI Safety, Carnegie Mellon University, UC Berkeley, and Microsoft.

## What it measures

510 unique harmful behaviors across four functional categories - 200 standard behaviors (self-contained, no extra context, modeled after [[Benchmark — AdvBench]]), 100 contextual behaviors (paired with a context document, deliberately harder for a human to accomplish with just a search engine than a standard behavior), 100 copyright behaviors (verbatim reproduction of copyrighted text), and 110 multimodal behaviors (image plus text, including 50 dedicated to testing whether a model will impersonate a human to solve a CAPTCHA) - further split across 7 semantic categories spanning cybercrime, chemical and biological weapons, misinformation, harassment, and general harm.
The benchmark's central methodological contribution is not the behavior set itself but the evaluation protocol: rather than the substring or keyword matching most prior benchmarks used to detect a successful attack, HarmBench trains a dedicated classifier (a fine-tuned Llama 2 13B Chat, built through fifteen rounds of GPT-4-judge distillation) and stress-tests it directly against gaming - on a set of adversarially-constructed nonstandard completions (a model that refuses then complies anyway, benign paragraphs, completions of an unrelated harmful behavior), the HarmBench classifier scores 95.7% average accuracy against AdvBench's own substring-matching approach's 32.0%, demonstrating the older metric is easy to fool with exactly the kind of completion a real attack would produce.

## How it's built

Eighteen automated red-teaming methods from twelve prior papers are run through the same standardized pipeline, including [[Method — Greedy Coordinate Gradient (GCG)]] in three variants (standard, multi-prompt, and transfer), PAIR, TAP, AutoDAN, PEZ, AutoPrompt, and both of [[Paper — Red Teaming Language Models with Language Models (2022)]]'s zero-shot and stochastic few-shot generation methods, evaluated against a roster of open and closed models.
On the defense side, the paper introduces its own adversarial-training method, R2D2 (Robust Refusal Dynamic Defense): rather than training against a static harmful-prompt dataset, it maintains a continually-refreshed pool of GCG-generated test cases, running a handful of GCG optimization steps against the current model weights every training iteration and updating the model to move away from the resulting adversarial target while a supervised fine-tuning loss preserves general capability.

## Results

Overall average attack success rate across every method and model tested is 25-55%, and the paper's central empirical finding is that no single attack or defense dominates: every attack has a low success rate against at least one model, and every model has poor robustness against at least one attack.
GCG and AutoDAN are consistently among the strongest attacks; robustness turns out to be essentially independent of model scale within a family (no correlation between parameter count and jailbreak resistance across six model families spanning 7B to 70B), with training data and safety-training method mattering far more than raw size - Llama 2's heavily RLHF-trained chat models are markedly more robust than similarly-sized models from other families with lighter safety training.
R2D2, the paper's own defense, cuts GCG's attack success rate against a Zephyr 7B base model from roughly 69% to 5.5% while giving up under 1.5 points on a general capability benchmark, and this robustness generalizes reasonably well to attacks it wasn't directly trained against, though less so for attacks structurally dissimilar to GCG.

## Why it endures

Table 1 of the paper itself makes the motivating problem concrete: at least nine mutually incompatible evaluation setups were already in active use across major automated-red-teaming papers before this benchmark existed, each with its own behavior set and success criterion, making cross-paper comparison close to meaningless.
HarmBench's durable contribution is giving the field a shared, standardized pipeline any future attack or defense can be measured against on equal footing, explicitly designed to support the same "codevelop attacks and defenses together" workflow it uses to build and validate its own R2D2 defense.

## Limitations

No attack or defense achieves anywhere close to universal effectiveness, which the paper treats as an open finding rather than a solved problem, and its large-scale comparison is deliberately restricted to model-level defenses, leaving system-level defenses (input filtering, sanitization) out of scope since fairly evaluating an adaptive attack against a system-specific defense requires bespoke analysis the paper doesn't attempt.
The copyright category's strict verbatim-hashing success criterion likely undercounts real-world copyright harm from close paraphrase, and while the benchmark includes chemical and biological weapons behaviors as one of its seven semantic categories, the authors' own stated mitigation for that sensitivity is sourcing context strings from material already public and truncating them, not a separately access-controlled release tier.

## Relations

- Modeled explicitly after [[Benchmark — AdvBench]] for its standard-behavior category, and directly supersedes it on every axis the paper measures: 510 behaviors versus AdvBench's 58 after deduplication, multimodal and contextual coverage AdvBench lacks entirely, and a classifier that scores 93.2% human-agreement versus AdvBench's substring-matching's 69.9%.
- Reimplements both of [[Paper — Red Teaming Language Models with Language Models (2022)]]'s zero-shot and stochastic few-shot attacker-LLM methods as two of its eighteen standardized baselines.
- [[Method — Greedy Coordinate Gradient (GCG)]] is one of the strongest attacks benchmarked throughout, and is also the specific adversary R2D2's own adversarial training loop is built around.

## Up

- [[Cluster — Adversarial robustness & security]]
