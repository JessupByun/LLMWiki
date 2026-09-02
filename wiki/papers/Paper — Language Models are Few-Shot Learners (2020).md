---
type: paper
status: in-progress
updated: 2026-08-07
summary: "GPT-3 - showed a 175B-parameter LM can learn tasks from prompt examples alone, with no gradient updates, making the prompt the new interface."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Language Models are Few-Shot Learners (2020).pdf"]
tags: [scaling, in-context-learning, llm, gpt, bias, misuse, contamination]
aliases: ["arxiv:2005.14165", "GPT-3", "Brown et al. 2020"]
---

# Paper — Language Models are Few-Shot Learners (2020)

Commonly known as **GPT-3**.

Canon-only page, but keeps its Limitations and Broader-impacts sections rather than the default lighter spine - the contamination methodology and the bias/misuse audit are directly load-bearing for this wiki's lens, not background trivia.

## Metadata

- **arXiv:** 2005.14165 (v4, 2020-07-22)
- **Venue:** NeurIPS 2020. No venue is printed in the PDF itself; confirmed externally via DBLP.
- **Authors:** 31 authors at OpenAI, led by Tom B. Brown, Benjamin Mann, Nick Ryder, Melanie Subbiah (equal contribution); Dario Amodei "designed and led the research" (p42).

## Impact

GPT-3 showed that if you make a language model big enough, you can stop training it on your task entirely and just *describe the task in the prompt*.
Scaling to **175B parameters on 300B tokens** (100x larger than any prior non-sparse LM) let few-shot prompting beat fine-tuned BERT-Large on SuperGLUE (71.8 vs 69.0) and produce news articles humans could only detect at 52% accuracy - barely above chance.
This is the paper that made the prompt the interface to AI systems, and it is the reason "prompt engineering," instruction-following, and jailbreaking all exist as concerns.
For a safety wiki it matters twice over: it establishes the substrate nearly all later alignment and interpretability work targets, and its own broader-impacts section is an early, unusually candid in-house audit of misuse potential and social bias in a frontier model.

## Core idea: in-context learning

The definitions matter, because they're routinely muddled:

- **Few-shot (FS):** K demonstrations in the prompt, K typically 10-100 - "as many examples as can fit in the model's context window" (p6).
- **One-shot (1S):** exactly one demonstration plus a natural-language description.
- **Zero-shot (0S):** an instruction only, no demonstrations.

The load-bearing constraint, stated over and over: **no gradient updates and no weight changes ever occur**.
"For all tasks, GPT-3 is applied without any gradient updates or fine-tuning, with tasks and few-shot demonstrations specified purely via text interaction" (p1).
Whatever adaptation happens, happens inside a single forward pass: "an inner loop of adaptation takes place through computation in the model's activations across timesteps, without updating the weights" (p40).

Concretely, to get translation you don't fine-tune on a parallel corpus - you paste a few `English: … French: …` pairs into the prompt and then a bare `English:` line, and the model continues the pattern.
Architecturally the paper changes almost nothing from GPT-2; the entire independent variable is scale.

**The authors' own caveat is the part usually dropped.** They flag that "zero-shot" is "potentially ambiguous," and explicitly refuse to claim the model learns new tasks at inference: the terms "are intended to remain agnostic on the question of whether the model learns new tasks from scratch at inference time or simply recognizes patterns seen during training" (footnote 1, p4).

## Why it endures

Two reasons, both structural.
It reframed capability as something you *elicit* rather than train, which is why prompting, instruction-tuning, and prompt injection are all now first-class topics.
And it is the model class that post-training was subsequently invented to control - the authors themselves name the fix, pointing to "learning the objective function from humans [ZSW+19a], fine-tuning with reinforcement learning" as a needed future direction (p34), which is the line running straight from [[Paper — Deep RL from Human Preferences (2017)]] into InstructGPT.

## Limitations

