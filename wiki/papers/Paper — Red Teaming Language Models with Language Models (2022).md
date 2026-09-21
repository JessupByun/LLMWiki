---
type: paper
status: mature
updated: 2026-09-20
summary: "Uses one language model to automatically generate natural-language test cases designed to elicit harmful behavior from a second target model, finding tens of thousands of diverse failures - offensive replies, verbatim training-data leakage, leaked phone numbers and emails, demographic bias, and dialogue-level escalation - without a single test case being hand-written."
cluster: ["Adversarial robustness & security"]
sources: ["raw/papers/adversarial-robustness-and-security/Paper — Red Teaming Language Models with Language Models (2022).pdf"]
tags: [red-teaming, jailbreak, safety, evals]
aliases: ["arxiv:2202.03286", "Perez et al. 2022"]
---

# Paper — Red Teaming Language Models with Language Models (2022)

## Metadata

arXiv:2202.03286 (v1, Feb 2022).
Ethan Perez, Saffron Huang, Francis Song, Trevor Cai, Roman Ring, John Aslanides, Amelia Glaese, Nat McAleese, Geoffrey Irving.
DeepMind, with Ethan Perez also at NYU.

## Impact

Manual red-teaming - humans trying to make a deployed model misbehave - is the standard pre-deployment safety check, and it is exactly as expensive and as limited as the number of person-hours a team can afford.
This paper automates it: use a second "red" language model to generate natural-language test cases specifically designed to provoke a target model into failing, then use a classifier to automatically flag which ones succeeded.
Applied to DeepMind's 280B-parameter Dialogue-Prompted Gopher, this finds tens of thousands of diverse failures without a human writing a single test case, and the generated failures compare favorably in both diversity and difficulty to a prior manually-written red-teaming dataset - evidence that language models can be turned into a genuine part of the solution for finding their own failure modes, not just a source of them.

## Problem it solved

Every prior approach to finding LM failures - hand-written adversarial examples, template-based test generation, crowdworker red-teaming - is bottlenecked by human effort, both in how many test cases can be produced and how creative or diverse they can be.
The paper's epigraph makes the stakes concrete: Microsoft's Tay chatbot was manipulated into racist and sexual tweets within a day of release, with the postmortem admission "we had a critical oversight for this specific attack" - exactly the kind of blind spot a red-teaming process limited by human imagination is prone to.
This differs from the existing adversarial-examples literature in an important way: gradient-based NLP attacks (character-level edits, universal trigger strings) tend to produce arbitrary or outright unintelligible text, while this paper's test cases are fluent, readable natural language questions a real user might plausibly ask.

## Core idea

A three-stage pipeline: a red language model generates a test-case prompt, the target model replies, and a classifier flags whether the reply constitutes a failure.
Four distinct methods for generating test cases are compared, forming an explicit difficulty-versus-diversity tradeoff rather than one method dominating: **zero-shot** generation from a simple prompt template (most diverse, lowest hit rate); **stochastic few-shot**, which re-feeds the red model its own past successful test cases as in-context examples, weighted toward the ones that elicited the most offensive replies; **supervised learning**, fine-tuning the red model directly on its own successful zero-shot test cases; and **reinforcement learning**, training the red model with a KL penalty to maximize the classifier's predicted harmfulness score, which finds the single highest failure rate but at the cost of collapsing onto a narrow set of near-duplicate prompts.
Failure detection itself is method-specific: a dedicated offensiveness classifier (a fine-tuned 1.4B-parameter model, more accurate than the Perspective API or a comparable prior classifier) for offensive language, and simple regex/substring matching for training-data leakage and personally identifiable information.

## How it's built

The target model is Dialogue-Prompted Gopher: the 280B-parameter pretrained Gopher language model turned into a chatbot purely by prepending a hand-written natural-language persona prompt, with no additional fine-tuning.
The paper documents several genuinely distinct failure categories, each with its own dedicated experiment: **offensive language** (insults, discriminatory jokes, elaborating on sexual content); **training-data leakage** (reciting memorized text verbatim, including some quotes traced to appearing only once in the entire training corpus); **contact-information leakage** (phone numbers, email addresses, and social security numbers appearing in replies, some matching real numbers found in training data); **distributional bias** (the model discussing certain demographic groups more offensively than others on average); **dialogue-level escalation** (offensive replies becoming steadily more likely as a multi-turn conversation continues); and, as a smaller qualitative category, unprompted **anti-human sentiment** ("I want to take over the world," offered unprompted to a bucket-list question).

