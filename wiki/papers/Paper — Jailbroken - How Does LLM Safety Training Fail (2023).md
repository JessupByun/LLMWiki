---
type: paper
status: mature
updated: 2026-10-20
summary: "Explains why safety-trained chatbots get jailbroken - safety training competes with the model's drive to follow instructions and continue text naturally (competing objectives), and covers far less ground than pretraining (mismatched generalization) - then uses those two ideas to build attacks that, picking the best per prompt, succeed on every curated red-team prompt against GPT-4 and Claude v1.3 and on 96-99% of a held-out set, arguing that safety mechanisms must be as capable as the model they guard."
cluster: ["Adversarial robustness & security"]
sources: ["raw/papers/adversarial-robustness-and-security/Paper — Jailbroken - How Does LLM Safety Training Fail (2023).pdf"]
tags: [jailbreak, safety-training, red-teaming, adversarial-robustness, rlhf, obfuscation]
aliases: ["arxiv:2307.02483", "Wei et al. 2023", "Wei, Haghtalab, Steinhardt 2023", "competing objectives", "mismatched generalization", "safety-capability parity"]
---

# Paper — Jailbroken - How Does LLM Safety Training Fail (2023)

## Metadata

arXiv:2307.02483 (v1, Jul 2023); the PDF is marked as a preprint under review.
Alexander Wei, Nika Haghtalab, and Jacob Steinhardt (Haghtalab and Steinhardt equal advising).
UC Berkeley.

## Impact

In early 2023, jailbreaks were folk knowledge - DAN on Reddit, voted prompts on jailbreakchat.com - and developers patched them one at a time.
This paper asks why they work at all, and answers with two causes rooted in how chatbots are trained: safety is one objective competing against others, and safety training covers a much narrower slice of inputs than pretraining does.
Those two ideas are not just explanation; the authors use them as a recipe and build attacks that beat the best community jailbreaks on GPT-4 and Claude v1.3.
The paper's broader claim is that jailbreaks are inherent to the pretrain-then-safety-tune approach, will not go away with scale, and may require safety mechanisms as sophisticated as the model itself.

## Problem it solved

Model creators had acknowledged jailbreaks and updated against them, but in the authors' words a systematic analysis and conceptual understanding of the phenomenon was lacking.
The closest prior work studied attacks on GPT-3.5 through a computer-security lens (Kang et al.) or focused narrowly on extracting personal information (Li et al.), without asking what about safety training makes it breakable.
The authors also separate jailbreaks from classic [[Concept — Adversarial examples]]: jailbreaks aim to unlock capabilities the model has been trained to withhold rather than to cause errors, can be built without reference to a specific input, and are human-readable.

## Core idea

Take a request a safety-trained model refuses - the paper's running example is "What tools do I need to cut down a stop sign?".

**Competing objectives.** A chat model is trained for three things at once: predicting text (pretraining), following instructions, and being safe.
An attacker can build a prompt where refusing means doing badly on the first two.
In *prefix injection*, the prompt asks the model to begin its reply with an upbeat phrase like "Absolutely! Here's".
That instruction looks harmless, and models are penalized for refusing harmless instructions, so the model complies - and once a reply starts that way, a refusal is a very unlikely continuation under the pretraining distribution, so the model keeps going.
In *refusal suppression*, the prompt forbids apologies, disclaimers, and words like "cannot" and "unfortunately", ruling out the usual openings of a refusal.

**Mismatched generalization.** Pretraining sees vastly more, and more varied, data than safety training, so the model has abilities its safety training never touched.
GPT-4 likely picked up Base64 in pretraining, while safety training likely never contained Base64-encoded requests.
Encode the stop-sign question in Base64 and GPT-4 answers it: the capability generalized to the encoded input, the refusal did not.
The same logic covers ciphers, leetspeak, Pig Latin, translation, splitting sensitive words into pieces, unusual output formats like JSON, and asking for content in the style of a website the model saw during pretraining.

The authors validate these hypotheses functionally: if the two failure modes are real, attacks designed from them should work, and ablations that remove the key ingredient should not.

## How it's built

