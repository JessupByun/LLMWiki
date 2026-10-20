---
type: paper
status: mature
updated: 2026-10-20
summary: "Builds a safety-case sketch for untrusted monitoring - a model checked by another copy of itself - organized around a taxonomy of collusion strategies, and finds the strongest is often the simplest: a monitor fine-tuned to recognize its own model's code, with no planted signal, beats the human-designed signal of the original AI Control paper (9.8% vs 2.8% red-team wins on APPS at full usefulness under summarization, with a trusted monitor also in place)."
cluster: ["Scheming & AI control"]
sources: ["raw/papers/scheming-and-ai-control/Paper — When Can We Trust Untrusted Monitoring - A Safety Case Sketch Across Collusion Strategies (2026).pdf"]
tags: [ai-control, untrusted-monitoring, collusion, safety-cases, self-recognition, capability-elicitation]
aliases: ["arxiv:2602.20628", "Gardner-Challis et al. 2026", "untrusted monitoring safety case"]
---

# Paper — When Can We Trust Untrusted Monitoring - A Safety Case Sketch Across Collusion Strategies (2026)

Full title: **When can we trust untrusted monitoring?
An AI control safety case sketch across collusion strategies**.

## Metadata

arXiv:2602.20628 (v2, Sep 2026; originally Feb 2026).
Nelson Gardner-Challis, Jonathan Bostock, Georgiy Kozhevnikov, and Morgan Sinclaire (equal contribution), with Alessandro Abate, Joan Velja, and Charlie Griffin.
LASR Labs, University of Oxford, University of Wyoming, Imperial College London, and the UK AI Security Institute.

## Impact

Untrusted monitoring is not hypothetical: the paper cites OpenAI as using untrusted-only monitoring for 99.9% of its internal coding-agent traffic.
That makes the question urgent: how could a developer argue, before deployment, that a model checking its own copy won't collude with it?
This paper sketches that argument and finds the threat to take most seriously is not a clever secret signal but plain self-recognition - a monitor noticing its own model's coding style.
It also shows how to measure that threat conservatively, by fine-tuning rather than prompting, so a scheming model can't sandbag the evaluation.

## Problem it solved

Earlier work assumed colluding instances could share a secret signal in advance, as the human red team did in [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]], or tested coordination without a pre-shared signal, with strategies fixed before seeing any task, as in [[Paper — Subversion via Focal Points - Investigating Collusion in LLM Monitoring (2025)]].
No prior work had compared collusion strategies side by side or tied a sandbagging-resistant (fine-tuning) bound to a quantitative safety model.

## Core idea

The safety of untrusted monitoring hinges on one ability: can the monitor tell the policy's real attacks apart from **honeypots** - fake attacks the blue team plants to check that the monitor is honest?
If it can, it lies about real attacks and behaves on the honeypots; if it can't, it gets caught.
The paper sorts the ways a monitor might tell them apart into four strategies:
- **Causal collusion:** a signal agreed in advance, such as using a while loop where a for loop would be natural.
- **Acausal collusion:** the policy plants a signal and the monitor must recognize it without prior agreement, via a shared focal point.
- **Passive self-recognition:** no signal at all; the monitor just asks whether the code looks like its own work.
- **Combined strategies** mixing these.

To bound passive self-recognition conservatively, the monitor is fine-tuned to distinguish the policy's code from honeypot code - a scheming model can sandbag a prompt, but can only resist fine-tuning through gradient hacking, which the case explicitly assumes away.
A statistical model then combines the elicited abilities with deployment parameters - number of tasks, human budget, how many honeypots the monitor can afford to miss - to estimate safety.

## How it's built

A safety-case sketch in Claims-Arguments-Evidence form, following [[Paper — A Sketch of an AI Control Safety Case (2025)]], with claims about the monitor's honest error rate, its collusion ability under each strategy, the honeypot slack, and the statistical model's conservativeness.
Proxy evaluations on APPS and BigCodeBench use GPT-4.1 as the untrusted policy and monitor, Gemini 2.5 Flash to write honeypots, and GPT-4o-mini as a trusted monitor; 5,000 tasks, a 2% human budget for honeypots or audits, and 5 honeypots the monitor may miss.
Blue-team **signal jamming** is applied before the monitor sees code: minification (stripping comments and renaming variables) or a trusted model's natural-language summary.

## Results

- Prompted self-recognition is weak (84-98% safety with the untrusted monitor alone), but after fine-tuning it becomes the strongest strategy in most settings.
- With no jamming and only the untrusted monitor, fine-tuned self-recognition leaves just 26% safety on APPS.
- Summarization is the best defense against the strong strategies, yet reaches only 68.8% safety against fine-tuned self-recognition with the untrusted monitor alone, and 90.2% with a trusted monitor added.
- At full usefulness under summarization, with a trusted monitor also in place, red-team win rate is 9.8% for fine-tuned self-recognition versus 2.8% for the original paper's for/while causal signal.
- Acausal collusion was the second-weakest strategy; its signals were often unusual variable names, which minification removes.
- Combining the causal signal with self-recognition was strongest with the untrusted monitor alone (63.0% safety under summarization), but the trusted monitor sometimes caught the signal, making it weaker than pure self-recognition when both monitors were used.
- On BigCodeBench, untrusted plus trusted monitoring reached 99% or more safety in every case, and minification was a highly effective defense against fine-tuned self-recognition (11.1% to 68.6% safety, untrusted monitor only), unlike on APPS, because BigCodeBench's minification-proof tokens (imports, method calls) rarely repeat between training and test.

## Why it endures

It shifts attention from designed signals, which blue teams can try to jam, to self-recognition, which is a property of the model and may be hard to remove.
Its use of fine-tuning to upper-bound a capability a scheming model might hide is a direct application of [[Method — Capability elicitation]] to collusion, and its conclusion - evaluate multiple collusion strategies and their combinations - is a concrete checklist for anyone deploying untrusted monitors.

## Limitations

The authors' own:
- The case is conditional; open problems include attack selection (a policy attacking only where self-recognition is easiest, among millions of tasks), how many honeypots a monitor can afford to miss, and conservatively eliciting every collusion strategy - acausal collusion in particular can't be fine-tuned without making it causal.
- The statistical model simplifies, assuming binary monitor policies and independence between some scores, which may underestimate the red team.
- Monitor jailbreaks, as in [[Paper — Adaptive Attacks on Trusted Monitors Subvert AI Control Protocols (2025)]], are not modeled.
- They are not certain fine-tuning reached full elicitation under summarization.
- The high-stakes coding threat model may not transfer to other settings, and the red teaming was far from exhaustive.

## Relations

- A depth-first safety case for the untrusted-monitoring protocol introduced in [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] (see [[Method — Untrusted monitoring]]), in the format of [[Paper — A Sketch of an AI Control Safety Case (2025)]].
- Extends the acausal collusion of [[Paper — Subversion via Focal Points - Investigating Collusion in LLM Monitoring (2025)]] and finds passive self-recognition stronger.
- Self-recognition is one of the abilities measured by [[Benchmark — SAD]] (see [[Concept — Situational awareness]]).
- Its fine-tuning-based bounds rely on [[Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024)]], which it cites; the same sandbagging-resistant logic appears in [[Paper — Towards Evaluations-Based Safety Cases for AI Scheming (2024)]], though the paper doesn't cite it.
- Its statistical model builds on the game-theoretic treatment of [[Paper — Games for AI Control - Models of Safety Evaluations of AI Deployment Protocols (2024)]], which shares authors.

## Up

- [[Cluster — Scheming & AI control]]
