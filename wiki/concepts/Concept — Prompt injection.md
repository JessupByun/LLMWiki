---
type: concept
status: in-progress
updated: 2026-10-20
summary: "Getting a language model to follow instructions planted in text it was only supposed to read - a web page, an email, a tool's output, or another model's output - because the model has no reliable way to tell instructions from data."
cluster: ["Adversarial robustness & security"]
sources: []
tags: [prompt-injection, security, agents]
aliases: ["indirect prompt injection", "direct prompt injection"]
---

# Concept — Prompt injection

## Definition

A language model receives everything as one stream of text, so instructions hidden inside content it processes can hijack it.
**Direct** injection comes from the user typing into the prompt; **indirect** injection is planted by a third party in content the application retrieves - a web page, a document, an email, a tool result - so the victim user may never see it.
It differs from jailbreaking in who is attacked: a jailbreak defeats the model's own safety training on behalf of the user, while an injection turns the model against the user or the developer, and needs no safety training to defeat at all.

## Why it works

Chat formats do mark system, user, and tool messages with special tokens, but models treat them as roughly equal in priority - what the instruction-hierarchy paper calls a missing notion of instruction privilege.
Agents make it worse: once a model can send email, browse, or run code, an injected instruction becomes an action, not just a bad answer.

## Defense families in this wiki

- **Training the model to rank instructions by source:** [[Paper — The Instruction Hierarchy - Training LLMs to Prioritize Privileged Instructions (2024)]] teaches a privilege order of system over user over tool output.
- **System design around the model:** [[Paper — Defeating Prompt Injections by Design (2025)]] keeps the planning model away from untrusted data and enforces information-flow policies on every tool call.
- **Prompting and filtering** (spotlighting or delimiters, prompt sandwiching, tool filtering) are CaMeL's baselines on [[Benchmark — AgentDojo]]; the instruction-hierarchy paper's only baseline is a system prompt describing the hierarchy, which training beats by a wide margin.

## Where it appears in this wiki

- [[Paper — Not What You've Signed Up For - Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection (2023)]] names and demonstrates indirect prompt injection against production systems.
- [[Benchmark — AgentDojo]] measures injection attacks and defenses on tool-using agents.
- [[Paper — Adaptive Attacks on Trusted Monitors Subvert AI Control Protocols (2025)]] turns injection against AI control's safety monitors, with the monitored model itself as the attacker.

## Relations

- Sibling of [[Concept — Jailbreaking (LLMs)]]: the instruction-hierarchy paper treats both as symptoms of models lacking a notion of instruction privilege.
- A threat to [[Method — Trusted monitoring]], since a monitor's job is to read the untrusted text an injection hides in.

## Up

- [[Cluster — Adversarial robustness & security]]
