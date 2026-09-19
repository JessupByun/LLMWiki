---
type: paper
status: mature
updated: 2026-09-18
summary: "Adds a few worked-out intermediate reasoning steps to each few-shot prompt exemplar, which dramatically improves large language models' accuracy on arithmetic, commonsense, and symbolic reasoning tasks, but only once the model is large enough."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022).pdf"]
tags: [prompting, reasoning, in-context-learning]
aliases: ["arxiv:2201.11903", "Wei et al. 2022", "CoT", "Chain-of-Thought Prompting"]
---

# Paper — Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022)

## Metadata

arXiv:2201.11903.
Jason Wei, Xuezhi Wang, Dale Schuurmans, Maarten Bosma, Brian Ichter, Fei Xia, Ed H. Chi, Quoc V. Le, Denny Zhou.
Google Research, Brain Team.
NeurIPS 2022.

## Impact

Standard few-shot prompting, as popularized by [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3), gives a model exemplars formatted as direct question-to-answer pairs, which works poorly on tasks that require multi-step reasoning and barely improves with model scale.
This paper's entire method is to change what the exemplars look like: instead of question-then-answer, each few-shot exemplar becomes question-then-worked-reasoning-then-answer, with no change to the model, no fine-tuning, and no new training data.
On the GSM8K math word problem benchmark, prompting PaLM 540B this way more than doubles its accuracy (from 18% to 57%) and reaches a new state of the art that beats a GPT-3 model fine-tuned specifically for the task with an added verifier - all from eight hand-written examples in the prompt, with the underlying model completely unchanged.

## Core idea

Consider how a person actually solves a multi-step word problem: not by jumping straight to the number, but by narrating the intermediate steps out loud - "she started with 5, gave away 2, so she has 3 left."
Chain-of-thought prompting simply writes a handful of few-shot exemplars in that same narrated style, so the model's own generated output, when it encounters a new problem, imitates the pattern and produces its own step-by-step reasoning before stating a final answer.
The paper's central and somewhat surprising finding is that this behavior is an emergent capability of scale: chain-of-thought prompting actually hurts performance for models under roughly 10 billion parameters (which tend to produce fluent but logically incoherent reasoning chains), and only starts reliably helping once a model reaches roughly 100 billion parameters, at which point the gains become large and consistent across arithmetic, commonsense, and symbolic reasoning benchmarks.
Careful ablations rule out the two most obvious alternative explanations: prompting the model to output only the final equation (no natural-language reasoning) helps far less on hard multi-step problems, and prompting it to spend equivalent extra tokens on a meaningless placeholder (a string of dots proportional to the reasoning length) barely helps at all - the benefit specifically comes from expressing the intermediate reasoning in natural language.

## Why it endures

Chain-of-thought reframed what "prompting" could mean: rather than only augmenting a model's input (as instruction-following and few-shot exemplars do), it showed that augmenting the model's own output trajectory, letting it generate intermediate computation before committing to an answer, unlocks capabilities that a flat input-to-output mapping cannot reach at any scale.
That principle, spend more inference-time computation on harder problems by generating intermediate steps, became the conceptual seed for the entire reasoning-model line of work that followed, including reasoning traces trained directly into a model rather than only elicited by a prompt.

## Limitations

The paper is explicit that a chain of thought which arrives at the correct final answer is not proof the model actually "reasoned" to get there: for free-response math problems this is unlikely to happen by chance, but for multiple-choice or binary tasks (most of the commonsense benchmarks here), the model can reach a correct answer through an incoherent or factually wrong reasoning path.
Manual error analysis on incorrect chains of thought found roughly half were fixable with one missing step or a semantic misunderstanding, while the model's arithmetic mistakes could often be caught and corrected by simply routing its equations through an external calculator.
There is also no factuality guarantee on the reasoning text itself, and the model still frequently produced fluent, coherent-sounding chains of reasoning that were nonetheless logically wrong.

## Relations

- Directly extends the few-shot prompting paradigm from [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3) by augmenting the output side of the prompt rather than the input side.
- Emergence tied to model scale, the same phenomenon [[Paper — Scaling Laws for Neural Language Models (2020)]] and [[Paper — Training Compute-Optimal Large Language Models (2022)]] (Chinchilla) study for other capabilities.
- Used as a feedback-quality technique (prompting a model to "think step-by-step" before answering) in [[Paper — Constitutional AI (2022)]]'s RL-CAI pipeline.
- See [[Method — Chain-of-Thought Prompting]] for the technique as reused across later reasoning-focused systems.

## Up

[[Cluster — Foundational canon]]