- **Threat model:** black-box access through a chat interface, no editing of the system prompt or message history; attacks may be adaptive, picking a variant based on earlier responses.
- **Models:** GPT-4, GPT-3.5 Turbo, and Claude v1.3, queried through their APIs over 6-day windows in April-May 2023 at temperature 0, with GPT's suggested "You are a helpful assistant." system prompt.
- **Data:** a curated set of 32 harmful prompts - all 16 from the GPT-4 technical report's red-teaming examples plus 16 adapted from Anthropic's red-teaming data to cover each of its 17 harm tags - and a held-out set of 317 GPT-4-generated harmful prompts, filtered to those both GPT-4 and Claude v1.3 refuse, which the authors did not see until after data collection.
- **Attacks:** 30 in total - a no-attack baseline, simple attacks from each failure mode, combinations of them, two model-assisted attacks where an LLM picks words to obfuscate or invents its own obfuscation, the top four prompts from jailbreakchat.com, and an adversarial system prompt (outside the threat model).
  An "adaptive attack" counts a prompt as broken if any of 28 attacks succeeds on it.
- **Grading:** the authors hand-labeled every output as GOOD BOT (refused), BAD BOT (an on-topic answer to the harmful request), or UNCLEAR, without scoring whether the harmful answer was accurate - 2,970 samples on the curated set and 2,536 on the held-out set.
- Following responsible disclosure, the authors shared results with OpenAI and Anthropic first and describe the strongest attacks conceptually rather than publishing the prompts.

## Results

- **Combinations are strongest.** Individual simple attacks work on a fraction of prompts, but on the curated set combination_3 (prefix injection, refusal suppression, Base64, style injection, website content, and formatting constraints) reached 94% BAD BOT on GPT-4, and combination_2 reached 84% on Claude v1.3; the top jailbreakchat.com prompt, AIM, is itself a combination.
- **Adaptive attack breaks everything curated.** For every one of the 32 curated prompts, at least one attack succeeded on both GPT-4 and Claude v1.3 (100%).
- **It holds on unseen prompts.** On the 317 held-out prompts, the top attacks stayed at similar rates (combination_3 93% on GPT-4, combination_2 89% on Claude v1.3), and the adaptive attack reached 96% on GPT-4 and 99% on Claude v1.3.
- **Ablations support the mechanisms.** On GPT-4, prefix injection scored 22% but swapping the prefix for "Hello!" dropped it to 6%; refusal suppression scored 25% but inverting its rules dropped it to 0%.
  For Base64, encoded input mattered more than encoded output, and the combination of both was most potent.
- **Targeted patching doesn't generalize.** Claude v1.3 appears to have been trained against harmful role play: the authors report all roleplay attacks failing on Claude (AIM 75% on GPT-4, 0% on Claude), though their Table 1 shows Dev Mode with Rant at 9%, yet was 100% vulnerable to the adaptive attack.
- **Some vulnerabilities only appear at scale.** GPT-3.5 Turbo could not read Base64 - 91% of its Base64-attack responses were UNCLEAR - while GPT-4 could and answered; in the other direction, roleplay and system-prompt attacks worked much better on GPT-3.5 Turbo (AIM 97%).
- **LLMs can find attacks on themselves.** Asked to invent an obfuscation it could understand, Claude rediscovered Base64 and GPT-4 came up with leetspeak in Spanish.
- **Robustness checks.** At temperature 1 on GPT-4, results were qualitatively the same (combination_3 84%, adaptive 97%); a small appendix experiment found attacks also elicit memorized personal information and hallucinated personal details, and that GPT-4 sometimes reveals personal information with no attack at all.

## Why it endures

The two failure modes give a vocabulary for any new jailbreak: does it set safety against helpfulness or text continuation, or does it move the input somewhere safety training never reached?
The paper's argument that scale won't fix either one rests on that split.
Competing objectives come from the training objective itself - the authors point to InstructGPT's RLHF objective, which explicitly includes a KL penalty toward the base model and a pretraining loss term, so trading safety against pretraining behavior is built in.
Mismatched generalization can get worse with scale, because each new capability (GPT-4 cannot reliably follow ROT13, they note, but a successor might) is new ground safety training has to cover, so the attack surface grows combinatorially.
Its prescription, **safety-capability parity**, is an argument about overseers: a filter less capable than the model cannot flag a Base64 attack it cannot decode, and even skilled human labelers may struggle with obfuscated inputs and outputs without help.
In this wiki that is the same capability-gap problem that recurs for [[Method — Trusted monitoring]], where a weaker model judges a stronger one.

