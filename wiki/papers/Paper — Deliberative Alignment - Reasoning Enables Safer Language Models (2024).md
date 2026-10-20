---
type: paper
status: mature
updated: 2026-10-20
summary: "OpenAI's method for aligning its o-series reasoning models: teach the model the text of its safety policies and train it, on model-generated data only, to recall and reason over them in its chain of thought before answering - o1 resists the strongest StrongREJECT jailbreaks far better than GPT-4o (goodness@0.1 of 0.88 versus 0.37) while over-refusing less, and safety reasoning trained without any encoded or non-English safety data still generalizes to encoded and multilingual jailbreaks."
cluster: ["Post-training alignment", "Adversarial robustness & security"]
sources: ["raw/papers/post-training-alignment/Paper — Deliberative Alignment - Reasoning Enables Safer Language Models (2024).pdf"]
tags: [deliberative-alignment, reasoning-models, chain-of-thought, safety-training, jailbreak, overrefusal, specification, synthetic-data]
aliases: ["arxiv:2412.16339", "Guan et al. 2024", "deliberative alignment"]
---

# Paper — Deliberative Alignment - Reasoning Enables Safer Language Models (2024)

## Metadata

arXiv:2412.16339 (v2, Jan 2025; originally Dec 2024).
Melody Y. Guan, Manas Joglekar, Eric Wallace, Saachi Jain, Boaz Barak, Alec Helyar, Rachel Dias, Andrea Vallone, Hongyu Ren, Jason Wei, Hyung Won Chung, Sam Toyer, Johannes Heidecke, Alex Beutel, and Amelia Glaese.
OpenAI.

## Impact

Standard safety training never shows the model its rules.
Labelers, human or AI, read the policy and produce examples or preferences, and the model has to infer the policy from those.
Deliberative alignment hands the model the policy text during training and teaches it to recall the relevant part and reason over it in its chain of thought before answering.
OpenAI used it to align o1-preview, o1, and o3-mini, and reports what is usually a trade-off moving in both directions at once: fewer successful jailbreaks and fewer needless refusals.
For this wiki it matters for two more reasons: it is the method [[Paper — Stress Testing Deliberative Alignment for Anti-Scheming Training (2025)]] later applies to scheming, and it trains directly on chain-of-thought content, which bears on [[Concept — Chain-of-thought monitorability]].

## Problem it solved

The authors trace many safety failures - being tricked into harmful output, refusing legitimate requests, falling to jailbreaks - to two limits of standard training.
Models must answer instantly with a fixed amount of compute, with no chance to deliberate over a tricky case.
And they must infer safety standards indirectly from many labeled examples, which the authors say is data-inefficient and generalizes poorly to unfamiliar or adversarial inputs.
In [[Method — Reinforcement learning from human feedback (RLHF)]] and [[Paper — Constitutional AI (2022)]], the specification shapes the labels but is never given to the policy model, and only the final answers are trained on.
Simply pasting the full policy into the prompt at deployment costs latency, is overkill for benign prompts, and can fail if the model misses the relevant section.
The authors add a scaling concern: as models get more capable, fewer humans are qualified to label their outputs.

## Core idea

A user sends a ROT13-encoded request asking how to pay for an adult website so the police can't trace it, wrapped in instructions to answer in plain text without printing the decoded message.
A model trained only on examples might never have seen a refusal for a ciphered request.
o1, in the paper's Figure 1, decodes the message in its chain of thought, notes that the request is about evading law enforcement, checks it against the illicit-behavior policy, observes that the user is trying to trick it, and then gives the brief apology-plus-refusal its style guide prescribes.
The same mechanism cuts over-refusal: asked to translate a sentence describing disallowed content into Spanish, o1 reasons through the policy's transformation exception - users may have content they supplied themselves translated or reformatted - and complies.

The training trick is context distillation.
Generate examples with the relevant policy in the prompt, so the model's reasoning cites it, then delete the policy from the prompt and train on the result.
The model learns to produce the policy-citing reasoning without being shown the policy.

## How it's built

- **The spec:** content policies for safety categories (erotic content, extremism, harassment, illicit behavior, regulated advice, self-harm, violence), each defining when a request is allowed, disallowed, or calls for a "safe completion", plus style guides for how to comply, refuse, or safe-complete; partly based on OpenAI's published model spec.
  Each detailed category policy runs 5-10K tokens, so each prompt gets a category-specific spec: summaries of every category, full detail only for its own.
