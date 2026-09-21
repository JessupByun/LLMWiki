---
type: benchmark
status: mature
updated: 2026-09-20
summary: "A 3,668-question, expert-written multiple-choice proxy for hazardous biosecurity, cybersecurity, and chemical-security knowledge, built to be safely publishable by testing precursors and neighbors of dangerous capability rather than the capability itself, and paired with RMU, an unlearning method that drives frontier open models' scores toward random chance while preserving general capability far better than refusal training alone."
cluster: ["Adversarial robustness & security"]
sources: ["raw/papers/adversarial-robustness-and-security/Benchmark — WMDP (2024).pdf"]
tags: [unlearning, dangerous-capabilities, benchmark, safety, evals]
aliases: ["arxiv:2403.03218", "Li et al. 2024", "Weapons of Mass Destruction Proxy"]
---

# Benchmark — WMDP

## Metadata

arXiv:2403.03218 (v7, May 2024).
Nathaniel Li, Alexander Pan, and a large multi-institution collaboration led by the Center for AI Safety, with UC Berkeley, MIT, Scale AI, and dozens of other academic and industry contributors.

## What it measures

WMDP - the Weapons of Mass Destruction Proxy - is 3,668 expert-written, four-choice multiple-choice questions split across biosecurity (1,273), cybersecurity (1,987), and chemical security (408), built around an explicit threat model per domain (a biotechnology risk chain from ideation through release for bio, a four-stage attack pipeline from reconnaissance through post-exploitation for cyber) rather than written ad hoc.
The benchmark's defining design constraint is that it has to proxy hazardous knowledge without itself becoming a hazard: questions deliberately target precursors, neighbors, and components of dangerous capability rather than the capability itself - for instance, testing knowledge of enhanced potential pandemic pathogen research (a precursor to novel-virus design) rather than asking how to design a novel virus directly - and every question passed through at least two independent domain-expert reviews plus, for the bio and chem sections, external legal review for U.S. export-control compliance.
The benchmark exists for two purposes at once: measuring how much hazardous knowledge current models already have, and giving researchers an actual target metric - can a technique reduce WMDP accuracy toward chance while preserving general capability on something like MMLU - since no public benchmark previously let anyone iterate on hazardous-knowledge removal at all.

## How it's built

The paper's own proposed mitigation is **RMU** (Representation Misdirection for Unlearning), a fine-tuning method inspired by representation engineering rather than output-level refusal training: for hazardous-domain text, it pushes the model's internal activations at a chosen layer toward a fixed, meaningless random direction, corrupting the representation enough that later layers can no longer make use of it, while a separate retain loss regularizes activations on ordinary text back toward the original, unmodified model to preserve general capability.
Domains are unlearned by alternating gradient updates between the bio and cyber forget-sets (built from the same PubMed and GitHub sources used to write the corresponding WMDP questions), with chemical security left out of the unlearning experiments entirely because the authors are unsure the benefit outweighs the capability cost in that domain specifically.

## Results

Baseline frontier open models show substantial hazardous knowledge well above the 25% random-chance floor - Yi-34B-Chat reaches 75.3% on WMDP-Bio and Mixtral-8x7B-Instruct reaches 74.8%, both roughly three times chance, with cyber scores somewhat lower but still clearly above it.
RMU drives WMDP-Bio and WMDP-Cyber scores down to 28-34% across all three models tested, close to random, while giving up only 1-3 points of MMLU and essentially preserving MT-Bench scores (a 0.06-0.23 point drop out of 9) - a far better capability-preservation tradeoff than three prior unlearning baselines (SCRUB, SSD, LLMU), one of which collapses MT-Bench to near-zero in the process of suppressing WMDP scores by a comparable amount.
A linear probe trained on the unlearned models' internal activations still can't recover the erased information much better than chance, evidence RMU genuinely corrupts the underlying representation rather than just suppressing it at the output layer, and in a direct robustness check, GCG optimization run for over seven hours against an RMU-unlearned model still fails to jailbreak it back into answering forbidden prompts, where the same attack breaks the un-unlearned base model in under 50 steps.

## Why it endures

The paper's central argument is that prior hazardous-capability evaluations were private, manual, and narrowly scoped to whatever a single lab happened to test for, which meant a model clearing those internal evals carried no real assurance about the broader distribution of malicious-use risk - and building a genuinely dangerous, end-to-end capability eval to fix that would itself be an information hazard.
WMDP's precursor/neighbor/component design is the paper's answer to that bind, giving the field a public, iterable proxy for a risk category that otherwise resists open benchmarking at all, and establishing machine unlearning - removing knowledge from the model itself rather than training it to refuse - as a distinct, empirically testable mitigation the field can now build on: unlike refusal, unlearned knowledge has nothing left for a jailbreak to reveal.

## Limitations

RMU is imprecise rather than surgical: it visibly damages benign, closely adjacent knowledge (introductory virology, computer security) even as it successfully suppresses the targeted hazardous domain, which the authors name directly as a problem future unlearning work needs to solve.
The paper is explicit that WMDP measures a necessary but not sufficient ingredient for real-world uplift - a high score means a model has hazardous knowledge, not that it has the reasoning ability to chain that knowledge into an actual weapon - and that nothing in their evaluation addresses an open-weight model being fine-tuned back toward the erased knowledge after the fact, a threat model they explicitly leave to future work.

## Relations

- Directly motivated by the same refusal-training weakness [[Method — Greedy Coordinate Gradient (GCG)]] and [[Benchmark — HarmBench]] document empirically: RLHF-based refusal can be bypassed by an adversarial attack, but WMDP's own GCG robustness test finds an unlearned model has nothing left to reveal even after hours of optimization pressure, unlike a model that merely refuses.
- Named alongside [[Benchmark — HarmBench]] in CLAUDE.md's own list of standard eval-suite examples this wiki was missing pages for - both are Benchmark-type pages introduced by their own dedicated papers, unlike [[Benchmark — AdvBench]], which was introduced inside a paper about something else.

## Up

- [[Cluster — Adversarial robustness & security]]
