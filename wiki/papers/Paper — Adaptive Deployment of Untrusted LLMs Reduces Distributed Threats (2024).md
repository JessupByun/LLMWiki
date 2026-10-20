---
type: paper
status: mature
updated: 2026-10-19
summary: "Extends AI control to 'distributed threats', where no single bad action is catastrophic or conclusive, with a two-level deployment: per-task micro-protocols use a weaker trusted model to monitor and constrain the untrusted model, while a macro-protocol keeps a Bayesian credence that the untrusted model is misaligned across the whole deployment and shifts toward safer micro-protocols as evidence accumulates - cutting accepted backdoors by about 80% versus non-adaptive baselines at the same usefulness."
cluster: ["Scheming & AI control"]
sources: ["raw/papers/scheming-and-ai-control/Paper — Adaptive Deployment of Untrusted LLMs Reduces Distributed Threats (2024).pdf"]
tags: [ai-control, control-protocols, distributed-threats, monitoring]
aliases: ["arxiv:2411.17693", "Wen et al. 2024", "adaptive deployment"]
---

# Paper — Adaptive Deployment of Untrusted LLMs Reduces Distributed Threats (2024)

## Metadata

arXiv:2411.17693 (v1, Nov 2024).
Jiaxin Wen (Tsinghua), Vivek Hebbar (Redwood Research), and Caleb Larson (MATS) as equal first authors, with Aryan Bhatt, Ansh Radhakrishnan, Mrinank Sharma, Henry Sleight, Shi Feng, He He, Ethan Perez, Buck Shlegeris, and Akbir Khan.
Redwood Research, Anthropic, MATS, Tsinghua, George Washington University, NYU, and UCL; code released open-source.

## Impact

[[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] studied the high-stakes case: one backdoor is unacceptable, and high-quality auditing shuts the model down the moment it is caught.
Many real deployments aren't like that.
An occasional insecure line of code is harmful in aggregate but not catastrophic on its own, and a weak monitor sees only ambiguous evidence about any single output.
This paper names that the **distributed threat setting** and shows a protocol can exploit the one advantage the deployer has there: time.
By accumulating evidence about the untrusted model across thousands of tasks and adjusting how much it trusts the model, the deployment gets safer and more useful the longer it runs.

## Problem it solved

Shutting down on a single detected issue is unnecessary when no single action is catastrophic, and prone to false alarms when an honest model makes ordinary mistakes.
Prior control protocols also relied on a budget of trusted high-quality labor (simulated by GPT-4 itself) to audit outputs; this paper removes that affordance to study the realistic case where no single output gives conclusive evidence of misalignment.

## Core idea

Two levels of protocol.
**Micro-protocols** decide how to get an output for a single task, using a weaker trusted model to constrain the untrusted one: query the untrusted model directly, have the trusted model edit the untrusted output, have the trusted model write the code itself, or three new plan-based variants where the untrusted model only writes a plan and the trusted model implements it (optionally filtering or editing out the most suspicious step).
Each micro-protocol trades usefulness for safety differently, and each returns a monitor score.
The **macro-protocol** runs over the whole deployment.
It holds a credence over hypotheses about the untrusted model - how often it misbehaves, and how it trades off sneakiness against impact - updates that credence by Bayes' rule from each task's monitor scores, and picks whichever micro-protocol output maximizes expected utility given its current belief.
Early on, when the model might well be benign, it accepts the riskier but more useful outputs; as suspicious evidence accumulates, it shifts toward the safer ones.
Because the evaluation is worst-case, the macro-protocol's starting prior is not a guess: it is solved as a Nash equilibrium against the red team's distribution over attack strategies.

## How it's built

The testbed is AI Control's APPS backdooring setting: solve a sequence of competitive programming problems, where an output is harmful if it passes the tests but contains a backdoor exploitable by an input the untrusted model knows.
GPT-4 plays the untrusted model - a plain prompt for the benign version, a complex scaffold for inserting subtle backdoors for the malicious one - and GPT-3.5 Turbo is the trusted model, with Claude 3 Haiku, Sonnet, and Opus also tested as trusted models to vary the capability gap.
The red team sees the blue team's protocols and prompts in advance but cannot carry state between tasks or access the trusted model.
The blue team gets a reference sample of benign and attempted-malicious outputs to estimate how monitor scores look under each hypothesis, which the authors flag as a key affordance.

## Results

On 10,000-task sequences with GPT-3.5 as the trusted model, the adaptive approach keeps about 98.4% of GPT-4's usefulness while reaching 99.2% safety, meaning 80 harmful outputs out of 10,000, versus 86.4% safety from deploying the untrusted model unprotected.
It dominates both non-adaptive baselines (one doing the same expected-utility calculation with a fixed credence, one using a fixed suspicion threshold), already outperforming them after only 10 tasks and improving as the sequence lengthens.
Combining all micro-protocols beats using any single one.
The capability gap between trusted and untrusted models matters enormously: with the much weaker Claude 3 Haiku as the trusted model, detection quality collapses (AUROC 0.67) and the protocol keeps only 17.9% of usefulness at 99.1% safety.
The untrusted model struggles to predict how the weaker monitor will score its own outputs - a correlation of only +0.22 for GPT-3.5 as monitor, but higher for Claude 3 Opus (+0.45 in the text; the figure gives 0.40, and 0.45 for Claude 3 Sonnet), which the authors suspect is why a stronger trusted model didn't straightforwardly produce a better tradeoff: a smaller capability gap may make the monitor easier to simulate.

## Why it endures

It shows control doesn't have to be a single tripwire.
In the distributed setting, a deployer can learn about a possibly-misaligned model from its behavior over time and manage risk continuously, which is closer to how real deployments of coding agents would operate.
The micro/macro split is a reusable design pattern for any control protocol that must choose among options with different safety-usefulness tradeoffs.

## Limitations

The macro-protocol depends on a reference distribution of benign and malicious outputs; in practice that would require time-intensive manual evaluation, and real deployments will drift away from it over time.
Programs are about fifty lines and tasks are independent; longer, multi-file, or correlated tasks (like a pull request built from many small changes) and less structured domains are untested.
The red team is memoryless and its strategy - mixing misaligned and aligned outputs according to a sneakiness-versus-impact score - is representative but not exhaustive.
It remains unclear whether how GPT-4 exploits GPT-3.5 says anything about how a future model would exploit the current one.
The paper also reports the trusted GPT-3.5 monitor's AUROC inconsistently: its figure gives 0.91 (with Claude 3 Opus at 0.94), while one passage of text gives 0.94 for GPT-3.5.

## Relations

- Extends [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] from concentrated to distributed threats, reusing its APPS testbed and three of its protocols, and dropping its trusted high-quality auditing budget.
- The "concentrated failure" that [[Paper — A Sketch of an AI Control Safety Case (2025)]] scopes itself to is exactly what this paper contrasts with; that safety case cites this work among the control evaluations it builds on.
- Its macro-protocol and trusted-monitor micro-protocols build on [[Method — Trusted monitoring]], evaluated with [[Method — Control evaluation]].
- Its memoryless red team mirrors the stateless-model assumption whose consequences [[Paper — Subversion Strategy Eval - Can Language Models Statelessly Strategize to Subvert Control Protocols (2024)]] measures.
- [[Paper — Ctrl-Z - Controlling AI Agents via Resampling (2025)]] extends the same protocol family to multi-step agents.
- Its expected-harm objective is one [[Paper — Games for AI Control - Models of Safety Evaluations of AI Deployment Protocols (2024)]] shows its game formalism can represent.

## Up

- [[Cluster — Scheming & AI control]]
