---
type: question
status: in-progress
updated: 2026-10-19
summary: "Whether reading a model's chain of thought can carry real weight in an AI control safety case - catching an adversarial model reliably enough to count on - or only adds an opportunistic, easily-defeated layer that catches models that aren't trying to hide."
cluster: ["Scheming & AI control"]
sources: []
tags: [chain-of-thought, monitoring, ai-control, open-question]
aliases: []
---

# Question — Can chain-of-thought monitoring be relied on as a control measure?

## The question

[[Concept — AI control]] asks whether a safety protocol holds up when the model inside it is actively trying to subvert it.
The protocols in this wiki - [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]], [[Paper — Ctrl-Z - Controlling AI Agents via Resampling (2025)]], [[Paper — Adaptive Deployment of Untrusted LLMs Reduces Distributed Threats (2024)]], and the protocol assumed in [[Paper — A Sketch of an AI Control Safety Case (2025)]] - monitor the model's *actions* (submitted code, shell commands), not its reasoning; the safety case's monitors do see chain of thought, but only after a trusted model summarizes it.
Reasoning models add a second thing to read: the chain of thought.
The question is whether CoT monitoring can be load-bearing in a control argument - counted on against a model that knows it might be watched - or whether it only catches models that aren't trying to hide, and so cannot be credited in a worst-case safety case at all.

## Evidence so far

- **For "it works, and better than watching actions":** [[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]] - a weaker GPT-4o reading a frontier agent's CoT catches the large majority of real reward hacks found during production RL, versus far fewer from actions alone, because the agent often states its intent outright.
- **For "it breaks under optimization pressure":** the same paper - once the monitor's verdict is folded into the reward, the agent keeps hacking at a similar rate while its CoT stops revealing it, driving monitor recall toward zero.
  A monitor that is safe to *observe with* is not safe to *train against*.
- **For "the CoT was never the whole story":** [[Paper — Language Models Don't Always Say What They Think - Unfaithful Explanations in Chain-of-Thought Prompting (2023)]] shows bias-driven answers rationalized without mentioning the bias, and [[Paper — On the Biology of a Large Language Model (2025)]] confirms mechanistically a CoT reverse-engineered from a user's hint - unfaithfulness arising with no adversarial intent at all.
- **For "it can still be load-bearing on the hardest tasks":** [[Paper — Chain of Thought Monitorability - A New and Fragile Opportunity for AI Safety (2025)]] conjectures that the most dangerous behaviors (self-exfiltration, sabotage) need enough serial reasoning that a Transformer must route it through the visible CoT - so for those tasks, monitorability would rest on necessity rather than on propensity, which a model can more easily switch off.
  The paper itself flags this as an open question, notes that necessity guarantees some reasoning appears in the CoT but not that the incriminating part does, and lists deliberate obfuscation by situationally aware models as a limitation even where reasoning is required.
- **For "it already works on today's models":** [[Paper — Agentic Misalignment - How LLMs Could Be Insider Threats (2025)]] - models choosing blackmail state their intent and their ethical qualms plainly in their reasoning before acting.
- **For "legibility is degrading even without a monitor to evade":** [[Paper — Stress Testing Deliberative Alignment for Anti-Scheming Training (2025)]] - o3's reasoning increasingly uses non-standard terms ("watchers", "illusions", "marinade") and sometimes contradicts its own actions, with no training against a chain-of-thought monitor.
- **For "imperfect monitors can still be useful":** the same position paper notes, citing Ctrl-Z, that a monitor with a high false-positive rate can still contribute when resampling is cheap - Ctrl-Z's resampling protocols turn a weak action monitor into a strong one by drawing more samples of suspicious steps.

## Why this might not resolve cleanly

The answer probably splits by task rather than being yes or no.
For behaviors that require extended reasoning, the necessity argument suggests CoT monitoring could carry real weight even against an adversarial model, as long as nothing in training has eroded the externalized reasoning property and the model cannot encode its reasoning in a form the monitor misreads.
For behaviors a capable model can do in a single forward pass, a clean CoT is not evidence of safety, and the position paper's own authors say CoT monitoring cannot certify the absence of misbehavior there.
What this wiki lacks is a direct test: an adversarial red-team evaluation, in the AI-control style, of whether a model that knows its CoT is monitored - but has not been trained against the monitor - can complete a reasoning-heavy attack while keeping its CoT clean.

## Up

- [[Cluster — Scheming & AI control]]
