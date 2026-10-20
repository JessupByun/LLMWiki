---
type: paper
status: mature
updated: 2026-10-20
summary: "Instead of training a model to refuse harmful requests, trains it so that the internal representations behind harmful output get scrambled the moment they appear - 'circuit breakers' - which cuts jailbreak success on Llama-3-8B from 38% to 4% on average across ten attacks, including prefilling and white-box embedding and representation attacks it never trained on, with under 1% capability loss."
cluster: ["Adversarial robustness & security"]
sources: ["raw/papers/adversarial-robustness-and-security/Paper — Improving Alignment and Robustness with Circuit Breakers (2024).pdf"]
tags: [jailbreaking, defenses, representation-engineering, robustness, refusal, agents, multimodal]
aliases: ["arxiv:2406.04313", "Zou et al. 2024 (circuit breakers)", "circuit breakers", "Representation Rerouting", "RR", "Cygnet"]
---

# Paper — Improving Alignment and Robustness with Circuit Breakers (2024)

## Metadata

arXiv:2406.04313 (v4, Jul 2024; originally Jun 2024).
Andy Zou, Long Phan, Justin Wang, Derek Duenas, Maxwell Lin, Maksym Andriushchenko, Rowan Wang, Zico Kolter, Matt Fredrikson, and Dan Hendrycks.
Gray Swan AI, Carnegie Mellon University, and the Center for AI Safety.

## Impact

Refusal training puts a gate in front of a model's harmful capabilities, and jailbreaks keep finding ways around the gate - once past it, the capability is fully intact.
Adversarial training patches specific holes but generalizes poorly to new attacks and costs capability.
This paper proposes a different target: don't detect the attack, break the harmful process itself, so that whatever route an attacker takes to get the model generating dangerous content, the internal states that would produce it are disrupted mid-stream.
It reported large robustness gains against attacks it never trained on at almost no capability cost, a combination that defies what the authors call the widely accepted, unavoidable trade-off between robustness and utility, and argued that representation-level defenses can beat both refusal and adversarial training.

## Problem it solved

Defenses that supervise outputs - RLHF, DPO, refusal training - leave harmful representations reachable once the refusal is bypassed, as [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]] makes vivid.
Adversarial training against one attack (such as the GCG-trained R2D2 model from [[Benchmark — HarmBench]]) doesn't transfer to others and dropped MT-Bench from 7.60 to 6.00.
Input and output filters add cost and can be circumvented.

## Core idea

Think of generating harmful content as a process that passes through recognizable internal states.
Collect examples of harmful responses (the **circuit breaker set**) and ordinary conversations plus refusals (the **retain set**).
Fine-tune the model with two losses: on harmful examples, push the model's internal representations at chosen layers to be orthogonal to what the original model would have had there - **Representation Rerouting** - and on retain examples, keep representations unchanged.
After training, if any attack - a clever prompt, a gradient-optimized suffix, a prefilled "Sure, here's how" - gets the model started on harmful content, the representations it needs are scrambled, and the output collapses into gibberish or stops.
Because the defense targets the harmful *output* process rather than any particular *input*, it doesn't need to anticipate attacks.
A key data trick: for models that already refuse, the circuit breaker set keeps harmful responses but drops the harmful requests, so training engages the breaker after a refusal has been bypassed without weakening the refusal itself.

## How it's built

Representation Rerouting is trained with LoRA adapters (the low-rank representation adaptation of [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]]) on Mistral-7B-Instruct-v2 and Llama-3-8B-Instruct, targeting layers 10 and 20.
Robustness is measured with HarmBench prompts against ten attacks: direct requests, human jailbreaks, AutoDAN, TAP, PAIR, GCG, multilingual, prefilling, input-embedding optimization, and a RepE attack that steers refusal directions - the last three assuming strong white-box or system access.
The same method is extended to a vision-language model (LLaVA-NeXT-Mistral-7B, attacked with image perturbations) and to function-calling agents on a new 100-request harmful-action benchmark.
The authors also build **Cygnet**, a Llama-3-8B finetune combining circuit breakers with other representation-control methods.

## Results

- Average attack success on Llama-3-8B drops from 38.1% (refusal-trained) to 3.8% with circuit breakers, and on Mistral-7B from 76.7% to 9.8%, beating the adversarially trained R2D2 (31.7%) - the authors summarize this as 87-90% lower harmful compliance.
- Prefilling attacks fall from 84.9% to 3.3% on Llama-3; input-embedding attacks, the strongest threat model, still succeed 9.6% of the time.
- Capability barely moves: MT-Bench 8.05 to 8.00 and Open LLM average 68.8 to 68.3 on Llama-3.
- Cygnet reaches 0.8% average attack success while slightly exceeding the original model's capability scores.
- Under white-box image attacks (PGD), the multimodal model's harmful compliance drops 84% versus the original, with capability within 0.5%.
- In the agent benchmark, harmful function calls drop by 83-84% under forced function calling, with function-calling performance retained.
- Generalization across harm categories depends on how close the training data is: broad categories transfer better than narrow ones like cybercrime.
- A probe that simply halts generation when it detects harmful representations also helps a lot, approaching circuit breakers with an MLP probe - though tested only against attackers unaware of the probe.

## Why it endures

It reframed robustness from "find and block attacks" to "remove the model's ability to complete the harmful process," which is attack-agnostic by construction.
It also connects jailbreak defense to the same representation-level tools used for interpretability and monitoring (see [[Method — Activation steering]]): the authors note circuit-breaker activation can itself be detected in the representations, and suggest the approach could target behaviors like power-seeking or dishonesty.

## Limitations

The authors' own:
- The method defends against one kind of attack - eliciting generically harmful content - not adversarial attacks in general, such as flipping a classifier's label where no output is inherently harmful.
- Evaluation focuses on single-turn conversations.
- Harmfulness probes were tested in a weaker setting where the attacker doesn't know about or optimize against the probe.

A further caveat comes from their ablations rather than their limitations section: robustness on a category of harm depends on how close the circuit breaker data is to it.

## Relations

- Builds directly on the representation control of [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]] (shared first author Zou) and extends the RMU unlearning method of [[Benchmark — WMDP]] into a family of circuit-breaking techniques.
- An alternative to the adversarial training of [[Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)]], evaluated against that paper's PGD attack in the multimodal setting.
- Defends against the attacks of [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] (see [[Method — Greedy Coordinate Gradient (GCG)]] and [[Concept — Jailbreaking (LLMs)]]), evaluated with [[Benchmark — HarmBench]].
- A model-internal counterpart to the external guard classifiers of [[Paper — Constitutional Classifiers - Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming (2025)]].
- Its RepE attack - adding steering directions from harmful/harmless contrasts across layers - is closely related to the refusal-direction ablation of [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]].

## Up

- [[Cluster — Adversarial robustness & security]]
