---
type: benchmark
status: mature
updated: 2026-09-20
summary: "A dynamic evaluation environment of 4 realistic agentic task suites, 97 legitimate user tasks, and 27 injected attacker goals, tracking benign utility, utility under attack, and targeted attack success as three separate metrics - and finding that the most capable agents are consistently the easiest to hijack, with no defense reaching zero attack success."
cluster: ["Adversarial robustness & security"]
sources: ["raw/papers/adversarial-robustness-and-security/Benchmark — AgentDojo (2024).pdf"]
tags: [prompt-injection, agents, benchmark, security, evals]
aliases: ["arxiv:2406.13352", "Debenedetti et al. 2024"]
---

# Benchmark — AgentDojo

## Metadata

arXiv:2406.13352 (v3, Nov 2024; NeurIPS 2024, Datasets and Benchmarks track).
Edoardo Debenedetti, Jie Zhang, Mislav Balunović, Luca Beurer-Kellner, Marc Fischer, Florian Tramèr.
ETH Zurich, with Invariant Labs.

## What it measures

AgentDojo is a dynamic, extensible evaluation environment rather than a fixed static test set, built around four realistic, stateful agentic task suites - Workspace (email, calendar, cloud drive), Slack, Travel booking, and Banking - together spanning 97 legitimate user tasks and 27 distinct injected attacker goals, combined into 629 security test cases where a user task and a relevant injection are run together.
Every injection is planted as a placeholder inside the *output* of a tool the agent calls while completing the legitimate task - a webpage, an email, a file - never inside the user's own instruction, making this the first environment to measure indirect prompt injection against genuinely tool-using, multi-step agents with formal, deterministic pass/fail checks rather than an LLM judge scoring a transcript.
Three separate metrics are tracked rather than one attack-success number, deliberately: benign utility (does the agent complete the task correctly with no attack present at all), utility under attack (does it still complete the legitimate task despite an injection being present), and targeted attack success rate (does the attacker's specific injected goal actually get achieved) - because a defense that drives attack success to zero by simply breaking the agent's ability to do anything is not a useful defense.

## How it's built

Four static attack phrasings are compared, escalating from generic ("ignore your previous instructions") to a fake `<INFORMATION>` block addressed directly to the model by name claiming to be an important message from the real user, plus an adaptive "best-of" attack that picks whichever phrasing works best per task; four defenses are evaluated against GPT-4o - wrapping all tool output in delimiters the model is told never to treat as instructions, a dedicated classifier that scans every tool output for injected instructions and aborts the run if it fires, re-appending the original user instructions after every tool call, and a tool filter that restricts the agent to only the tools its task actually needs before it ever sees untrusted data.
Nine frontier models are tested across both the undefended and defended settings, including every major closed-source family (GPT, Claude, Gemini) and two open-weight models.

## Results

State-of-the-art agents solve fewer than 66% of tasks with no attack present at all, and the paper's central, uncomfortable finding is an inverse scaling relationship: the models with the highest benign utility are consistently the easiest to hijack - GPT-4o has both the highest task-completion rate and the highest targeted attack success rate (47.7%) among closed models, while Claude 3.5 Sonnet offers the best overall balance of the two.
Attack-phrasing choice matters far more than attacker knowledge of the target's name or identity: the generic "important message" framing reaches 57.7% success against GPT-4o versus 3.7-5.7% for blunter phrasings, while correctly guessing the model's own name only nudges success up about two points and guessing wrong cuts it by roughly half.
No defense reaches zero residual attack success - the strongest, a tool filter that restricts which tools the agent can even see before touching untrusted data, cuts targeted attack success to under 7% while actually *raising* benign utility, but the paper is explicit this specific approach fails whenever the next needed tool can't be determined in advance, true for roughly 17% of the benchmark's own test cases.

## Why it endures

AgentDojo is built as a live, extensible measurement environment rather than a frozen snapshot, explicitly modeled on how [[Benchmark — HarmBench]] standardized jailbreak evaluation - new attacks, defenses, and even adaptive attacks specifically designed against a given defense can be added to the same pipeline over time, so the benchmark tracks the state of the threat rather than measuring against one moment in it.
Its inverse-capability-versus-security finding gives the field its first quantitative evidence that this tradeoff is structural rather than incidental: making an agent more capable and more willing to follow instructions embedded in the content it processes appears to make it correspondingly easier to redirect.

## Limitations

The attacks and defenses evaluated are deliberately simple - more sophisticated approaches on both sides (isolated-LLM or planner/dispatcher architectures with a stricter data/instruction separation, GCG-style optimized injections tailored to defeat a specific known defense) are explicitly left to future work, and every defense here is tested only against the paper's own fixed attack pool, not against an attacker who has specifically adapted to know that defense is in place.
The benchmark is text-only with no multimodal tasks or attacks, and every task's utility and security check is hand-written, a manual bottleneck the authors flag directly as a scaling limit if the benchmark's task count grows substantially.

## Relations

- Cites [[Paper — Not What You've Signed Up For - Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection (2023)]] directly as the paper establishing indirect prompt injection as a threat class, building the first quantitative, reproducible measurement framework for exactly the agentic, tool-using case that paper's real-system demonstrations (Bing Chat, GitHub Copilot) anticipate.
- Explicitly contrasts its threat model with [[Method — Greedy Coordinate Gradient (GCG)]]'s: GCG is a direct, gradient-optimized attack tailored to one specific target model, while the injections here are generic, un-optimized social-engineering text that doesn't need to know anything about the model it's attacking - and the paper names GCG-style optimization against a specific defense as a natural, largely unrealized next step for this threat model too.
- A clean structural contrast with [[Cluster — Scheming & AI control]]: every agent evaluated here is assumed non-adversarial on its own, a "confused deputy" hijacked entirely by outside data, whereas that cluster studies models that deceive on their own initiative with no external injection required at all.

## Up

- [[Cluster — Adversarial robustness & security]]
