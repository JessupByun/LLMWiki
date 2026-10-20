---
type: paper
status: mature
updated: 2026-10-19
summary: "Defines an emergent ability as one absent in smaller language models but present in larger ones - near-random performance until a threshold scale, then a sharp jump - and surveys dozens of examples across GPT-3, LaMDA, Gopher, Chinchilla, and PaLM, from multi-digit arithmetic and MMLU to chain-of-thought prompting, arguing such abilities cannot be predicted by extrapolating smaller models."
cluster: ["Capability evals", "Foundational canon"]
sources: ["raw/papers/capability-evals/Paper — Emergent Abilities of Large Language Models (2022).pdf"]
tags: [scaling, emergence, capabilities, forecasting, language-models]
aliases: ["arxiv:2206.07682", "Wei et al. 2022", "emergent abilities"]
---

# Paper — Emergent Abilities of Large Language Models (2022)

## Metadata

arXiv:2206.07682 (v2, Oct 2022; originally Jun 2022); Transactions on Machine Learning Research, Aug 2022.
Jason Wei, Yi Tay, Rishi Bommasani, Colin Raffel, Barret Zoph, Sebastian Borgeaud, Dani Yogatama, Maarten Bosma, Denny Zhou, Donald Metzler, Ed H. Chi, Tatsunori Hashimoto, Oriol Vinyals, Percy Liang, Jeff Dean, and William Fedus.
Google Research, Stanford, UNC Chapel Hill, and DeepMind.

## Impact

[[Paper — Scaling Laws for Neural Language Models (2020)]] and [[Paper — Training Compute-Optimal Large Language Models (2022)]] made a reassuring promise: loss falls smoothly and predictably as models grow.
This paper names the uncomfortable exception - specific *abilities* that appear suddenly, absent in every smaller model and then present past some scale, so they can't be forecast from the smaller models.
"Emergent abilities" became a widely adopted framing, and for safety it carried a sharp implication: if useful abilities can appear without warning, so might dangerous ones.
That worry runs through the case for dangerous-capability evaluation in [[Paper — Model Evaluation for Extreme Risks (2023)]], and it is the claim [[Paper — Are Emergent Abilities of Large Language Models a Mirage (2023)]] challenges directly.

## Problem it solved

Scaling laws described aggregate loss, but downstream task performance was known to behave differently on some tasks - Ganguli et al. had already noted that specific capabilities can appear abruptly.
There was no shared definition or systematic catalog of the phenomenon, so it was hard to say how common it was or what it implied about further scaling.

## Core idea

Adapting Steinhardt's definition, rooted in physicist Philip Anderson's "More Is Different," emergence is when quantitative changes produce qualitative changes in behavior.
Applied to language models: an ability is **emergent** if it is not present in smaller models but is present in larger models.
On a plot of performance against training compute, the signature is a hockey stick lying on its side - flat at random chance across several orders of magnitude, then a sharp rise once a critical scale is reached.
Take three-digit addition: GPT-3 models score near zero until about 13 billion parameters, then jump well above chance.
A forecaster who had only seen the smaller models would have predicted that the next model would also score near zero.

The paper extends the idea from tasks to techniques: a prompting or fine-tuning method is emergent if it doesn't help, or even hurts, until the model is large enough.

## How it's built

A survey and re-plotting of published results rather than new training runs, using training FLOPs as the main measure of scale (with parameter-count versions in the appendix).
**Emergent few-shot tasks:** BIG-Bench arithmetic, IPA transliteration, word unscrambling, and Persian question-answering; TruthfulQA; grounded conceptual mappings; MMLU; and Word-in-Context.
**Emergent techniques:** chain-of-thought prompting, instruction tuning, scratchpads for multi-step computation, and a True/False self-evaluation method for calibration.
Appendices catalog emergent BIG-Bench tasks and measure cross-entropy loss on six of them.

## Results

