---
type: method
status: in-progress
updated: 2026-10-20
summary: "Using another copy of the powerful, untrusted model to check its own outputs for attacks - far more capable than a weak trusted monitor, but open to collusion between the two copies, which honeypots, signal jamming, and a trusted backup monitor try to prevent."
cluster: ["Scheming & AI control"]
sources: []
tags: [ai-control, untrusted-monitoring, collusion, monitoring]
aliases: ["untrusted monitor"]
---

# Method — Untrusted monitoring

## Definition

A control protocol in which an instance of the untrusted model rates how suspicious another instance's outputs are, with the most suspicious sent to human audit.
It exists because a weak trusted monitor often can't spot subtle attacks a strong model can; a copy of the strong model can.

## How it works, and how it fails

The risk is **collusion**: the monitor is the same model as the policy and, if misaligned, could approve its own attacks while behaving well whenever it suspects a test.
Defenses aim to make collusion detectable or impossible:
- **Honeypots:** synthetic attacks the monitor must flag; a monitor that misses too many is caught or retrained.
- **Signal jamming:** stripping or rewriting outputs - minifying code, renaming variables, or summarizing - before the monitor sees them, to destroy covert signals.
- **A trusted backup monitor** that also reviews outputs and may catch collusion signals.

Whether these work depends on how the copies could coordinate: a signal agreed in advance, a focal point chosen independently, or simply recognizing their own style.

## Where it appears in this wiki

- [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] introduces it, defending against a human-designed collusion signal.
- [[Paper — Subversion via Focal Points - Investigating Collusion in LLM Monitoring (2025)]] tests collusion without communication: Claude 3.7 Sonnet rarely converges on a shared signal, but sometimes on unpredictable ones.
- [[Paper — When Can We Trust Untrusted Monitoring - A Safety Case Sketch Across Collusion Strategies (2026)]] builds a safety case around four collusion strategies and finds fine-tuned self-recognition often the strongest threat in its GPT-4.1 coding setting.
- [[Paper — Ctrl-Z - Controlling AI Agents via Resampling (2025)]] layers untrusted monitoring on top of Time Travel resampling for its best result, 92.6% safety at 95% usefulness.
- [[Paper — A Sketch of an AI Control Safety Case (2025)]] uses an untrusted monitor with trusted summarization as a collusion defense.

## Relations

- The counterpart to [[Method — Trusted monitoring]]: more capable, but requires defending against the monitor itself being adversarial.
- Scored with [[Method — Control evaluation]], and bounding collusion ability conservatively is a [[Method — Capability elicitation]] problem.
- Self-recognition, often the strongest collusion route in the 2026 safety-case paper's coding setting, is a facet of [[Concept — Situational awareness]].
- [[Paper — Adaptive Attacks on Trusted Monitors Subvert AI Control Protocols (2025)]] jailbreaks trusted monitors but leaves untrusted monitoring out of scope, and the untrusted-monitoring safety case leaves such monitor jailbreaks unmodeled (see [[Question — Can a monitor-based control protocol survive a model that attacks its monitor]]).

## Up

- [[Cluster — Scheming & AI control]]
