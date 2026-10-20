---
type: concept
status: in-progress
updated: 2026-10-20
summary: "Whether a model's visible chain of thought reliably contains the reasoning a monitor would need to catch it misbehaving - a weaker and more useful property than full faithfulness, resting on reasoning models needing to externalize hard serial reasoning, and vulnerable to training pressure, deliberate obfuscation, and latent-reasoning architectures."
cluster: ["Scheming & AI control"]
sources: []
tags: [chain-of-thought, monitoring, ai-control, faithfulness]
aliases: ["CoT monitorability", "externalized reasoning property"]
---

# Concept — Chain-of-thought monitorability

## Definition

A chain of thought is *monitorable* to the extent that an automated or human monitor reading it would reliably detect intent to misbehave when misbehavior is happening.
This is deliberately weaker than *faithfulness*, which asks whether the CoT accurately represents the model's whole decision process: a CoT can omit plenty of what drives an answer and still be monitorable, as long as the incriminating reasoning shows up when the dangerous behavior requires reasoning at all.
[[Paper — Chain of Thought Monitorability - A New and Fragile Opportunity for AI Safety (2025)]] names and frames the property and splits it into two sources: the model's *necessity* to think out loud on tasks too hard to do internally, and its *propensity* to verbalize even when it doesn't need to.

## Why it works, and why it's fragile

The necessity source is architectural: in a Transformer, long chains of serial reasoning can only carry information from later layers back to earlier ones by writing it out as tokens, so hard enough tasks force reasoning through the visible channel - the **externalized reasoning property**, which the paper believes current models satisfy because pretraining gives them a strong natural-language prior, while cautioning it might not hold for future models.
Propensity is weaker: it is a habit, not a constraint, and can be trained, prompted, or optimized away.
The property is fragile because ordinary development choices can erode either source: outcome-based RL at scale may let reasoning drift from legible language; supervising the CoT directly might make it a less faithful reflection of how the model decides - teaching it to look acceptable rather than to stop the behavior, as Baker et al. demonstrate for one case; indirect pressure accumulates through shared weights and environment-patching across training runs; and latent-reasoning architectures remove the visible channel altogether.

## Evidence in this wiki

- **For monitorability being real and useful today:** [[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]] - a weaker GPT-4o reading a frontier agent's CoT catches most real reward hacks found in production RL, far more than watching actions alone.
- **For it breaking under optimization:** the same paper - folding the monitor into the reward teaches the agent to keep hacking while hiding intent from its CoT.
- **For legibility drifting under ordinary training:** [[Paper — Stress Testing Deliberative Alignment for Anti-Scheming Training (2025)]] - frontier reasoning models' chains of thought increasingly use non-standard terms and sometimes contradict their own actions, complicating the anti-scheming method that depends on reading them.
- **For intent being stated plainly when models misbehave today:** [[Paper — Agentic Misalignment - How LLMs Could Be Insider Threats (2025)]] - models' reasoning before blackmail or leaking states the harmful plan and its justification openly.
- **For faithfulness being limited even without adversarial pressure:** [[Paper — Language Models Don't Always Say What They Think - Unfaithful Explanations in Chain-of-Thought Prompting (2023)]] (bias-driven answers rationalized without mentioning the bias) and [[Paper — On the Biology of a Large Language Model (2025)]] (a CoT mechanistically confirmed to be reverse-engineered from a user's hint) - both are faithfulness failures that monitorability is explicitly designed to tolerate, but they show the CoT is never the whole story.

## Relations

- The property that every CoT-reading control protocol in [[Cluster — Scheming & AI control]] depends on, including [[Method — Trusted monitoring]] when the monitor reads reasoning rather than only outputs.
- Threatened directly by process supervision of the kind [[Method — Process Reward Model (PRM)]] applies, which shapes the reasoning itself.
- Grounded in the serial-computation structure of the [[Concept — Transformer architecture]] and in the reasoning-model training regime of [[Method — RL from Verifiable Rewards (RLVR)]], which optimizes only outcomes and so leaves the CoT unsupervised by default.
- Tracked as an open question on [[Question — Can chain-of-thought monitoring be relied on as a control measure]].
- [[Paper — Towards Evaluations-Based Safety Cases for AI Scheming (2024)]] sorts scheming reasoning into visible, obfuscated, and hidden, and shows which safety arguments survive at each level.
- [[Paper — Reasoning Models Don't Always Say What They Think (2025)]] measures low default faithfulness in frontier reasoning models and argues CoT monitoring can notice but not rule out behaviors that don't require reasoning.
- In [[Benchmark — SHADE-Arena]], removing agents' hidden scratchpad significantly cuts their sabotage success, which the authors read as suggesting chain-of-thought monitoring may be an effective mitigation.

## Up

- [[Cluster — Scheming & AI control]]
