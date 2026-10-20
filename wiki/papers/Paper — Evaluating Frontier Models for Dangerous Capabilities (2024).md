---
type: paper
status: mature
updated: 2026-10-19
summary: "Google DeepMind's prototype dangerous-capability evaluation programme, piloted on Gemini 1.0 across persuasion and deception, cybersecurity, self-proliferation, and self-reasoning: finds no strong dangerous capabilities but early warning signs, with persuasion the most mature, and introduces an 'expert bits' measure of how much help an agent needs to finish a task it fails alone."
cluster: ["Capability evals"]
sources: ["raw/papers/capability-evals/Paper — Evaluating Frontier Models for Dangerous Capabilities (2024).pdf"]
tags: [evaluation, dangerous-capabilities, persuasion, cybersecurity, self-proliferation, self-reasoning, agents]
aliases: ["arxiv:2403.13793", "Phuong et al. 2024", "Gemini dangerous capability evaluations"]
---

# Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)

## Metadata

arXiv:2403.13793 (v2, Apr 2024; originally Mar 2024).
Mary Phuong (first author) and Toby Shevlane (last author), with core contributors Matthew Aitchison, Elliot Catt, Sarah Cogan, Alexandre Kaskasoli, Victoria Krakovna, David Lindner, and Matthew Rahtz, and others including Anca Dragan, Rohin Shah, and Allan Dafoe.
Google DeepMind.

## Impact

[[Paper — Model Evaluation for Extreme Risks (2023)]] said frontier labs should test models for dangerous capabilities before deciding to deploy them; this paper is that lab actually doing it, publicly, on its own flagship models.
It runs evaluations across four risk areas on Gemini 1.0 Nano, Pro, and Ultra, publishes prompts and rater instructions (though not code), and reports a careful negative: no strong dangerous capabilities yet, but rudimentary versions of all of them in the stronger models, with persuasion and deception the most developed.
Its most reusable idea is methodological - a way to measure how *close* an agent is to a capability it can't yet demonstrate, which is what an early-warning system needs.

## Problem it solved

Popular capability benchmarks test broad skills like coding or verbal reasoning, which only indirectly say anything about risk.
Most safety evaluations test whether a model refuses harmful requests, which says nothing about what it could do if it didn't refuse or was misused.
And a pass/fail evaluation gives no warning: by the time a model passes, the capability is already there.

## Core idea

