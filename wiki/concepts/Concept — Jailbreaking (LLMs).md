---
type: concept
status: in-progress
updated: 2026-10-20
summary: "Getting an aligned language model to produce content or behavior its safety training was meant to prevent, whether through hand-crafted prompts or automated optimization."
cluster: ["Adversarial robustness & security"]
sources: []
tags: [security, jailbreak, adversarial-examples, alignment]
aliases: ["jailbreak"]
---

# Concept — Jailbreaking (LLMs)

## Definition

A jailbreak is an input, or family of inputs, that causes an aligned language model to produce content or exhibit behavior its safety training was specifically meant to prevent - while leaving the model's underlying capability to produce that content fully intact.
The model "knows how" to comply; the jailbreak is what gets past the trained-in refusal.

Three lineages have converged on this term.
**Manual jailbreaks** are hand-engineered prompts - role-play framings, hypothetical scenarios, instructions to begin with an affirmative word - that require human ingenuity and are comparatively brittle, easily patched by the next round of alignment training.
**Gradient-optimized jailbreaks**, demonstrated at scale by [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] via [[Method — Greedy Coordinate Gradient (GCG)]], instead search for an adversarial suffix using gradients and optimization, the same way a vision adversarial example is found, rather than relying on a human's intuition for what scenario will confuse the model.
**In-context jailbreaks**, demonstrated at scale by [[Paper — Many-shot Jailbreaking (2024)]], need neither cleverness nor gradient access: stuffing a long context window with hundreds of fake dialogue turns of compliance reliably overrides safety training purely through ordinary in-context learning.

## Why it works

The affirmative-response trick that GCG's objective exploits is itself borrowed from the manual jailbreaking community: getting a model to merely *begin* its response with "Sure, here is..." tends to switch it into a "mode" where it continues the objectionable content anyway, rather than reconsidering and refusing partway through.
What automated methods add is the ability to search this space far more thoroughly and reliably than manual trial-and-error, and - because the resulting suffix is optimized jointly against multiple models - to find suffixes that **transfer** to models the attacker never had access to, purely black-box.

This makes jailbreaking a token-space instance of the same phenomenon as [[Concept — Adversarial examples]]: a deliberately optimized input that drives a model outside its intended behavior.
The disanalogy is what counts as "small": an image attack must be imperceptible, but a jailbreak prompt can look like arbitrary nonsense, since there is no equivalent constraint that a user-facing prompt must look natural.

## Relations

- Automated and made reliably transferable by [[Method — Greedy Coordinate Gradient (GCG)]], introduced in [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]].
- A token-space relocation of [[Concept — Adversarial examples]], sharing its transferability property.
- Exploits the same prompt-as-attack-surface property that [[Concept — In-context learning]] names as the mechanism behind prompt injection.
- Measured by [[Benchmark — AdvBench]].
- Raises the open question, not resolved by the paper that automates it, of whether post-hoc RLHF-style alignment training ([[Method — Reinforcement learning from human feedback (RLHF)]]) can ever structurally close this gap - a question [[Cluster — Scheming & AI control]] takes up directly.
- [[Paper — Many-shot Jailbreaking (2024)]] traces its in-context jailbreak mechanism to [[Concept — Induction heads]], the same circuit behind ordinary in-context learning, giving this concept's newest lineage a genuine mechanistic account.
- [[Paper — Red Teaming Language Models with Language Models (2022)]] is a direct methodological precursor: automating the *discovery* of many diverse failure categories with a second LM, rather than automating one single reusable jailbreak attack.
- Distinct from indirect prompt injection ([[Paper — Not What You've Signed Up For - Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection (2023)]]): a jailbreak is delivered through the user's own prompt to circumvent the model's own safety training, while an indirect injection arrives through third-party content an application retrieves, and doesn't need the target model to have any safety training to defeat in the first place.
- [[Paper — Constitutional Classifiers - Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming (2025)]] defends against universal jailbreaks with constitution-trained input and output classifiers, holding up against over 3,000 hours of red teaming.
- [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]] shows a white-box route: ablating one activation direction removes refusal in open-weight models with little capability loss.
- [[Paper — Improving Alignment and Robustness with Circuit Breakers (2024)]] defends by disrupting harmful internal representations rather than training refusals, generalizing to attacks it never saw.
- [[Paper — Adaptive Attacks on Trusted Monitors Subvert AI Control Protocols (2025)]] turns jailbreaking on a safety monitor, with the monitored model itself as the attacker.
- [[Paper — The Instruction Hierarchy - Training LLMs to Prioritize Privileged Instructions (2024)]] treats jailbreaks and prompt injection as one missing-privilege problem (see [[Concept — Prompt injection]]); training only on injection and extraction data raised robustness to ChatGPT jailbreaks from 37.4% to 71.2% on GPT-3.5 Turbo.
- [[Paper — Fine-tuning Aligned Language Models Compromises Safety, Even When Users Do Not Intend To (2023)]] moves jailbreaking from prompts to weights: fine-tuning on 10 harmful or harmless-looking examples removes safety training; [[Paper — Tamper-Resistant Safeguards for Open-Weight LLMs (2024)]] is a defense against that weight-space route.
- [[Paper — Jailbroken - How Does LLM Safety Training Fail (2023)]] explains why manual jailbreaks work - competing objectives (safety versus instruction-following and text continuation) and mismatched generalization (inputs like Base64 that pretraining covers but safety training doesn't) - and is one of the cited sources of the prefix-injection trick GCG's objective automates.
- [[Paper — Deliberative Alignment - Reasoning Enables Safer Language Models (2024)]] defends by training a reasoning model to consult its safety spec in its chain of thought, in an ablation with encoded and non-English safety data removed, still generalizing to those jailbreaks.

## Up

- [[Cluster — Adversarial robustness & security]]