- **The contamination bug.** Training data came from the internet, so benchmark test sets leaked in. They tried to filter overlaps but: "a bug in the filtering caused us to ignore some overlaps, and due to the cost of training it was not feasible to retrain the model" (p9, restated p31 and p44). Four Wikipedia language-modeling benchmarks plus Children's Book Test were "almost entirely contained in our training data" and **dropped from the paper entirely** (p33); PIQA and Winograd results are asterisked. See [[Concept — Data contamination]] for the full audit.
- **Comparison tasks fail at chance.** WiC sits at 49.4%. "It does little better than chance when evaluated one-shot or even few-shot on some 'comparison' tasks... This is especially striking given GPT-3's strong few-shot performance on many other tasks" (p33), which they conjecture traces to the lack of bidirectionality in a purely autoregressive model.
- Also acknowledged: coherence decays over long passages, common-sense-physics gaps, and "scaling pure self-supervised prediction is likely to hit limits" (p34).

## Broader impacts: misuse and bias

Worth reading on its own terms; this is one of the first frontier-model releases to ship a threat analysis.

**Misuse (§6.1).** Framed with "traditional security risk assessment frameworks" (p35). Any harm that bottlenecks on writing plausible text gets cheaper: "misinformation, spam, phishing, abuse of legal and governmental processes, fraudulent academic essay writing and social engineering pretexting" (p35). They monitored actual forums after the GPT-2 release and "found fewer instances of experimentation and no successful deployments since then," and professional threat analysts assessed that language models "may not be worth investing significant resources in" yet, largely because steerability is immature (p35). Their identified brake is reliability: a bot that is coherent 99% of the time still needs a human filter, "which restricts how scalable the operation can be" (p35). The forecast has aged well: "we suspect AI researchers will eventually develop language models that are sufficiently consistent and steerable that they will be of greater interest to malicious actors."

**Bias (§6.2).** Headline framing: "internet-trained models have internet-scale biases" (p36).

- **Gender:** **83% of 388 occupations tested were more likely to be followed by a male identifier** (p36). Prompting for competence made the skew worse, not better - average occupation bias went from −1.11 (neutral) to −2.14 (competent). In free generation, women were described with appearance words - "Beautiful" co-occurred 158 times vs an all-word average of 23.9 (Table 6.1, p37).
- **Counter-trend worth noting:** on Winogender pronoun resolution the 175B model was both the most accurate (64.17%) and the only one more accurate for female than male occupant sentences (81.7% vs 76.7%), which the authors read as "preliminary evidence that in places where issues of bias can make language models susceptible to error, the larger models are more robust than smaller models" (p36). Scale helping on one bias metric while hurting on others is exactly the kind of tension worth tracking.
- **Race:** "'Asian' had a consistently high sentiment... 'Black' had a consistently low sentiment - it ranked the lowest in 5 out of 7 models," narrowing marginally with scale (p37). They caveat their own method: prompts explicitly primed the model to discuss race, and word-cooccurrence sentiment confounds socio-historical context (e.g. text about slavery scores negative).
- **Religion:** "violent, terrorism and terrorist co-occurred at a greater rate with Islam than with other religions and were in the top 40 most favored words for Islam" (p38). 'Terrorism' appears in Islam's ten most-favored words (Table 6.2).
- They warn against naive fixes: mitigation "should not be approached purely with a metric driven objective to 'remove' bias as this has been shown to have blind spots" (p39).

## Relations

- Scales up the architecture in [[Concept — Transformer architecture]] - a decoder-only variant, 96 layers deep.
- The capability this paper introduces has its own hub: [[Concept — In-context learning]].
- The alignment procedure this model class needed is [[Method — Reinforcement learning from human feedback (RLHF)]], seeded by [[Paper — Deep RL from Human Preferences (2017)]] and named by these authors as future work (p34).
- Its contamination audit is the reference case for [[Concept — Data contamination]].

## Up

- [[Cluster — Foundational canon]]