Measure capability in units of help needed.
Suppose an agent fails a self-proliferation task, say installing an open-source model on a cloud server, in all ten tries.
Is it hopeless, or one small nudge away?
The paper splits each task into 3-7 milestones and starts the agent at each one, so it can see which steps the agent can and can't do.
Where the agent still fails a step, a researcher looks at 16 candidate actions the model proposed and picks the first one that makes progress - and the agent is charged the number of **bits** needed to point to that choice.
If even that fails, the agent is charged the bits needed to communicate a hand-written "golden" solution, measured by how surprised the model is by it.
The total is a smooth number: Pro and Ultra needed only 10 and 9 bits of expert help to finish a Bitcoin-wallet task they never completed on their own, while another task (finding a new employee's email address) stayed far out of reach.
A smooth measure like this could, in principle, become the y-axis of a scaling plot that forecasts when a capability will appear.

## How it's built

**Persuasion and deception (four evaluations, 100 UK participants per model per study).** Agents converse with real people for about 30 minutes: persuading them to donate a £20 bonus to charity (Money Talks), building rapport in a role-play (Charm Offensive), covertly getting them to click a suspicious link, find an email address, or run code (Hidden Agenda), and convincing them of wrong quiz answers when they know the agent might lie (Web of Lies).
**Cybersecurity.** Agents with a Bash shell attack 13 in-house capture-the-flag challenges built to stay out of training data, 81 InterCode-CTF challenges, and 13 harder Hack The Box challenges, each with 10 attempts of 20 shell interactions; separately, models classify security patches and vulnerable functions.
**Self-proliferation.** 11 agentic tasks with a shell and web browser, covering acquiring resources, running AI agents on the internet, and self-improvement, with every action approved by an overseeing researcher.
**Self-reasoning.** Tasks where succeeding requires noticing that the agent should change something about itself - increasing its own context length, turning off a safety filter, or recognizing which questions fall past its knowledge cutoff and need a search - at three hint levels.
**Forecasting.** Eight professional forecasters predicted when the evaluations would be passed.
Models were evaluated without safety filters, to measure underlying capability rather than product safety.

## Results

- **Persuasion is the most mature.** In Hidden Agenda's hardest task, Ultra got 14% of participants to run suspicious code; in Web of Lies, Pro shifted participants toward wrong answers even though they knew the agent might be lying, though a small human baseline did better; in Money Talks, Pro had only a small positive effect on donations relative to no chatbot.
- Agents were capable of duplicity - reasoning "I will pretend to look it up," then telling the user "Let me check... It seems that" followed by a lie - and showed sycophancy in role-play, adapting their stated views to the participant's.
- **Cyber offense is weak.** Ultra solved 24 of 81 InterCode-CTF challenges and 3 of 13 in-house challenges (two trivial web vulnerabilities and a passwordless-sudo privilege escalation); no model solved any Hack The Box challenge.
- Vulnerability detection was modest: Ultra reached 74% accuracy classifying security patches in the older dataset, but near chance (53.8%) classifying vulnerable functions.
- **Self-proliferation is out of reach end-to-end but partly in reach.** No model completed any task unaided, but both larger models completed several sub-tasks, and expert best-of-N showed models sometimes generate a good action and rank it below worse ones.
- Agents failed in recognizable ways: rarely searching for missing information, recording failed commands as successes, and drifting from the objective on long tasks.
- **Self-reasoning is limited.** Ultra passed only the easiest introspection task without hints; otherwise models succeeded only with strong guidance, though larger models did better than smaller ones.
- Pro often performed similarly to Ultra and sometimes better, which the authors read as a reason to watch models just behind the frontier and as a possibility that results are sensitive to fine-tuning differences.
- Forecasters' median estimates for when evaluations would be passed ranged from 2025 to 2029: the in-house CTFs around 2025-2026, Hack The Box around late 2028, about half the self-proliferation tasks (5 of 11) by 2025-2026 and 10 of 11 by late 2026-2029, and self-reasoning very soon.

## Why it endures

Google DeepMind carried this suite into later Gemini evaluations, and its human-participant persuasion studies and milestone-based agent tasks became common patterns in pre-deployment testing.
The expert-bits measure addresses a real gap in [[Method — Dangerous capability evaluation]]: a pass/fail threshold says nothing until it is crossed, while a measure of distance-to-capability can warn ahead of time - the same goal as [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]]'s time horizon, approached from the opposite direction.

## Limitations

The authors' own:
- The persuasion agent is rudimentary (one hidden reasoning step per turn, no tools), with less scaffolding iteration than other areas.
- Persuasion results come from a lab setting, and how they transfer to real-world social contexts is an open question.
- The cyber setup has no graphical interface or internet access, and Hack The Box solutions may be in training data.
- Fine-tuning recipes change, so later versions of the same models may perform differently, and results may be sensitive to fine-tuning differences between models.
- The programme is not comprehensive - CBRN evaluations are only a preliminary framework - and the paper does not attempt a risk assessment.
- Finding the true minimum help an agent needs trades off against evaluation time - expert best-of-N, for instance, was run only where all unguided attempts failed - so bit counts approximate rather than pin down distance to capability.

The negative results also inherit the elicitation problem: they show what these agents did, which is a lower bound on what the models can do.

## Relations

- The empirical follow-through on [[Paper — Model Evaluation for Extreme Risks (2023)]], led by two of its co-authors (Phuong first, Shevlane last), and the most complete instance in this wiki of [[Method — Dangerous capability evaluation]].
- Its self-proliferation tasks build on Kinniment et al.'s autonomous-replication evaluations at ARC Evals, now METR, whose later [[Paper — Measuring AI Ability to Complete Long Software Tasks (2025)]] measures general agentic capability rather than specific dangerous tasks.
- Its self-reasoning evaluations target a prerequisite for [[Concept — Sandbagging]] and for evading oversight, and it notes a misaligned model might hide self-reasoning ability on exactly these tests.
- Its persuasion transcripts show the [[Concept — Sycophancy]] documented by [[Paper — Towards Understanding Sycophancy in Language Models (2023)]], which it cites, now used as a persuasion tactic.
- Cites [[Paper — Are Emergent Abilities of Large Language Models a Mirage (2023)]]'s argument that abrupt capability jumps may reflect discontinuous metrics, motivating its smooth expert-bits measure.
- Reuses one and adapts two of [[Paper — Evaluating Language-Model Agents on Realistic Autonomous Tasks (2023)]]'s self-proliferation tasks.

## Up

- [[Cluster — Capability evals]]
