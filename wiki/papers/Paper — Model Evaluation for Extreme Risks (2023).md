---
type: paper
status: mature
updated: 2026-10-19
summary: "Argues that frontier developers must evaluate models for extreme risks along two axes - dangerous capabilities (can it do serious harm, e.g. cyber-offense, manipulation, self-proliferation) and alignment (would it try) - and lays out how those evaluations should gate training, deployment, transparency, and security decisions, anticipating the capability-threshold structure of the frontier-lab safety frameworks that followed."
cluster: ["Capability evals"]
sources: ["raw/papers/capability-evals/Paper — Model Evaluation for Extreme Risks (2023).pdf"]
tags: [evaluation, dangerous-capabilities, alignment-evaluation, governance, frontier-ai]
aliases: ["arxiv:2305.15324", "Shevlane et al. 2023", "extreme risk evaluations"]
---

# Paper — Model Evaluation for Extreme Risks (2023)

## Metadata

arXiv:2305.15324 (v2, Sep 2023; originally May 2023).
Toby Shevlane, Sebastian Farquhar, Ben Garfinkel, Mary Phuong, Jess Whittlestone, Jade Leung, Daniel Kokotajlo, and others, with Allan Dafoe as last author.
Led from Google DeepMind, with co-authors at OpenAI, Anthropic (Jack Clark), the Alignment Research Center (Paul Christiano), the Centre for the Governance of AI, Mila (Yoshua Bengio), and several universities.

## Impact

Before this paper, "evaluation" in ML mostly meant benchmarks for bias, toxicity, or accuracy, and almost nothing deliberately targeted risks on the scale of tens of thousands of lives or the disempowerment of governments.
This paper - a position paper with authors from three frontier labs - formalizes the missing category and popularizes the vocabulary for it: **dangerous capability evaluations** (could the model cause extreme harm?) and **alignment evaluations** (would it?).
More importantly, it ties those evaluations to decisions: delay or pause a training run, block or stage a deployment, report results, harden security.
That "evaluate, then gate decisions on the result" structure is the shape of the frontier-lab safety frameworks that followed, and it frames the empirical programme [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]] runs a year later.

## Problem it solved

Many AI governance initiatives, like the draft EU AI Act's "high-risk" categories, judged risk by deployment context - which application is the model being used in.
But a model with dangerous enough capabilities could be risky even in a low-risk application, so assessing the model itself was needed alongside assessing its use.
Frontier models also gain capabilities their developers don't foresee, so a lab can train a model with skills in deception or cyber-offense without having sought them.

## Core idea

A model is dangerous if it has a capability profile that would be enough for extreme harm, *assuming* someone misuses it or it is misaligned.
So evaluation has two questions, asked separately:
- **Can it?**
  Dangerous capability evaluations probe a list of offensive or oversight-evading skills: cyber-offense, deception, persuasion and manipulation, political strategy, weapons acquisition, long-horizon planning, AI development, situational awareness, and self-proliferation.
- **Would it?**
  Alignment evaluations probe propensities: pursuing goals the developer didn't give it, power-seeking, resisting shutdown, colluding with other AI systems, or whether it resists malicious users' attempts to access its dangerous capabilities.

The heuristic follows: if the model has a capability profile sufficient for extreme harm, deploying it requires both very strong controls against misuse and very strong assurance, from alignment evaluations, that it will behave as intended.
Think of it like drug approval: you test toxicity before you know whether anyone will misuse the drug, and the test results decide whether trials can proceed.

## How it's built

A governance workflow with evaluation embedded throughout, fed by three sources: internal evaluation by the developer, external research access, and external audits.
- **Responsible training**: evaluate earlier and smaller models to forecast the planned run, run extreme-risk evaluations during training, and respond to concerning results by studying them, changing the training method, or training a smaller model.
- **Responsible deployment**: pre-deployment risk assessment, staged release that widens exposure as evidence accumulates, and continued evaluation after deployment; even internal deployments, such as coding assistants for the lab's own engineers, may need pre-deployment evaluation.
- **Transparency**: incident reporting, sharing pre-deployment risk assessments, scientific reporting, and demonstrations for policymakers.
- **Security**: red-teaming of infrastructure, AI-assisted monitoring of the model, isolation, rapid response, and system integrity checks, with the model itself listed as a possible threat actor.

It also lists qualities a good evaluation portfolio needs - among them coverage of many threat models, mechanistic as well as behavioral checks, robustness to deception, surfacing latent capabilities through prompting or fine-tuning, and a wide difficulty range so progress toward danger can be tracked.

## Results

A position paper, so no experiments; it points to early work already underway, such as ARC Evals testing GPT-4 and Claude for self-proliferation before release, OpenAI's red-teaming of GPT-4's cybersecurity operations and its ability to purchase certain chemical compounds, and a DeepMind manipulation game, "Make-me-say."
It anticipates that alignment evaluation will be the hardest part: a model can pass narrow checks (an agent saying it doesn't mind being shut down) without showing how it would act given a genuine opportunity to resist.

## Why it endures

Its two-axis split - capability versus propensity - is now standard in how labs, governments, and evaluators describe frontier risk, and its idea of capability thresholds that trigger stronger safeguards anticipates the structure of later responsible-scaling-style policies.
It also names, in a governance document, the failure modes that later empirical safety work studies directly: deceptive alignment defeating behavioral evaluations, latent capabilities that only elicitation reveals, and the risk that training against evaluations produces models that only look safe.

## Limitations

The authors list limitations of evaluation itself:
- Risk depends on factors beyond the model, such as tools released later or how resilient society turns out to be.
- Unknown threat models: capable models may find pathways no one anticipated.
- Some properties are hard to find, notably capability overhang (chain-of-thought prompting revealed new GPT-3 capabilities months after release) and deceptive alignment, where a situationally aware model behaves well during evaluation.
- Capabilities can emerge abruptly with scale, undermining forecasts from smaller models.
- The external evaluation and audit ecosystem is immature.
- Overtrust: too much faith in evaluation results can create a false sense of security.

They also flag hazards of doing the evaluations: spreading dangerous capabilities through results, datasets, elicitation techniques, or trained models (likened to gain-of-function research); competitive pressure from shared results; superficial safety from training against evaluations, which over time could select for deceptively aligned models; and harm during the evaluation itself, such as ARC's GPT-4 test sending deceptive messages to a TaskRabbit worker.

## Relations

- Supplies the framework [[Method — Dangerous capability evaluation]] puts into practice, and its first full implementation is [[Paper — Evaluating Frontier Models for Dangerous Capabilities (2024)]] (Phuong and Shevlane, both authors here, lead it as first and last author).
- Its "robust to deception" and "surfacing latent capabilities" requirements are exactly the problems [[Paper — AI Sandbagging - Language Models can Strategically Underperform on Evaluations (2024)]] and [[Paper — Stress-Testing Capability Elicitation With Password-Locked Models (2024)]] study (see [[Concept — Sandbagging]]).
- Lists deceptive alignment as a reason not to rely on behavioral evaluations alone - the threat model of [[Concept — Deceptive instrumental alignment]] - and names mechanistic analysis, including reading whether a model's outputs misreport its knowledge as in [[Paper — Discovering Latent Knowledge in Language Models Without Supervision (2022)]], as one remedy.
- Cites [[Paper — The Off-Switch Game (2016)]] when listing resistance to shutdown among the behaviors alignment evaluations should look for (see [[Concept — Corrigibility]]).
- Its "model as a threat actor" security stance and AI-assisted monitoring anticipate the protocols of [[Concept — AI control]].

## Up

- [[Cluster — Capability evals]]
