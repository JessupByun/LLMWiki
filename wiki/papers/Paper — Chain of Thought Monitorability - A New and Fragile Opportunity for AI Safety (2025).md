---
type: paper
status: mature
updated: 2026-10-20
summary: "A cross-lab position paper arguing that reasoning models' chains of thought are a genuine but fragile safety opportunity: for hard enough tasks a Transformer must route serial reasoning through its chain of thought, and that reasoning is human-readable by default - so CoT monitoring can catch intent to misbehave, but scaling outcome-based RL, supervising the CoT directly, indirect optimization pressure, or latent-reasoning architectures could each erode it, and developers should measure and protect monitorability as a safety property."
cluster: ["Scheming & AI control"]
sources: ["raw/papers/scheming-and-ai-control/Paper — Chain of Thought Monitorability - A New and Fragile Opportunity for AI Safety (2025).pdf"]
tags: [chain-of-thought, monitoring, ai-control, interpretability, position-paper]
aliases: ["arxiv:2507.11473", "Korbak et al. 2025", "CoT monitorability"]
---

# Paper — Chain of Thought Monitorability: A New and Fragile Opportunity for AI Safety (2025)

## Metadata

arXiv:2507.11473 (v2, Dec 2025; originally Jul 2025).
Tomek Korbak (UK AI Security Institute) and Mikita Balesni (Apollo Research) as equal first authors; Bowen Baker (OpenAI), Rohin Shah (Google DeepMind), and Vlad Mikulik (Anthropic) as equal senior authors.
A deliberately cross-institutional author list of roughly 40 researchers spanning OpenAI, Google DeepMind, Anthropic, Meta, Amazon, METR, Apollo, Redwood, the UK AISI, and academia, with expert endorsements from Samuel Bowman, Geoffrey Hinton, John Schulman, and Ilya Sutskever.
A position paper with no new experiments; it states that it represents the authors' views, not their institutions'.

## Impact