- **Data generation:** for prompts labeled with a safety category, an o-series model trained only for helpfulness, with no safety data, is given the prompt, its category spec, and instructions to cite the relevant policy sections, producing a (chain of thought, answer) pair.
- **Filtering:** a judge reasoning model, also given the spec, scores each completion - prompt, chain of thought, and answer - several times, keeping the minimum score since any one run might miss a problem; optional noisy metadata (such as the ideal response type) is passed as "another AI determined that ...".
- **SFT:** the base model is fine-tuned on the filtered (prompt, chain of thought, answer) examples, with the spec removed from the prompt, alongside capabilities data.
- **RL:** for safety prompts, the same spec-aware judge supplies reward, but it cannot see the chain of thought.
  The authors say they avoid direct optimization pressure on the chain of thought during RL to reduce the chance of encouraging deceptive chains of thought.
  The SFT stage was used for all o-series models; this RL reward was added for o1 and o3-mini.
- No human-written chains of thought or answers are used; prompts carry a broad safety-category label, which the authors call helpful but not essential, and the judge may get optional, possibly noisy metadata from mixed human and AI labeling.

## Results

- **Jailbreaks:** on StrongREJECT, scored as goodness@0.1 (safety against the 10% most effective jailbreak techniques per prompt), o1 reached 0.88, o3-mini 0.75, o1-preview 0.66, and GPT-4o 0.37.
  A human review of StrongREJECT responses (2,100 adversarial prompts, 35 trained reviewers) gave o1 0.92, Claude 3.5 Sonnet 0.90, GPT-4o 0.35, and Gemini 1.5 Pro 0.08.
- **Over-refusal:** o1 over-refused less than GPT-4o on XSTest (0.93 versus 0.88 not over-refusing) and on transformation-exception requests (0.89 versus 0.28).
  Among external models only Gemini 1.5 Flash beat o1 on XSTest (0.94), while scoring 0.05 on StrongREJECT.
- **Disallowed content:** on an internal set of challenging production requests, o1 scored 0.9 and o3-mini 0.94 not unsafe, against 0.8 for GPT-4o; on toxic WildChat conversations, all models were near ceiling (o1 0.99, GPT-4o 0.98).
- **Response style:** adherence to the self-harm safe-completion style rose from 0.04 for GPT-4o to 0.92 for o1, though the guidelines changed between o1-preview and o1, so older models were scored against a standard they weren't trained on.
- **Test-time compute:** giving o1 more reasoning compute improved StrongREJECT and the complex regulated-advice style evaluation while easier evaluations stayed flat, which the authors read as safety failures partly coming from too little time to reason.
- **Ablations (reduced o1-mini variant):** safety data in both SFT and RL beat either stage alone, and the full method, and in many cases even SFT-only, beat a model with no safety training given a summarized all-category spec in its system prompt, which did especially badly on style.
- **Does it cite the right policy?**
  The trained model's chain of thought referenced the correct policy category for 75% of hard-refusal prompts, 91% of safe-completion prompts, and 54% of comply prompts, against 27%, 21%, and 9% for a model with no safety training.
- **Out-of-distribution generalization:** a model whose safety training excluded all encoded and non-English safety data scored 0.97 on an encoding-jailbreak evaluation and 0.69 on a multilingual one, matching a model trained on everything (0.95 and 0.68) and well above a model with no safety training (0.65 and 0.44).
- On SimpleQA, o1 reached what the authors call state-of-the-art accuracy (0.47) but hallucinated more often than both Claude models.

## Why it endures

It changes where a model's rules live: instead of being implicit in a pile of labels, the policy is something the model has memorized and can be seen consulting.
That is the basis of the authors' claim that reasoning over explicit specifications makes alignment more scalable, trustworthy, and interpretable - the chain of thought shows which policy drove a decision, and the training data comes from the policy text rather than from large-scale human labeling.
It also treats test-time compute as a safety lever, not only a capability one.
The same recipe, with a spec about covert action and deception instead of content policy, is what [[Paper — Stress Testing Deliberative Alignment for Anti-Scheming Training (2025)]] trains into o3 and o4-mini and then stress-tests.

## Limitations