- MMLU performance stays near chance for GPT-3, Gopher, and Chinchilla models up to about 10^22 training FLOPs (around 10B parameters) and rises well above it at 70B-280B parameters.
- Word-in-Context stays at chance even for GPT-3 175B and Chinchilla, which GPT-3's authors had tentatively blamed on its architecture or autoregressive objective - but PaLM at 540B parameters finally rose above chance with no architectural change.
- Chain-of-thought prompting only beats standard prompting at around 100B parameters; instruction tuning hurts models of 8B parameters or fewer and helps only near 100B.
- Cross-entropy loss on six emergent BIG-Bench tasks improves even at small scales where accuracy, exact match, or BLEU sit flat - so the downstream metric can hide steady underlying progress.
- Emergence scale is not fixed: PaLM 62B is above chance on 14 BIG-Bench tasks where the larger LaMDA 137B and GPT-3 175B are not, pointing to data and architecture as well as size.

## Why it endures

It gave the field its vocabulary for capabilities that scaling laws don't predict, and it turned "what will the next model be able to do?" into an open research question.
For safety, it is the canonical citation for the possibility of unforecastable dangerous capabilities - a key reason extrapolation from smaller models is treated as insufficient on its own when evaluating dangerous capabilities.
Its own caveats about metrics also seeded the most important rebuttal: whether emergence lives in the models or in how we score them.

## Limitations

The authors' own:
- There are few compelling explanations for why abilities emerge.
- Evaluation metrics matter: exact-match scoring on long outputs, or scoring only a final answer, may disguise steady incremental improvement as emergence - though they argue this is at best an incomplete explanation, since emergence also appears on classification tasks.
- The loss analysis shows hidden progress but neither explains why downstream metrics jump nor predicts at what scale.
- The threshold scale is not a fixed property of an ability: better data, architectures, or training can lower it, and today's models are likely not trained optimally.
- Training-data size is not plotted, because many model families train every size on the same amount of data, and no single measure captures all aspects of scale.

## Emergent risks

The paper has its own short section on risks: just as abilities can emerge, so can harms, and some risks - such as GPT-3's tendency to mimic human falsehoods on TruthfulQA, and larger models' greater memorization of training data - grow with scale.
It lists possible future emergent risks, including backdoor vulnerabilities, inadvertent deception, and harmful content synthesis, and points to data filtering, forecasting, governance, and automated discovery of harmful behaviors as responses.

## Tension / update

[[Paper — Are Emergent Abilities of Large Language Models a Mirage (2023)]] argues that most claimed emergent abilities, including several surveyed here, are artifacts of nonlinear or discontinuous metrics plus too little test data, and disappear under continuous metrics or larger test sets.
This paper anticipated part of that argument in its own discussion of metrics and loss, but held that metric choice could not explain emergence on classification tasks; the Mirage paper's finding that multiple-choice grade, a discontinuous metric, is the most common metric under which BIG-Bench emergence appears, and that Brier-score rescoring removes LaMDA's emergence on such tasks, bears directly on that case, though this paper's own classification examples were not rescored.

## Relations

- Builds on, and qualifies, the smooth loss curves of [[Paper — Scaling Laws for Neural Language Models (2020)]] and [[Paper — Training Compute-Optimal Large Language Models (2022)]].
- Many of its examples come from GPT-3's few-shot prompting in [[Paper — Language Models are Few-Shot Learners (2020)]]; see [[Concept — In-context learning]].
- Cites [[Paper — Extracting Training Data from Large Language Models (2020)]] as evidence that memorization risk grows with scale.
- Notes that [[Paper — Training LMs to Follow Instructions (2022)]] made a 1.3B model preferred over much larger ones, an example of a technique lowering the scale an ability needs.
- Its worry about unpredictable capabilities motivates [[Method — Dangerous capability evaluation]] as developed in [[Paper — Model Evaluation for Extreme Risks (2023)]].

## Up

- [[Cluster — Capability evals]]
- [[Cluster — Foundational canon]]
