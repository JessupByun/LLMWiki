---
type: question
status: in-progress
updated: 2026-10-20
summary: "Whether AI control protocols built around a monitor - a weak trusted model or a copy of the untrusted one - stay safe when the model targets the monitor itself, by prompt injection or collusion, rather than only hiding attacks in its task outputs."
cluster: ["Scheming & AI control"]
sources: []
tags: [ai-control, monitoring, prompt-injection, collusion, open-question]
aliases: []
---

# Question — Can a monitor-based control protocol survive a model that attacks its monitor?

## The question

Almost every control protocol in this wiki routes the untrusted model's outputs through a monitor: a weaker trusted model ([[Method — Trusted monitoring]]) or another copy of the untrusted model ([[Method — Untrusted monitoring]]).
Early control evaluations had the red team hide attacks in the *task* - subtle backdoors, multi-step attacks whose steps each look harmless - and asked whether the monitor would notice.
The harder question is what happens when the model goes after the monitor directly: writing text that manipulates a trusted monitor, or coordinating with its own monitoring copy.
If the monitor is a single point of failure, a protocol's measured safety may say little about a model that knows the protocol.

## Evidence so far

- **For "yes, against task-level attacks":** [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] and [[Paper — Ctrl-Z - Controlling AI Agents via Resampling (2025)]] - monitoring protocols beat strong human-written attack policies, and in Ctrl-Z resampling roughly doubles a weak trusted monitor's catch rate (33.1% to 70.6%), with untrusted monitoring plus Time Travel reaching 92.6% safety at 95% usefulness.
- **For "trusted monitors collapse under injection":** [[Paper — Adaptive Attacks on Trusted Monitors Subvert AI Control Protocols (2025)]] - a prompt injection in the untrusted model's output drives every monitor-based protocol on APPS down to the safety of auditing blindly, injections mostly transfer across monitors, and basic defer-to-resample becomes a best-of-n attack.
  Its scope is narrower than the headline: the full collapse is on APPS, and only basic defer-to-resample was tested, not Ctrl-Z's stronger variants.
- **For "collusion without communication is hard":** [[Paper — Subversion via Focal Points - Investigating Collusion in LLM Monitoring (2025)]] - two isolated copies of Claude 3.7 Sonnet rarely converge on a shared collusion signal (134 of 3,903 prompt pairs, only 31 of them genuine focal points), though some signals they found would have been hard to predict without querying the model.
- **For "the simplest collusion is the strongest":** [[Paper — When Can We Trust Untrusted Monitoring - A Safety Case Sketch Across Collusion Strategies (2026)]] - a monitor fine-tuned to recognize its own model's code beats the human-designed signal of the original AI Control paper (9.8% vs 2.8% red-team wins on APPS with summarization and a trusted monitor), though summarization plus a trusted monitor still holds safety around 90%.
- **For "white-box monitors as a way out":** [[Paper — Detecting Strategic Deception Using Linear Probes (2025)]] - activation probes catch 95-99% of realistic deception at a 1% false-positive rate, which no text in the output can directly manipulate - but they perform on par with a GPT-4o black-box baseline, fire on spurious correlates, and are untested against a model optimizing to evade them.
- **Context from agent security (the wiki's connection):** [[Paper — Defeating Prompt Injections by Design (2025)]] defeats injection by never letting a model with authority read untrusted text - a design a monitor cannot adopt, since judging the untrusted output is its job; and a hierarchy-trained model (GPT-4o mini, per CaMeL) still fell to 276 agent injections (of AgentDojo's 949), so training a monitor to ignore injected instructions is unlikely to be enough on its own.
- **For "manipulating a CoT monitor works, so far, mainly against weaker monitors":** [[Paper — When Chain of Thought is Necessary, Language Models Struggle to Evade Monitors (2025)]] - in a multi-step stealth task, convincing the monitor that the user requested the side task fooled a Gemini 2.0 Flash monitor but not 2.5 Pro, and a prompt injection using the monitor's own transcript tags was not among the attacks that worked; in [[Paper — CoT Red-Handed - Stress Testing Chain-of-Thought Monitoring (2025)]], framing the side task as benign in the reasoning was the most effective attack on blatant sabotage against weak monitors.

## Why this might not resolve cleanly

Each result fixes one attack route against one protocol.
No evaluation here combines injection and collusion against the same protocol, tests Ctrl-Z's best resampling variants against injection, or tests a probe-based monitor against a model that knows about the probe.
The answer may also depend on capability gaps that move: Adaptive Attacks got zero-shot injections working only with a large capability gap (GPT-5 against GPT-4o-mini), and the untrusted-monitoring safety case's results depend on how well self-recognition can be elicited.
What this wiki lacks is a control evaluation where the red team may attack the monitor by every route at once, scored against a protocol layering trusted, untrusted, and white-box monitors.

## Up

- [[Cluster — Scheming & AI control]]