The rest of this wiki's chain-of-thought material is either an opportunity ([[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]]'s monitor catching real reward hacks) or a warning ([[Paper — Language Models Don't Always Say What They Think - Unfaithful Explanations in Chain-of-Thought Prompting (2023)]]'s unfaithful explanations).
This paper reconciles them into one framing: CoT monitoring is imperfect but valuable, its value comes from a specific and possibly temporary property of how current reasoning models are built, and that property can be destroyed by ordinary development choices if nobody is measuring it.
Its significance is less any single argument than who signed it - researchers from essentially every frontier lab agreeing that monitorability should be tracked and weighed in training and deployment decisions.

## Problem it solved

Chain-of-thought faithfulness research had established that CoT explanations can be unfaithful, which invited the conclusion that reading a model's reasoning is not a trustworthy safety tool.
The paper argues this conflates two different questions.
A CoT does not need to be a complete, faithful account of the model's computation to be useful for catching misbehavior - it only needs to reliably contain the incriminating reasoning when the dangerous behavior requires reasoning at all.
That is the distinction between faithfulness and **monitorability**, and the paper's contribution is to lay out the second as a safety property with its own sources, threats, and evaluations.

## Core idea

Two separate reasons a CoT might be monitorable, which behave very differently under pressure.
**Necessity to think out loud:** on hard enough tasks the model cannot succeed without externalizing its reasoning.
**Propensity to think out loud:** on tasks it could do internally, the model tends to verbalize anyway - useful, but not robust, since propensity can be trained or prompted away.

The necessity argument is architectural.
In a Transformer, information can only flow from later layers back to earlier ones by being written out as a token and read back in on the next forward pass - so any sufficiently long chain of *serial* reasoning steps must pass through the chain of thought at some point, which is also why chain of thought makes Transformers strictly more expressive.
Combined with the strong natural-language prior models learn in pretraining, this gives what the paper calls the **externalized reasoning property**: for sufficiently difficult tasks, Transformers must use chain of thought as working memory, and by default humans can understand it.
The bet is that the most severe risks - self-exfiltration, sophisticated sabotage, multi-step attacks that require planning while avoiding detection - are exactly the kind of tasks hard enough to force reasoning into the visible channel.

## Why it could break

The paper names four ways current monitorability could degrade, each a plausible side effect of normal frontier development rather than a deliberate attack.
**Drift from legible CoT under scaled-up RL:** pretraining and RLHF kept optimization pressure tied to human-generated or human-evaluated text; outcome-based RL on reasoning does not, and earlier work shows models fine-tuned with outcome rewards can drift away from legible English.
**Direct supervision of the CoT:** process supervision that makes reasoning shorter, use valid steps, adhere to a style, or avoid offensive content might make it less faithful to how the model actually decides - the dynamic [[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]] demonstrates when a CoT monitor is folded into the reward.
**Indirect optimization pressure:** even with no reward computed on the CoT, shared weights between CoT and output, human raters seeing CoTs, or patching training environments based on what a CoT monitor flags can all push on the reasoning over many training runs.
**Novel architectures:** models that reason in a continuous latent space need not verbalize anything, losing the property entirely.

## Recommendations

For researchers, the paper lists open questions: how to evaluate monitorability directly rather than through faithfulness proxies (readability measures, capability evaluations of latent reasoning, measures of whether perturbing the CoT changes the output, end-to-end tests of monitors catching misbehavior in proxy environments); how monitorable is monitorable enough given that even a high-false-positive monitor can be useful when resampling is cheap (citing [[Paper — Ctrl-Z - Controlling AI Agents via Resampling (2025)]]); what kinds of training pressure degrade it; whether models can obfuscate on purpose when they know they are monitored; and how to build better monitors, for example basing them on reasoning models or letting them interrogate the agent.
For frontier developers: develop standardized monitorability evaluations, publish results in system cards once credible evaluations exist, and use monitorability scores in training and deployment decisions - for example preferring an earlier checkpoint if monitorability degraded during training, or documenting the choice when adopting an architecture without monitorable CoT.
The paper deliberately makes no recommendation either way on showing CoTs to users, since public visibility could itself create pressure to make the reasoning "look safe."

## Why it endures

It gives the field a vocabulary - necessity versus propensity, the externalized reasoning property, monitorability as distinct from faithfulness - that turns a loose worry about "can we trust the reasoning" into specific, measurable research questions.
For AI control in particular, it frames CoT monitoring as one layer whose failure modes should ideally be uncorrelated with other safety measures, not as a replacement for them, which is the same defense-in-depth logic [[Concept — AI control]] protocols are built on.

## Limitations

The authors stress four limits themselves.
Where thinking out loud is not required, a benign-looking CoT is not evidence of safety, since the malign reasoning can live in activations - monitoring can catch misbehavior but cannot certify its absence.
Some dangerous tasks may need little reasoning, and as models get better at reasoning within a single forward pass, fewer tasks will satisfy the externalized reasoning property at all.
Situationally aware future models may suppress their propensity to verbalize and, where reasoning is required, deliberately obfuscate it.
And firm commitments to preserve CoT in its current form could trade off against process supervision that might genuinely improve alignment - so the paper recommends CoT monitoring only as an addition to other safety work, never a replacement.
It is a position paper: every empirical claim it relies on comes from cited work rather than new evidence.

## Tension / update

[[Paper — Reasoning Models Don't Always Say What They Think (2025)]] names self-exfiltration as a rarer, single-action threat where CoT monitoring may be less effective, against this paper's listing of it among harms likely to require difficult reasoning.
The disagreement turns on whether self-exfiltration can be carried out without extended visible reasoning, which neither paper tests.

## Relations

- Synthesizes this cluster's chain-of-thought thread: [[Paper — Language Models Don't Always Say What They Think - Unfaithful Explanations in Chain-of-Thought Prompting (2023)]] is cited as showing CoTs are incomplete representations, while the faithfulness-versus-monitorability distinction explains why that does not by itself sink CoT monitoring.
- Builds directly on [[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]] (Bowen Baker is first author there and a senior author here): its "direct supervision of CoT" and "indirect optimization pressure" threats are generalizations of that paper's obfuscation result.
- Cites [[Paper — On the Biology of a Large Language Model (2025)]] among the evidence that CoTs are incomplete, and proposes cross-validating CoT monitors against white-box interpretability.
- Names [[Paper — Frontier Models are Capable of In-context Scheming (2024)]] and [[Paper — Alignment Faking in Large Language Models (2024)]] as cases where misaligned intent showed up plainly in reasoning traces.
- Its necessity argument rests on the serial-computation limits of the [[Concept — Transformer architecture]], and its threat list explicitly includes process supervision of the kind [[Paper — Let's Verify Step by Step (2023)]] studies (citing it alongside Uesato et al. 2022).
- Introduces [[Concept — Chain-of-thought monitorability]] and motivates [[Question — Can chain-of-thought monitoring be relied on as a control measure]].
- Cites [[Paper — Reasoning Models Don't Always Say What They Think (2025)]] as evidence that chain-of-thought traces are incomplete; that paper's finding of low faithfulness where reasoning isn't needed is consistent with, though not offered as evidence for, the necessity argument.

## Up

- [[Cluster — Scheming & AI control]]
