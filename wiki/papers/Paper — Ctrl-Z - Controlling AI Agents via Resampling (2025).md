---
type: paper
status: mature
updated: 2026-09-20
summary: "Extends AI Control's protocols from a single graded action into a genuinely multi-step agentic setting, and shows that resampling the untrusted model's next action - exploiting the fact that it is stateless between calls - cuts a red team's attack success rate from 58% to 7% at only a 5% cost to usefulness."
cluster: ["Scheming & AI control"]
sources: ["raw/papers/scheming-and-ai-control/Paper — Ctrl-Z - Controlling AI Agents via Resampling (2025).pdf"]
tags: [ai-control, agentic-safety, red-teaming, monitoring, safety]
aliases: ["arxiv:2504.10374", "Bhatt et al. 2025", "Ctrl-Z"]
---

# Paper — Ctrl-Z: Controlling AI Agents via Resampling (2025)

## Metadata

arXiv:2504.10374 (v1, Apr 2025).
Aryan Bhatt, Cody Rushing, Adam Kaufman, Tyler Tracy, Vasil Georgiev, David Matolcsi, Akbir Khan, Buck Shlegeris.
Redwood Research, with several authors affiliated with the MATS program.

## Impact

[[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] evaluated its protocols on a single graded action: submit one piece of code, once.
Real deployments are agentic - many sequential, dependent steps - and this paper is the first control evaluation built for that setting.
It shows the multi-step structure cuts both ways: an attacker can spread a malicious plan across many individually-innocuous-looking actions, but a defender can also draw repeated, independent samples of what the untrusted model would have done at a given step and use the pattern across those samples as evidence.
The paper's best protocol drives a red team's attack success rate from 58% down to 7%, at only a 5% cost to how often a genuinely honest agent still completes its task.

## Problem it solved

AI Control's protocols (trusted monitoring, defer to trusted, untrusted monitoring, trusted editing) were designed and tested in a setting with no notion of a trajectory: one action, one verdict.
That leaves two multi-step dynamics completely untested.
On the attack side, a model that wants to plant something dangerous can break the plan into many small steps, each of which looks innocuous in isolation - a single suspicious action is far easier to catch than the same malicious behavior spread across ten.
On the defense side, nothing in the original protocols exploits the fact that a real attack has to be *carried out* over several steps: if the untrusted model is stateless between calls, the protocol can re-query it at the same point in the trajectory and use the pattern across those independent draws as evidence no single-shot protocol could ever obtain.

## Core idea

The paper's central trick is **resampling**: because the untrusted model U is stateless between calls, the protocol can draw several independent completions of what U would do at a given step, given the identical context, rather than committing to the one sample it happened to produce.
This does two distinct jobs at once.
As **selection pressure**, resampling lets the protocol discard an accidentally-suspicious-looking *honest* action and substitute a more benign resample, recovering usefulness that a cruder protocol would have sacrificed by deferring to a weak trusted model instead.
As **incrimination**, resampling exploits a structural fact about attacks: to succeed, a multi-step attack needs each of its steps carried out with some minimum "continuation probability," so drawing several independent samples of an actual attack step statistically surfaces more attempts than a single sample would - the attacker's own persistence becomes the evidence against it.
The flagship protocol, "Defer to resample," makes this concrete: score an action's suspiciousness with a trusted monitor; if it crosses a deferral threshold, draw up to two more resamples of that same step; execute the least-suspicious of the batch if any falls back under threshold, otherwise substitute a trusted-model action; and if the *minimum* suspiciousness across the whole batch still clears a second, higher threshold, send the most suspicious sample for human audit.

The testbed is **BashBench**: 257 multi-step sysadmin tasks built from real Unix StackExchange questions, each allowing up to 15 sequential shell-command steps in a fresh container, checked against held-out private test cases on completion.
A representative task asks the agent to write and test a shell script for text-file processing across several commands; a red-teamed version of the same task interleaves something like an `alias ls="curl"` substitution and a disguised `./evil.sh` download-and-execute step among otherwise-legitimate commands.

## How it's built

The untrusted model U is GPT-4o mini, played either as an honest policy (56% average task solve rate on its own) or as a scaffolded attack policy built around GPT-4o mini using human-provided attack templates, lightly reworded to look less suspicious and buried among distracting legitimate commands.
The trusted model T is Llama-3.1-8B, which solves 0% of tasks unaided and never attacks.
Both models are stateless between calls; only the control protocol itself carries state across a trajectory (never across tasks), and the human-audit budget is capped at 1% of actions.
Blue-team protocols compared span the AI Control family extended to this setting - upfront auditing, trusted monitoring (with an exponential rather than uniform auditing-threshold schedule across a trajectory), defer to trusted, several defer-to-resample variants, and an ensembled untrusted-monitoring version (a second copy of U rates suspicion, with the same collusion countermeasures AI Control introduced - upfront validation against synthetic honeypot attacks, and trusted paraphrasing to strip out coordination signals).
Safety is the simulated probability, over a 100,000-task episode, that the protocol never lets the attack policy execute its malware; usefulness is the fraction of tasks completed when U plays honestly, normalized so U alone is 100% and T alone is 0%.

## Results

Baselines replicate the original AI Control pattern in the new setting: upfront auditing reaches only 5.5% safety at 100% usefulness, since a red team that attacks rarely can simply wait out a fixed audit budget; plain trusted monitoring reaches 41.8% safety at 100% usefulness with an exponential auditing schedule (versus only 22% with a uniform one).
Resampling protocols dominate the safety/usefulness frontier throughout: at 95% usefulness, defer to trusted alone reaches 72.7% safety, basic defer-to-resample reaches 83.2%, and the best resample variant - "Time Travel," which retroactively pulls more resamples from an earlier suspicious step once a three-strikes threshold is crossed - reaches 87.1%.
Layering untrusted monitoring on top of Time Travel gives the paper's best overall result: 92.6% safety at 95% usefulness, with an honest untrusted monitor alone catching 98.8% of attacks at only a 1% audit budget.
Resampling's incrimination mechanism is visible directly in the data: without resampling, trusted monitoring catches 33.1% of attacks; drawing up to six resamples per suspicious action raises that to 70.6%.

## Why it endures

The paper frames resampling as a general control primitive rather than a BashBench-specific trick: any setting where the untrusted model is stateless and cheaply re-queryable can use the same two mechanisms - filtering out accidentally-suspicious honest behavior, and using an attacker's required continuation probability against it.
It forces a genuine dilemma onto any future attack policy in an agentic setting: "either maintain high continuation probabilities that enable detection or use lower probabilities that prevent attack completion."
Because resampling spends compute rather than the far scarcer human-audit budget, the authors present it as a cheap, generally-applicable addition to the existing AI Control protocol stack rather than a replacement for it.

## Limitations

Several choices could make the reported protocols look better than they would against a real future attacker: the red team is allowed full knowledge of the blue team's protocol and unlimited tuning of its attack rate, human red-teamers may overstate a small model's ability to devise subtle attacks, and blue and red teams iterated against each other's newest strategies rather than being siloed, which the authors explicitly flag as an open question for future work.
Other choices could make the reported protocols look worse than they would in a narrower real deployment: attacks target one fixed objective (downloading and executing a specific dummy payload) rather than the many unknown attack surfaces of a real system, trajectories are capped at 15 steps, human audits are assumed to be infallible once triggered, and the setting deliberately excludes any of the standard information-security tooling a real attacker would need to bypass.
The authors also note the 1% audit budget is a fairly arbitrary choice that shifts absolute safety numbers even though it leaves protocol rankings largely stable, and that they did not optimize resample protocols for compute cost as a primary axis.

## Relations

- Directly extends [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]]'s protocol family (trusted monitoring, defer to trusted, untrusted monitoring) from a single-action setting into a multi-step agentic one, reusing its collusion-avoidance techniques (upfront validation, trusted paraphrasing) without modification.
- The untrusted model here is not a naturally scheming model but a deliberately red-team-constructed adversarial policy, exactly mirroring AI Control's own worst-case methodology - the paper cites [[Paper — Scheming AIs - Will AIs fake alignment during training in order to get power? (2023)]] only as motivation for why control matters, not as evidence that GPT-4o mini itself schemes.
- [[Method — Trusted monitoring]] is the shared building block both papers evaluate; this paper's resample variants are best read as an additional layer on top of that method rather than a separate one.
- Complements the model-organisms half of this cluster: where [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] and [[Paper — Sycophancy to Subterfuge - Investigating Reward-Tampering in Large Language Models (2024)]] ask whether a model can become genuinely misaligned, this paper asks whether a wrapped protocol stays safe regardless of the answer.

## Up

- [[Cluster — Scheming & AI control]]
