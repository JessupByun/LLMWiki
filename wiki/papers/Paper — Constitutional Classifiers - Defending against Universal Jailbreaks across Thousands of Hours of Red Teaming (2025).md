---
type: paper
status: mature
updated: 2026-10-20
summary: "Guards a model with input and streaming output classifiers trained on synthetic data generated from a natural-language constitution of permitted and forbidden content; in over 3,000 estimated hours of paid red teaming, no one found a universal jailbreak extracting CBRN information at helpful-only detail across most queries, and an improved version blocks 95% of held-out jailbreak attempts on automated evaluations with only a 0.38% rise in production refusals."
cluster: ["Adversarial robustness & security"]
sources: ["raw/papers/adversarial-robustness-and-security/Paper — Constitutional Classifiers - Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming (2025).pdf"]
tags: [jailbreaking, defenses, classifiers, red-teaming, cbrn, misuse]
aliases: ["arxiv:2501.18837", "Sharma et al. 2025", "Constitutional Classifiers", "universal jailbreak defense"]
---

# Paper — Constitutional Classifiers - Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming (2025)

## Metadata

arXiv:2501.18837 (v1, Jan 2025).
Mrinank Sharma, Meg Tong, Jesse Mu, Jerry Wei, Jorrit Kruthoff, Scott Goodfriend, and Euan Ong (first block) and Jan Leike, Jared Kaplan, and Ethan Perez (last block) are core contributors, with Sharma and Perez as equal advisors and many others in between.
Anthropic's Safeguards Research Team, with co-authors from Haize Labs and one independent researcher; conducted under Anthropic's Responsible Scaling Policy.

## Impact

Every jailbreak paper in this wiki shows safety training can be bypassed; this one asks whether a deployed system can be made robust enough to hold up against determined, paid attackers, without becoming unusable.
Its answer is a qualified yes for one narrow but important target - **universal jailbreaks** that reliably extract detailed dangerous chemistry or biology across most questions in a domain.
Rather than making the model itself refuse better, it wraps the model in separate classifiers trained to recognize forbidden content, and tests them with over 3,000 hours of red teaming through an invite-only HackerOne bug bounty.
Anthropic developed it under its Responsible Scaling Policy as a misuse safeguard, and it is the clearest example here of defense through monitoring layers rather than through the model's own alignment.

## Problem it solved

Defenses built into the model, such as harmlessness training, keep being broken by jailbreak techniques like the "Do Anything Now" prompts, [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]], and [[Paper — Many-shot Jailbreaking (2024)]].
The worry for chemical, biological, radiological, and nuclear (CBRN) misuse is specifically a *universal* jailbreak: a reliable strategy that turns the model into one without safeguards across most queries, giving non-experts the step-by-step help they couldn't verify or obtain on their own.
A practical defense also has to stay cheap, keep responses streaming, refuse few legitimate requests, and adapt as threats change.

## Core idea

Write the rules down, then train guards on them.
A **constitution** lists, in plain language, categories of harmful content (for instance, acquiring restricted chemical precursors) and harmless content (for instance, common household chemicals and school chemistry).
A helpful-only model generates many queries and answers spanning both lists, augmented by translation, paraphrasing, system-prompt variations, and known jailbreak styles, plus attacks from automated red teaming.
Two fine-tuned classifiers are trained on this data: an **input classifier** that blocks harmful or obfuscating requests, and a **streaming output classifier** that scores the response token by token and halts generation the moment it predicts the full answer will be harmful.
Because the rules are text, updating the defense for a new threat means editing the constitution and regenerating data - echoing the constitution idea of [[Paper — Constitutional AI (2022)]], which used one to train the model itself.
The layers work in a "Swiss cheese" model: no single guard need be perfect.

## How it's built

**Red-teaming phase:** classifiers fine-tuned from Claude 3.5 Sonnet guard the same model, tuned for robustness at the cost of refusing about 44% of real Claude.ai traffic.
405 participants recruited through HackerOne tried to answer ten CBRN questions, with bounties up to $15,000; outputs counted as jailbroken if an automated rubric grader judged them to contain at least half the detail of an unguarded helpful-only model's answer.
**Deployment phase:** improved classifiers fine-tuned from Claude 3.5 Haiku with a chemical-weapons constitution, evaluated on automated attacks including held-out jailbreak styles, on production traffic, and for compute overhead.

## Results

- Across an estimated 3,000-plus hours of red teaming (mean estimate 4,720), none of 113 jailbreak reports answered all ten questions even at half the unguarded model's detail; at full unguarded detail, no single report answered more than five questions, and no red teamer more than six even combining all their reports.
- Red teamers did extract some harmful information, but the most successful strategies mostly exploited the rubric grader - harmless-sounding paraphrases and very long answers that scored well on volume - rather than beating the classifiers; known universal jailbreaks like many-shot jailbreaking were notably absent from successful attacks.
- One apparent universal jailbreak turned out to be an infrastructure bug that streamed up to 128 tokens after the output classifier had already flagged harm.
- On automated evaluations (not human red teaming), the improved system blocks over 95% of held-out jailbreak attempts versus 14% for the unguarded model, with a 0.38% absolute increase in refusals on production traffic and 23.7% inference overhead.

## Why it endures

It showed a practical architecture for misuse safeguards - constitution-defined synthetic data, separate input and streaming-output classifiers, and large-scale paid red teaming as the test - that can be updated without retraining the main model.
Its red-teaming lesson generalizes: attackers go after the weakest link, which here was the evaluation rubric and the deployment plumbing rather than the classifiers.
The authors also note the constitutional approach can target threats from model misalignment, connecting it to monitoring in [[Concept — AI control]].

## Limitations

The authors' own:
- The red-teamed prototype refused far too much to deploy, and red teamers still found some vulnerabilities.
- The red-team cohort represents only some adversaries, and new attack strategies will appear.
- Payouts began at half an unguarded model's detail, so red teamers may not have pushed for more complete jailbreaks.
- Red-teaming hours rely on self-reports for calibration, which may overestimate effort.
- The rubric grader had difficulty distinguishing benign knowledge from actionable information, and the evaluation covered only a chatbot setting with preset queries.
- Vulnerabilities will likely emerge with continued testing, so complementary defenses are needed.

By design, the defense targets universal jailbreaks in specific domains; jailbreaks that extract some harmful information, or that work on only a few queries, are not ruled out.

## Relations

- Defends against the jailbreak techniques of [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] and [[Paper — Many-shot Jailbreaking (2024)]] (see [[Concept — Jailbreaking (LLMs)]]), which its streaming output classifier is designed to mitigate.
- Echoes the constitution idea of [[Paper — Constitutional AI (2022)]] (though without citing it), applied to training guard classifiers rather than the model itself, and reuses the automated red teaming of [[Paper — Red Teaming Language Models with Language Models (2022)]] to generate training attacks.
- A deployed instance of the monitoring layers studied in [[Method — Trusted monitoring]], aimed at misuse rather than a scheming model.
- Its CBRN focus targets the same misuse risk as [[Benchmark — WMDP]] and the dangerous-capability concerns of [[Method — Dangerous capability evaluation]].

## Up

- [[Cluster — Adversarial robustness & security]]