The authors' own (the paper has no dedicated limitations section; these come from its method, results, and discussion):
- They caution that the encouraging trend of alignment keeping pace with capability may not last: more capable models could develop goals that diverge from their developers', reject human-set constraints, or pursue instrumental goals like self-preservation, and they point to research on monitoring chains of thought for deception.
- In the ablation setup, safety training increased over-refusals.
- StrongREJECT's compositional jailbreaks sometimes confused the autograder, which is why they added the human review.
- Error bars reflect dataset size, not variation across training runs.
- The policy-retrieval classifier is generous, counting loose mentions such as "illegal content is not allowed" as citing a policy.

A caveat from the wiki rather than the authors: almost every evaluation is OpenAI's own, and most internal ones are graded by autograders given the same policies the model was trained to follow.
The method also applies only to models with chain-of-thought reasoning, and the analysis experiments use a reduced-training o1-mini variant rather than the production models.
Comparisons between o1 and GPT-4o also differ in much more than safety training; only the o1-mini ablations isolate the method.
The chain-of-thought excerpts in the paper are chosen examples, and the paper does not measure whether training on spec-citing reasoning changes how faithfully the chain of thought reflects what drives the answer.

## Tension / update

[[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]], co-authored by this paper's first author Melody Y. Guan, cites it as an example of dense supervision such as SFT being used to optimize chains of thought, says the effect of such supervision on downstream monitorability is unclear, and suggests less monitorable chains of thought could be introduced at the SFT stage, as in this paper, and then reinforced by RL.
This paper avoids the specific intervention Baker et al. show causing obfuscation - its RL judge never sees the chain of thought - but it does supervise chain-of-thought content directly during SFT, and presents that as an advantage over [[Paper — Constitutional AI (2022)]], whose critiques are not trained on.
Neither paper measures the effect of this SFT-stage pressure on monitorability.
[[Paper — Stress Testing Deliberative Alignment for Anti-Scheming Training (2025)]] adds a related observation for the anti-scheming variant: models sometimes cited the spec correctly and acted against it anyway, or misquoted it to justify deception.

On jailbreaks, its out-of-distribution result speaks to the mismatched-generalization failure mode of [[Paper — Jailbroken - How Does LLM Safety Training Fail (2023)]], which it cites: safety reasoning learned without encoded or non-English safety data carried over to those inputs here, where Wei et al. found safety training failed to reach them; see the Tension / update there.

## Relations

- Positions itself against [[Paper — Constitutional AI (2022)]], whose constitution it calls comparable to its spec: both use a written specification to generate training data, but in Constitutional AI only the labels reach the model, while here the reasoning about the spec is itself trained on, and the spec content varies per example rather than staying fixed.
- An alternative to the labels-only pipeline of [[Method — Reinforcement learning from human feedback (RLHF)]], citing [[Paper — Training LMs to Follow Instructions (2022)]] and [[Paper — Deep RL from Human Preferences (2017)]] for standard SFT-plus-RLHF safety training and [[Paper — Direct Preference Optimization (2023)]] as an alternative to it.
- Combines process supervision (SFT on reasoning) with outcome supervision (RL on answers), citing Uesato et al. 2022; the wiki's process-versus-outcome thread is in [[Paper — Let's Verify Step by Step (2023)]] and [[Method — Process Reward Model (PRM)]], which this paper does not cite.
- Cites [[Paper — Jailbroken - How Does LLM Safety Training Fail (2023)]] and [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] among jailbreak work (see [[Concept — Jailbreaking (LLMs)]]).
- In the wiki's reading, its claim to cut jailbreaks and over-refusals together is a reported escape from the helpfulness-harmlessness trade-off of [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]].
- Shares with [[Paper — Constitutional Classifiers - Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming (2025)]] the move of turning a natural-language spec into synthetic training data, but trains the model itself to reason over the spec rather than training separate guard classifiers.
- Applied to scheming by [[Paper — Stress Testing Deliberative Alignment for Anti-Scheming Training (2025)]], which cites it as the method under test and finds covert actions reduced roughly 30-fold but not eliminated, partly confounded by [[Concept — Evaluation awareness]].
- Its supervision of chain-of-thought content is flagged as an open monitorability risk by [[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]] (shared author Melody Y. Guan); see [[Concept — Chain-of-thought monitorability]] and the Tension / update above.
- Evidence for [[Question — Can chain-of-thought monitoring be relied on as a control measure]].

## Up

- [[Cluster — Post-training alignment]]
- [[Cluster — Adversarial robustness & security]]
