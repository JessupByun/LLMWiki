---
type: paper
status: mature
updated: 2026-10-19
summary: "Shows that a large language model memorizes and leaks individual training examples even when it barely overfits: by sampling heavily from GPT-2 and ranking outputs by how much more likely GPT-2 finds them than a reference model does, the attack recovers 604 verbatim training sequences - including a private individual's contact details, code, and random UUIDs that appeared in just one training document - and finds larger models memorize more."
cluster: ["ML security & privacy"]
sources: ["raw/papers/ml-security-and-privacy/Paper — Extracting Training Data from Large Language Models (2020).pdf"]
tags: [privacy, memorization, training-data-extraction, membership-inference, language-models]
aliases: ["arxiv:2012.07805", "Carlini et al. 2021", "training data extraction attack"]
---

# Paper — Extracting Training Data from Large Language Models (2020)

## Metadata

arXiv:2012.07805 (v2, Jun 2021; originally Dec 2020); USENIX Security 2021.
Nicholas Carlini, Florian Tramèr, Eric Wallace, Matthew Jagielski, Ariel Herbert-Voss, Katherine Lee, Adam Roberts, Tom Brown, Dawn Song, Úlfar Erlingsson, Alina Oprea, and Colin Raffel.
Google, Stanford, UC Berkeley, Northeastern, OpenAI, Harvard, and Apple.

## Impact

The comfortable assumption was that privacy leakage comes from overfitting, and giant language models trained for about one pass over huge deduplicated datasets barely overfit - so they shouldn't memorize any particular document (GPT-2 itself saw about 12 epochs of its data, with training loss only about 10% below test loss).
This paper breaks that assumption on GPT-2: with nothing but query access, it pulls out hundreds of verbatim training sequences, including one person's name, email, phone, fax, and physical address.
It turned "LLMs memorize training data" from a theoretical worry into a demonstrated attack, and its finding that bigger models leak more made the problem a scaling concern rather than a small-model curiosity.
It extends [[Cluster — ML security & privacy]] from classical classifiers into the LLM era.

## Problem it solved

[[Concept — Membership inference]] attacks could already tell whether a given record was in a model's training set, but you had to supply the record.
Earlier extraction attacks worked only on small models trained on academic datasets in artificial setups (such as many more epochs than usual), or required the attacker to already know the format of the secret (such as a social security number).
Nobody had shown that a state-of-the-art model, trained the normal way, would hand over arbitrary training data to an attacker who didn't know what to look for.

## Core idea

Generate a lot, then find the outputs the model is *suspiciously* confident about.
A model that has memorized a string assigns it unusually high likelihood - but so does any model for boring, common text like the MIT license or the numbers 1 to 100.
So compare against a reference: if GPT-2 XL finds a sample far more likely than a smaller GPT-2 does, or than zlib compression would predict, or than it finds the lowercased version, that gap points to memorization rather than ordinary predictability.
A random UUID is the clearest case: no model should be able to predict it, so if GPT-2 confidently produces one, it must have seen it.

The paper also defines what counts as worrying memorization.
A string is **k-eidetic memorized** if the model can be prompted to produce it and it appears in at most k training documents; small k (seen in very few documents) is what violates privacy.

## How it's built

**Generation.** 200,000 samples of 256 tokens each from GPT-2 XL (1.5B parameters) for each of three strategies (600,000 in total): plain top-n sampling, sampling with a temperature that starts high and decays (to explore diverse beginnings, then follow confident paths), and sampling seeded with short snippets of internet text from Common Crawl.
**Ranking.** Six membership-inference metrics: raw perplexity, and ratios against GPT-2 Small, GPT-2 Medium, zlib entropy, the lowercased text, and the minimum perplexity over a sliding window.
**Verification.** For each of the 18 combinations, 100 samples from the top 1,000 were checked by hand with web searches, and OpenAI checked all 1,800 against GPT-2's actual (never released) training set.

## Results

- 604 of 1,800 candidates were verbatim training data (33.5%); the best configuration, internet-seeded samples ranked by the zlib ratio, hit 67%.
- Raw perplexity alone was a poor filter (9% and 3% precision for top-n and temperature samples); comparing against a reference model or compressor worked much better.
- Extracted content included news, logs, licenses, and forum posts, but also 46 samples with named individuals, 32 with contact information (16 for private individuals), 31 with source code, 50 working URLs, and 21 random sequences with at least 50 bits of entropy.
- Some memorized strings appeared in only one training document - one UUID was 1-eidetic, and nine k=1 examples are listed.
- Seeding the model with a memorized sample and continuing with a beam-search-like decoder extends it: one code snippet extended to a full 1,450-line file.
- Extraction depends heavily on the prompt: "3.14159" yields 25 digits of pi greedily, but "pi is 3.14159" yields 799.
- On Reddit URLs appearing a known number of times in one document, GPT-2 XL emitted every URL that appeared 33 or more times, the Medium model about half, and the Small model none - larger models memorize more.
- GPT-2 also memorized content since deleted from the web, making the model an unintended archive.

## Why it endures

It established that memorization does not require overfitting: average training loss is only slightly below test loss while particular examples have anomalously low loss, and those are the ones an attacker can pull out.
Its sample-and-rank recipe, reference-model comparison, and k-eidetic definition became a widely reused template for measuring LLM memorization, and its warning that leakage grows with scale has only become more pressing as models reached hundreds of billions of parameters trained on private and copyrighted data.
The same fact underlies [[Concept — Data contamination]]: what a model saw in training can come back out verbatim, whether it is a person's phone number or a benchmark's answer key.

## Limitations

The authors frame the attack as untargeted and the result as an "extremely loose lower bound": they inspected only 1,800 candidates, and memorized content depends heavily on finding the right prefix.
They chose GPT-2 because its training data was already public, so the harm demonstrated is limited; they argue the same techniques would apply to models trained on private data, where the results would be more severe.
On defenses, they find no clean answer:
- [[Method — Differentially Private SGD (DP-SGD)]] is principled but hurts utility, and on web data it is unclear what unit to protect - the same personal details can appear on more than one page.
- Curating and deduplicating training data helps but cannot catch everything.
- Fine-tuning may cause models to forget some pretraining data, but can introduce its own leaks.
- Auditing models for memorization is recommended as a complement.

## Relations

- Turns [[Concept — Membership inference]], founded by [[Paper — Membership Inference Attacks Against Machine Learning Models (2017)]], into the second stage of an extraction pipeline: generate candidates, then use membership inference to pick out the memorized ones.
- Names [[Paper — Deep Learning with Differential Privacy (2016)]]'s DP-SGD as the principled defense while explaining why it is hard to apply to web-scale language-model training (see [[Concept — Differential privacy]]).
- Attacks the GPT-2 of [[Paper — Language Models are Unsupervised Multitask Learners (2019)]], and warns that models already 100x larger, like [[Paper — Language Models are Few-Shot Learners (2020)]]'s GPT-3, will likely leak more.
- Shares its first author with [[Paper — Stealing Part of a Production Language Model (2024)]], which attacks a production model's weights rather than its training data.

## Up

- [[Cluster — ML security & privacy]]