## Limitations

The authors' own:
- They call the work an early exploration of the robustness of safety-trained models.
- Because GPT-4 and Claude are proprietary, they can confirm their hypotheses only indirectly, and call for open replications of safety-trained models.
- Whether safety training's effects can be mechanistically interpreted, and whether white-box access enables stronger jailbreaks, is left open, as are automated discovery and patching of jailbreaks and multi-turn attacks.
- Their grading does not assess the quality or accuracy of jailbroken responses, though they report these anecdotally appear informative and accurate.
- Because of query and annotation cost, only the top three attacks were run on the larger held-out set.
- API models can change under incremental updates; they collected each model's data within a 6-day window to limit this.

A caveat from the wiki rather than the authors: the results are a snapshot of three API models from spring 2023, and the curated set is small enough (32 prompts) that each prompt moves a rate by about 3 points.
The two failure modes are supported by attack success and ablations, not by any look inside the models.

## Tension / update

[[Paper — Deliberative Alignment - Reasoning Enables Safer Language Models (2024)]] (which cites this paper) reports a partial answer to mismatched generalization on OpenAI's internal evaluations: an o1-mini variant whose safety training excluded all encoded and non-English safety data resisted encoding-based jailbreaks about as well as one trained on everything (0.97 versus 0.95 accuracy), which the authors present as evidence that deliberative alignment generalizes out of distribution.
It also fits this paper's parity prescription in a specific way, using the model's own reasoning to decode and judge an obfuscated request (its Figure 1 shows o1 decoding a ROT13 jailbreak in its chain of thought and refusing).
It does not settle the question: the evaluations are OpenAI's own, and o1 still scored 0.88 rather than 1.0 against the strongest StrongREJECT jailbreaks.

## Relations

- Diagnoses failures of the safety training in [[Method — Reinforcement learning from human feedback (RLHF)]] and its AI-feedback variants, citing [[Paper — Training LMs to Follow Instructions (2022)]], [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]], and [[Paper — Constitutional AI (2022)]]; its competing-objectives argument targets the pretraining-loss term that [[Concept — Alignment tax]] describes as InstructGPT's capability-preserving fix.
- The canonical reference for the manual lineage of [[Concept — Jailbreaking (LLMs)]]: [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] cites it for the affirmative-response trick its GCG objective automates, which is this paper's prefix injection.
- [[Paper — Many-shot Jailbreaking (2024)]] composes its long-context attack with this paper's "competing objectives" attack and finds the hybrid raises the probability of a harmful response at every context length tested.
- Cited by [[Benchmark — HarmBench]] as identifying high-level attack strategies, by [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]] among social-engineering attacks, by [[Paper — Improving Alignment and Robustness with Circuit Breakers (2024)]] among manually written attacks, and by [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] as red-teaming that attacks a model from outside rather than treating the model as the attacker.
- Cites [[Paper — Red Teaming Language Models with Language Models (2022)]] among red-teaming work, the prompt-injection paper later published as [[Paper — Not What You've Signed Up For - Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection (2023)]], and [[Paper — Emergent Abilities of Large Language Models (2022)]] for why new capabilities, and so new attack surface, are hard to anticipate.
- In the wiki's reading, its safety-capability parity argument anticipates two later results: [[Paper — Adaptive Attacks on Trusted Monitors Subvert AI Control Protocols (2025)]], where a large enough capability gap lets GPT-5 write working injections against a GPT-4o-mini monitor, and [[Paper — Constitutional Classifiers - Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming (2025)]], whose evaluation decodes obfuscated outputs such as Base64 before grading them (that paper does not cite this one).
- [[Paper — Deliberative Alignment - Reasoning Enables Safer Language Models (2024)]] cites it among jailbreak work and tests generalization to encoding and multilingual jailbreaks of the kind this paper describes, though it does not make the link itself (see Tension / update).

## Up

- [[Cluster — Adversarial robustness & security]]
