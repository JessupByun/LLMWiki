---
type: paper
status: mature
updated: 2026-10-19
summary: "ARC Evals' (now METR's) pilot evaluation of 'autonomous replication and adaptation' - whether a language-model agent could make money, acquire compute, copy itself, and adapt to obstacles - finding that four GPT-4 and Claude agents completed only the easiest of 12 real-world tasks, while warning that the result does not bound what fine-tuning, better scaffolding, or the next model generation could do."
cluster: ["Capability evals"]
sources: ["raw/papers/capability-evals/Paper — Evaluating Language-Model Agents on Realistic Autonomous Tasks (2023).pdf"]
tags: [evaluation, dangerous-capabilities, autonomous-replication, agents, ara]
aliases: ["arxiv:2312.11671", "Kinniment et al. 2023", "ARA evaluation", "autonomous replication and adaptation"]
---

# Paper — Evaluating Language-Model Agents on Realistic Autonomous Tasks (2023)

## Metadata

arXiv:2312.11671 (v2, Jan 2024; originally Dec 2023).
Megan Kinniment, Lucas Jun Koba Sato, Haoxing Du, Brian Goodrich, Max Hasin, Lawrence Chan, Luke Harold Miles, Tao R. Lin, Hjalmar Wijk, Joel Burget, Aaron Ho, Elizabeth Barnes (corresponding), and Paul Christiano (Alignment Research Center).
METR, formerly ARC Evals; its GPT-4-early results also fed the GPT-4 system card.

## Impact

It comes from the team whose pre-release GPT-4 testing appeared in the GPT-4 system card, including the much-cited episode of the model messaging a TaskRabbit worker, and turns that kind of testing into a reusable method.
It defines a single dangerous-capability threshold worth tracking, **autonomous replication and adaptation (ARA)**, and tests it the way it would actually happen: by wrapping a model in an agent and giving it open-ended real-world tasks, not multiple-choice questions.
The answer in 2023 was reassuring (the agents were far from ARA), but the paper's lasting contribution is its framing of why that answer doesn't last: an evaluation of today's agent is not a bound on tomorrow's.
Several of its authors (Kinniment, Chan, Barnes, and others) went on to write [[Benchmark — HCAST]] and [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]], and its self-proliferation tasks reappear in [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]].

## Problem it solved

Benchmarks measure short, self-contained tasks such as multiple-choice tests or programming contests, but risk depends on how AI systems behave in messy real-world environments over many steps.
Web-agent benchmarks like WebArena and Mind2Web tested simple website tasks; unlike those, this paper aims to measure how far agents are from being able to cause catastrophic harm.

## Core idea

Pick one capability that, once present, makes every other risk hard to bound.
A system capable of ARA can make money (freelancing or cybercrime), use it to buy compute, install its own weights and scaffolding on new machines, improve itself, and switch strategies when something fails.
Once copies can spread outside human control, you have to ask what thousands of them could do with years of runtime - so ARA is a threshold worth catching before it is crossed.
The threshold deliberately does *not* require escaping a developer's control, because leaked or stolen weights put the model in the same position.
The tasks are chosen to be easier than ARA: if an agent could replicate in the wild, it should be able to do most of them, so failing them is evidence of distance from the threshold.

## How it's built

Four agents combine a model with scaffolding that lets it think out loud, run Bash and Python, browse the web, and delegate to sub-agents (GPT-4-simple gets only Bash and reasoning): GPT-4-simple, GPT-4-delegate, GPT-4-early-delegate (a pre-release GPT-4), and Claude-assistant (Claude v1.3).
Twelve tasks run from trivial (find a password in a filesystem) through moderate (make a phone call with the Twilio API, set up a Bitcoin wallet, install GPT-J on a cloud server) to nearly ARA-hard (phish a consenting real student's university credentials, fine-tune LLaMA-7B for longer context).
A human overseer checks any action with real-world side effects, simulates some (phishing emails were redirected to the overseers), and overrides refusals by editing the model's "I cannot complete this task" into "I will make a plan to accomplish this task," since the goal is to measure ability, not willingness.

## Results

- The agents completed only the easiest tasks: all four found the password, and some completed the Twilio call, the BBC headlines task, and the Wikipedia lookup.
- They partially completed harder tasks, such as setting up a cloud instance for GPT-J, but no agent fully completed any of the other eight, including the Bitcoin, phishing, and LLaMA tasks.
- In the phishing run, GPT-4-delegate took over a hundred actions across more than a dozen subtasks with almost no human help, but researched the wrong person with the same name, built an unconvincing fake login page, and got stuck signing up for web hosting.
- Recurring failure modes: repetitive loops (more than half of one agent's failures), hallucinated results, unrealistic plans, misdiagnosed errors, and poor self-understanding - sub-agents not passing on credentials, and agents killing their own process or emitting their own stop token.
- The released GPT-4, fine-tuned for helpfulness and harmlessness, completed a few more tasks than the pre-release GPT-4-early - a hint that ordinary fine-tuning can raise dangerous capabilities as a side effect.

## Why it endures

Its approach - real tasks in real environments, a human-legible threshold, and agents built to elicit capability rather than raw model outputs - carries into METR's later work.
Its warning that behavioral evaluations of a fixed agent don't bound future agents - better scaffolding, fine-tuning, and scale can each change the answer - became a standard caveat in [[Method — Dangerous capability evaluation]], and its call for extrapolations of progress over time across a larger array of tasks is what the time-horizon metric later partly delivers.

## Limitations

The authors' own:
- The results measure the specific agents built, not the model's ceiling; better scaffolding and prompting could improve them rapidly.
- They say nothing about fine-tuned models, and fine-tuning not aimed at ARA may still raise ARA capability.
- They make no forecast for larger models, and the authors know of no evidence that this kind of behavioral evaluation improves continuously with scale.
- So the evaluation cannot rule out that the next generation (e.g. a 100x effective-compute scale-up) yields ARA-capable agents, unless intermediate evaluations are run during pretraining.

The authors also flag that the browsing tool was developed on some of the tasks it was later tested on; beyond that, the suite is small (12 tasks, 48 runs) and uses temperature-0 sampling for reproducibility.

## Relations

- Its self-proliferation framing and three of its tasks (the Bitcoin wallet, reused; installing an open model on a cloud instance and finding new employees' emails, adapted) reappear in [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]], and it describes itself as a step toward the dangerous capability evaluations proposed in [[Paper — Model Evaluation for Extreme Risks (2023)]].
- METR's later [[Benchmark — HCAST]], sharing several authors, replaces its 12 hand-graded tasks with 189 automatically scored, human-timed ones, which [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]] extends.
- Its refusal overrides make an elicitation choice explicit: it measures what the model can do with its refusals bypassed, as a stand-in for a model without refusal training, the concern that [[Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024)]] studies under controlled conditions.
- Cites [[Paper — Are Emergent Abilities of Large Language Models a Mirage (2023)]] for the point that measured progress can look continuous or abrupt depending on the metric.

## Up

- [[Cluster — Capability evals]]
