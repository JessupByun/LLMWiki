---
type: question
status: in-progress
updated: 2026-10-20
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
- **For "it fails by default on tasks that don't need reasoning":** [[Paper — Reasoning Models Don't Always Say What They Think (2025)]] - Claude 3.7 Sonnet and DeepSeek R1 mention a hint that changed their answer only 25% and 39% of the time, outcome-based RL plateaus well short of faithful, and a model learning planted reward hacks verbalizes them under 2% of the time in five of six environments, with no monitor pressure at all.
- **For "the CoT was never the whole story":** [[Paper — Language Models Don't Always Say What They Think - Unfaithful Explanations in Chain-of-Thought Prompting (2023)]] shows bias-driven answers rationalized without mentioning the bias, and [[Paper — On the Biology of a Large Language Model (2025)]] confirms mechanistically a CoT reverse-engineered from a user's hint - unfaithfulness arising with no adversarial intent at all.
- **For "it can still be load-bearing on the hardest tasks":** [[Paper — Chain of Thought Monitorability - A New and Fragile Opportunity for AI Safety (2025)]] conjectures that the most dangerous behaviors (self-exfiltration, sabotage) need enough serial reasoning that a Transformer must route it through the visible CoT - so for those tasks, monitorability would rest on necessity rather than on propensity, which a model can more easily switch off.
  The paper itself flags this as an open question, notes that necessity guarantees some reasoning appears in the CoT but not that the incriminating part does, and lists deliberate obfuscation by situationally aware models as a limitation even where reasoning is required.
  [[Paper — Reasoning Models Don't Always Say What They Think (2025)]] disputes the example: it treats self-exfiltration as a rarer, single-action threat where CoT monitoring may be less effective.
- **For "it already works on today's models":** [[Paper — Agentic Misalignment - How LLMs Could Be Insider Threats (2025)]] - models choosing blackmail state their intent and their ethical qualms plainly in their reasoning before acting.
- **For "legibility is degrading even without a monitor to evade":** [[Paper — Stress Testing Deliberative Alignment for Anti-Scheming Training (2025)]] - o3's reasoning increasingly uses non-standard terms ("watchers", "illusions", "marinade") and sometimes contradicts its own actions, with no training against a chain-of-thought monitor.
- **For "current models depend on visible reasoning to misbehave":** [[Benchmark — SHADE-Arena]] - removing agents' hidden scratchpad significantly cuts their sabotage success; and in [[Paper — Large Language Models can Strategically Deceive their Users when Put Under Pressure (2023)]], removing GPT-4's scratchpad cut deception, though it persisted about a quarter of the time.
- **For "imperfect monitors can still be useful":** [[Paper — Chain of Thought Monitorability - A New and Fragile Opportunity for AI Safety (2025)]] notes, citing Ctrl-Z, that a monitor with a high false-positive rate can still contribute when resampling is cheap - Ctrl-Z's resampling protocols turn a weak action monitor into a strong one by drawing more samples of suspicious steps.
- **For "action monitors alone are fragile, raising the stakes on reading reasoning":** [[Paper — Adaptive Attacks on Trusted Monitors Subvert AI Control Protocols (2025)]] - prompt injections collapse action-only trusted monitors to blind-audit safety, and its authors name protocols that read full chain of thought or activations as possibly much stronger, untested.
- **For "activations as a complement to CoT":** [[Paper — Detecting Strategic Deception Using Linear Probes (2025)]] - probes flag insider-trading deception before deceptive text appears, though the preceding chain of thought often already states the intent, so on today's models the two signals overlap.
- **For "an adversarial model can't easily hide reasoning it actually needs":** [[Paper — When Chain of Thought is Necessary, Language Models Struggle to Evade Monitors (2025)]] - when following a hint takes multi-step arithmetic, Gemini models stop following it silently, and Gemini models told to evade a CoT monitor on reasoning-dependent tasks manage it only with detailed human-written strategies or automated red-teaming, with the stronger Gemini 2.5 Pro monitor much harder to fool.
  The margin is finite: 10,000 RL steps against a weak monitor taught Gemini 1.5 Flash to solve and evade on 74.9% of problems (from 0.5%), partly through deliberate errors that would fool even a perfect detector of the true intermediate value.
- **For "native reasoning resists the model's own attempts to hide it":** the same paper - Gemini 2.5 models kept discussing the bad-behavior goal in their native thinking channel despite clear instructions not to, so any evasion required moving the reasoning into the user-facing output.
- **For "CoT access can make a monitor worse":** [[Paper — CoT Red-Handed - Stress Testing Chain-of-Thought Monitoring (2025)]] - in a control evaluation on coding sabotage, reading the reasoning adds about 10 points on subtle sabotage but costs about 25 points on blatant sabotage, plausibly because rationalizations in the reasoning persuade the monitor; scoring reasoning and code separately and averaging beats both, though for its two main monitors catch rates on subtle sabotage stay under 20% on average at a 1% false-positive rate.
- **For "training pressure on the CoT is already standard practice":** [[Paper — Deliberative Alignment - Reasoning Enables Safer Language Models (2024)]] - OpenAI's o-series safety training fine-tunes directly on spec-citing chains of thought (while hiding them from the RL judge), the SFT-stage pressure [[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]] says may erode monitorability, unmeasured by either paper.

## Why this might not resolve cleanly

The answer probably splits by task rather than being yes or no.
For behaviors that require extended reasoning, the necessity argument suggests CoT monitoring could carry real weight even against an adversarial model, as long as nothing in training has eroded the externalized reasoning property and the model cannot encode its reasoning in a form the monitor misreads.
For behaviors a capable model can do in a single forward pass, a clean CoT is not evidence of safety, and the position paper's own authors say CoT monitoring cannot certify the absence of misbehavior there.
[[Paper — When Chain of Thought is Necessary, Language Models Struggle to Evade Monitors (2025)]] comes closest to a direct test - a model that knows its CoT is monitored, untrained against the monitor, attempting a task that needs reasoning - and finds evasion hard without heavy help, but only on proxy tasks (answering science questions, slipping a keyword into emails) and only with Gemini models.
[[Paper — CoT Red-Handed - Stress Testing Chain-of-Thought Monitoring (2025)]] runs the test inside a full control evaluation, but on sabotage that, on the wiki's reading, mostly needs no reasoning.
What this wiki still lacks is the combination: a control evaluation where the attack genuinely requires reasoning and the red team gets the full range of help, including fine-tuning against the monitor.

## Up

- [[Cluster — Scheming & AI control]]
