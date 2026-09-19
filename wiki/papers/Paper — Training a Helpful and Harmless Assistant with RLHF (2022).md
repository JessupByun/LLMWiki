---
type: paper
status: in-progress
updated: 2026-09-19
summary: "Applies preference modeling and RLHF to train a helpful and harmless dialogue assistant, releases the HH-RLHF preference dataset, and shows that at sufficient scale, alignment training carries essentially no capability cost - the opposite of the 'alignment tax' seen in smaller models."
cluster: ["Post-training alignment", "Foundational canon"]
sources: ["raw/papers/post-training-alignment/Paper — Training a Helpful and Harmless Assistant with RLHF (2022).pdf"]
tags: [rlhf, alignment, helpfulness, harmlessness, red-teaming, preference-modeling]
aliases: ["arxiv:2204.05862", "Bai et al. 2022", "HH-RLHF"]
---

# Paper — Training a Helpful and Harmless Assistant with RLHF (2022)

## Metadata

- **arXiv:** 2204.05862 (v1, 2022-04-12)
- **Authors:** Yuntao Bai, Andy Jones, Kamal Ndousse, Amanda Askell, Anna Chen, Nova DasSarma, Dawn Drain, Stanislav Fort, Deep Ganguli, Tom Henighan, Nicholas Joseph, Saurav Kadavath, Jackson Kernion, Tom Conerly, Sheer El-Showk, Nelson Elhage, Zac Hatfield-Dodds, Danny Hernandez, Tristan Hume, Scott Johnston, Shauna Kravec, Liane Lovitt, Neel Nanda, Catherine Olsson, Dario Amodei, Tom Brown, Jack Clark, Sam McCandlish, Chris Olah, Ben Mann, Jared Kaplan (Anthropic).

## Impact

This is Anthropic's foundational RLHF paper: the direct precursor to Claude, and the origin of the widely-used **HH-RLHF** preference dataset (publicly released on GitHub).
Its central empirical claim cuts against a natural intuition - that safety costs capability - by showing that for models above roughly 10B parameters, RLHF for helpfulness and harmlessness actually *improves* performance on standard NLP evaluations rather than degrading it, a result the authors call an "alignment bonus" as opposed to the "alignment tax" seen at smaller scale.
It is also the first paper in this wiki to treat helpfulness and harmlessness as **explicitly separate, partially anti-correlated objectives** rather than folding both into a single preference signal, and to study **red-teaming as a structured data-collection process** rather than an afterthought.

## Problem it solved

By early 2022, RLHF (via [[Paper — Deep RL from Human Preferences (2017)]] and applied at LLM scale in [[Paper — Learning to Summarize from Human Feedback (2020)]] and [[Paper — Training LMs to Follow Instructions (2022)]]) was proven to work for narrow tasks like summarization and single-turn instruction-following.
Open questions going into this paper: does RLHF work for open-ended, multi-turn dialogue; can a single training process produce a model that is simultaneously helpful *and* actively resistant to adversarial misuse; and does alignment training trade off against raw capability, or can the two be pursued jointly at scale?

## Core idea

Collect two **separate** human preference datasets rather than one: a **helpfulness** dataset (crowdworkers have open-ended conversations soliciting assistance, and pick the more helpful response) and a **harmlessness** (red-teaming) dataset (crowdworkers deliberately try to provoke the model into harmful, toxic, or dangerous responses, and pick the *more harmful* response, i.e. adversarially probing rather than demonstrating good behavior).
Train preference models (PMs) on a mixture of both, then run standard RLHF ([[Method — Proximal Policy Optimization (PPO)]] against the PM score, following the [[Paper — Learning to Summarize from Human Feedback (2020)]] recipe) to produce the final assistant.

A second core contribution is **iterated online RLHF**: rather than training once on a static dataset, retrain fresh preference models and RLHF policies on a roughly weekly cadence, redeploy the updated policy to crowdworkers to collect new comparisons on the model's *current* upper tail of behavior, and repeat.
This directly operationalizes the "keep labeling as the policy changes" mitigation for reward hacking that [[Paper — Deep RL from Human Preferences (2017)]] first proposed, at LLM scale.

## How it's built