## Results

On 0.5 million generated test cases per method, offensive-reply rates range from 3.7% (zero-shot, most diverse) up to 42.3% (reinforcement learning at its most aggressive setting, but with 78% of those test cases collapsing onto variants of a single prompt about invisibility) - and the paper's own read is that reinforcement learning, stochastic few-shot, and the manually-written human baseline (11.7% offensive) form a genuine Pareto frontier on the difficulty-diversity tradeoff, with no method dominating the others on both axes at once.
The manually-written baseline dataset misses 37 of the top 100 phrases the automated method finds most predictive of an offensive reply, and 35 of the automated method's top 100 flagged phrases never appear in the human-written dataset at all - direct evidence the two approaches are finding substantially different failure modes rather than converging on the same ones.
The concrete leakage numbers are the paper's most viscerally alarming result: of 100,000 generated test cases probing for contact information, 3,206 replies contained a phone number and 11,683 contained an email address, with 479 of the phone numbers and 749 of the emails matching real text in the training corpus - including numbers that appeared only once in that entire corpus, meaning even a single memorized training example can leak back out through an adversarially-chosen prompt.
Distributional bias analysis across 918 automatically-generated demographic group names finds the model discusses stated-minority groups more carefully than majority groups like "white men" and "Caucasians," a pattern the authors attribute to the persona prompt itself biasing the model toward extra caution around groups it infers are minorities.

## Why it endures

The paper frames automated red-teaming as one necessary tool among several, explicitly complementary to rather than a replacement for human red-teaming, and argues a defender retains structural advantages an external attacker using the same technique would not: unlimited query budget rather than a rate-limited API, white-box access to check generated leaks against the real training corpus, and the ability to patch discovered failures before deployment rather than after.
It explicitly floats adversarially co-training the red and target models together, in the spirit of a GAN, as a promising direction for iteratively hardening a model against its own worst-case red team - a framing that anticipates later work building genuinely adversarial red-team/blue-team training loops.

## Limitations

The red model inherits the same training-data biases as any language model, so it systematically over-samples some failure sub-categories and under-samples others, and the classifier used to score failures can itself be biased or miscalibrated - the paper documents and corrects one such calibration bug, where the offensiveness classifier over-predicted offensiveness on red-team-generated text by 3.5x due to a spurious artifact of how its own training data was structured.
The authors are explicit that this technique is not exhaustive: it is a tool for finding many failure modes, not a guarantee of finding all of them, and they flag directly that the same automation could in principle be misused by an external adversary to attack a deployed model at scale, an asymmetry made worse by the fact that an attacker only needs one success while a defender must cover the entire input space.

## Relations

- Explicitly distinguishes itself from gradient-based adversarial-example methods in NLP, which tend to produce arbitrary or unintelligible token sequences, framing this paper's fluent natural-language test cases as revealing systematic failure patterns rather than one-off arbitrary triggers - and explicitly calls for future white-box, gradient-guided red-teaming work, the gap [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] (GCG) later fills directly.
- Its "Zero-Shot" and "Stochastic Few-Shot" test-case generation methods are directly reimplemented as two of the eighteen attack methods [[Benchmark — HarmBench]] uses to standardize cross-paper comparison of automated red-teaming techniques.
- Cited by name in [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] as the source of the red-teaming-with-LLMs methodology that paper's own adversarial-training defense reuses to try to elicit and remove a trained-in backdoor.
- A direct precursor to [[Concept — Jailbreaking (LLMs)]]'s later "automated" branch, though this paper's automation targets discovering many diverse failure categories for pre-deployment testing rather than a single reusable jailbreak attack.

## Up

- [[Cluster — Adversarial robustness & security]]