- **Models:** a family of Transformer decoders from 13M to 52B parameters, following the specifications of Anthropic's earlier "general language assistant" paper (Askell et al. 2021).
- **Three tranches of data:** a base dataset collected from a context-distilled model, a rejection-sampling dataset, and an iterated-online dataset collected from RLHF-finetuned models redeployed over roughly five weeks.
- **RLHF objective:** identical structure to [[Paper — Learning to Summarize from Human Feedback (2020)]] - PPO against the PM score with a KL penalty against the initial policy - but with a much smaller KL coefficient (`0.001` vs `Stiennon` et al.'s larger values), since the authors find RLHF training in this setting is not particularly KL-penalty-sensitive.
- **Specialized-skill compatibility experiments:** mixing HH preference data with [[Paper — Learning to Summarize from Human Feedback (2020)]]'s summarization data, and running natural-language RLHF on top of a separately code-finetuned model, both to test whether alignment training degrades a specialized skill.
- **Out-of-distribution (OOD) detection:** a "Simplified Relative Mahalanobis distance" classifier over hidden activations, used to flag likely-harmful prompts *without any harmlessness training data at all*, as an alternative, less-supervised route to rejecting bad requests.

## Results

- **Alignment bonus at scale, alignment tax below it:** 13B and 52B RLHF models perform *better* than their raw pretrained counterparts on zero-shot NLP evaluations (MMLU, Lambada, HellaSwag, OpenBookQA, ARC, TriviaQA); smaller models see a capability decline from the same training.
- **Helpfulness and harmlessness are genuinely anti-correlated at the preference-model level:** a PM trained purely on one objective performs *significantly worse than chance* on the other; the tension shrinks, but does not disappear, as model size grows.
- **Specialized skills survive alignment training intact:** mixing HH preference data with summarization data produces no measurable drop in either HH or summarization PM accuracy; RLHF on a code-finetuned model *improves* its HumanEval pass rate rather than hurting it.
- **A tension between helpfulness and harmlessness inside RLHF training itself:** an earlier stage of the project produced models that gave the same exaggerated, unhelpfully-avoidant refusal to almost any sensitive question, because the red-teaming data collection procedure only ever showed the model examples of what *not* to do, never a genuinely good "hostage negotiator" response - a concrete instance of the objective-mismatch problem the paper calls out directly.
- **OOD detection catches most harmful requests with zero harmfulness training data:** exposing the linear OOD classifier to just 10 examples of harmful prompts lifts its AUROC (helpful vs. harmful) to `0.94`, comparable to or better than the best full-harmlessness-supervised preference model.
- Crowdworkers preferred the online HH model to professional human writers on curated HHH dialogues about 56% of the time.

## Why it endures

The HH-RLHF dataset itself became one of the most widely reused open preference datasets in post-training alignment research, cited far beyond this paper.
The "helpfulness vs. harmlessness as separate, competing objectives" framing, and the specific failure mode of a red-teaming-only dataset producing an unhelpfully avoidant model, both recur throughout later work on refusal behavior and over-refusal in aligned assistants.
The alignment-bonus-at-scale result is one of the cleanest empirical rebuttals in this wiki's corpus to the a priori assumption that safety training must cost capability.

## Limitations

- The paper explicitly documents its own red-teaming data collection flaw: because harmlessness comparisons only ever move a conversation toward *more* harmful responses, the resulting reward model never learns what a genuinely good response to a harmful request looks like, only that refusal beats compliance.
- Labeler-Anthropic agreement (~63%) was notably lower than comparable work such as [[Paper — Learning to Summarize from Human Feedback (2020)]] and [[Paper — Training LMs to Follow Instructions (2022)]], which the authors attribute partly to the more open-ended nature of the dialogue task.
- Gender-bias measurements show RLHF models are *more* biased by one metric than plain language models, though the paper attributes much of this to RLHF's lower-entropy output distribution rather than necessarily to a deeper increase in bias; the authors are careful to flag this as unresolved rather than explain it away.
- The paper's own qualitative examples (e.g. subtle factual inaccuracies a reader would need reference material to catch) directly illustrate the same "errors too subtle for human feedback to penalize" limitation discussed on [[Method — Reinforcement learning from human feedback (RLHF)]].

## Relations

- Runs the [[Method — Reinforcement learning from human feedback (RLHF)]] loop at dialogue scale, following the exact PPO-plus-PM-score recipe from [[Paper — Learning to Summarize from Human Feedback (2020)]].
- Directly compares its design choices against [[Paper — Training LMs to Follow Instructions (2022)]] (InstructGPT) in its own related-work section: this paper trains purely through RL with no supervised fine-tuning stage, and additionally trains for harmlessness, which InstructGPT does not.
- Cites [[Paper — Concrete Problems in AI Safety (2016)]] directly for general AI safety framing, and frames its own RL robustness failures as an instance of [[Concept — Reward hacking]], citing Pan et al. 2022 (queued, "The Effects of Reward Misspecification") as the closest prior empirical study.
- Mixes in [[Paper — Learning to Summarize from Human Feedback (2020)]]'s dataset directly as a specialized-skill compatibility test.

## Up

- [[Cluster — Post-training alignment]]
- [[Cluster — Foundational canon]]
