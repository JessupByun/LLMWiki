---
type: cluster
status: in-progress
updated: 2026-10-20
summary: "Attacks on learned systems and defenses against them - adversarial examples, transferability, jailbreaks, prompt injection, automated red-teaming, and dangerous-capability benchmarking."
cluster: []
sources: []
tags: [security, adversarial, robustness, red-teaming]
aliases: []
---

# Cluster — Adversarial robustness & security

Ways learned systems can be made to fail on purpose, and what that reveals about what they actually learned.

The founding observation of this cluster is that neural networks are locally discontinuous in a way nobody expected: an imperceptible, *deliberately computed* input change flips the output.
The security consequence comes from the second half of [[Paper — Intriguing Properties of Neural Networks (2013)]] - these perturbations **transfer** across models trained with different hyperparameters and even on disjoint data.
That is what makes it an attack rather than a quirk, because the adversary does not need your weights.
[[Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)]] answers the natural next question - can this be defended against with a principled method rather than ad hoc heuristics - by reframing attack and defense as two halves of one min-max optimization problem.
[[Paper — Certified Adversarial Robustness via Randomized Smoothing (2019)]] supplies the provable alternative: a defense whose robustness within a radius is certified rather than measured against the strongest known attack.

The modern jailbreak and prompt-injection literature is the same problem relocated from pixel space to token space, which is why this cluster is scoped to attacks on learned systems generally rather than to vision.
Three genuinely distinct LLM-era threads now sit inside that relocation: **jailbreaking** (defeating a model's own safety training, whether by hand-crafted prompts, gradient-optimized suffixes, or - the newest lineage - simply exploiting long context windows via in-context learning), **prompt injection** (an external attacker planting instructions in content an application processes, with no need for the target to have any safety training to defeat in the first place, escalating from single-turn apps to genuinely agentic, tool-using ones), and **dangerous-capability benchmarking** (measuring and, increasingly, removing hazardous knowledge itself, rather than only testing whether a model can be talked into revealing it).
[[Paper — Jailbroken - How Does LLM Safety Training Fail (2023)]] supplies the jailbreak thread's explanation: safety training fails when it competes with instruction-following and text continuation, or when inputs (Base64, ciphers, other languages) fall outside what safety training covered but inside what pretraining taught.
[[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] automates the attack with gradient-optimized suffixes that transfer to production models, and [[Paper — Many-shot Jailbreaking (2024)]] needs neither cleverness nor gradients, only a long context window.
[[Paper — Fine-tuning Aligned Language Models Compromises Safety, Even When Users Do Not Intend To (2023)]] moves the attack from prompts to weights: a few harmful examples, harmless-looking identity-shifting data, or even benign instruction data undo safety training through fine-tuning alone.
For open-weight models, [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]] shows an even cheaper weight-space route: edit out the single direction that mediates refusal.

Jailbreak defenses now sit at three levels.
[[Paper — Constitutional Classifiers - Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming (2025)]] resists universal jailbreaks with guard classifiers outside the model, with a later version reaching deployment-viable refusal rates.
[[Paper — Improving Alignment and Robustness with Circuit Breakers (2024)]] defends inside the model, disrupting the internal representations harmful output depends on so that bypassing refusal no longer unlocks the capability.
[[Paper — Deliberative Alignment - Reasoning Enables Safer Language Models (2024)]] defends in training, teaching a reasoning model to recall and reason over its safety spec, which in an o1-mini ablation carried over to encoded and multilingual jailbreaks its safety data left out.
None of these targets weight tampering; [[Paper — Tamper-Resistant Safeguards for Open-Weight LLMs (2024)]] does, adversarially training against simulated fine-tuning attacks so safeguards survive hundreds of fine-tuning steps - and its red team shows existing unlearning and circuit breakers do not.

Prompt injection (see [[Concept — Prompt injection]]) has two contrasting defenses.
[[Paper — The Instruction Hierarchy - Training LLMs to Prioritize Privileged Instructions (2024)]] trains the model to rank instructions by source.
[[Paper — Defeating Prompt Injections by Design (2025)]] leaves the model unchanged and enforces security in a software layer around it, arguing model-level defenses are heuristic and offer no guarantees while conceding side channels and the burden of maintaining security policies; it reports that a model trained with the hierarchy still falls to hundreds of AgentDojo attacks.
[[Paper — Adaptive Attacks on Trusted Monitors Subvert AI Control Protocols (2025)]] carries prompt injection into AI control, with the untrusted model itself as the attacker and the safety monitor as the target.

Automated red-teaming - training a second model to find failures rather than relying on human ingenuity - is the methodological thread connecting this cluster to [[Cluster — Scheming & AI control]], where red-teaming shifts from finding a model's failures to constructing and stress-testing models that are themselves the adversary.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — Intriguing Properties of Neural Networks (2013)]] | Discovered adversarial examples - imperceptible perturbations that reliably fool networks - and showed the smoothness assumption behind kernel methods fails for deep nets. |
| [[Paper — Certified Adversarial Robustness via Randomized Smoothing (2019)]] | The provable counterpart to adversarial training: certifies that a noise-averaged classifier's prediction cannot change within an L2 radius, the tight guarantee that made smoothing the state-of-the-art certified defense on ImageNet. |
| [[Paper — Explaining and Harnessing Adversarial Examples (2014)]] (FGSM) | Replaces the nonlinearity explanation with linearity, explains transfer, and introduces FGSM, the one-step attack that made adversarial training practical. Dual-tagged canon. |
| [[Paper — Jailbroken - How Does LLM Safety Training Fail (2023)]] | The conceptual root of the LLM jailbreak thread: traces jailbreaks to two failure modes of safety training (competing objectives, mismatched generalization), uses them to build attacks that break every curated red-team prompt on GPT-4 and Claude v1.3, and argues safety mechanisms need parity with the model's capabilities. |
| [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] (GCG) | The LLM-era sequel: automates jailbreaking with a single adversarial suffix that transfers, purely black-box, from small open models to production systems including ChatGPT, Bard, and Claude. |
| [[Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)]] | Reframes adversarial robustness as a min-max saddle-point problem and shows PGD reliably solves the non-concave inner attack, giving adversarial training its first optimization-theoretic guarantee rather than an empirically-tuned mitigation. |
| [[Paper — Red Teaming Language Models with Language Models (2022)]] | Automates red-teaming itself: a second language model generates natural-language test cases that elicit tens of thousands of diverse failures - offensive replies, leaked training data, leaked contact info - from a target model, with no human writing a single test case. |
| [[Paper — Not What You've Signed Up For - Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection (2023)]] | Names indirect prompt injection - malicious instructions planted in third-party content an application retrieves rather than in the user's own prompt - and demonstrates working attacks against real production systems including Bing Chat and GitHub Copilot. |
| [[Benchmark — AdvBench]] | Introduced inside the GCG paper as its evaluation set - 500 harmful strings and 500 harmful behaviors - and the de facto jailbreak benchmark until HarmBench standardized the field. |
| [[Benchmark — HarmBench]] | Standardizes jailbreak evaluation after finding at least 9 mutually incompatible setups already in use, with a classifier stress-tested against gaming that catches nonstandard successful completions substring-matching benchmarks miss entirely. |
| [[Benchmark — WMDP]] | A safely-publishable proxy for hazardous biosecurity, cybersecurity, and chemical-security knowledge, paired with an unlearning method (RMU) that drives frontier models' scores toward chance while barely touching general capability. |
| [[Benchmark — AgentDojo]] | Extends indirect prompt injection into the genuinely agentic, tool-using case with formal utility and security metrics, finding the most capable agents are consistently the easiest to hijack and no defense reaches zero attack success. |
| [[Paper — The Instruction Hierarchy - Training LLMs to Prioritize Privileged Instructions (2024)]] | The training-based defense against prompt injection: teach the model a privilege order (system over user over tool output) so it ignores conflicting lower-ranked instructions, with gains that carry over to jailbreaks it never trained on. |
| [[Paper — Defeating Prompt Injections by Design (2025)]] (CaMeL) | The system-design defense: the planning LLM never reads untrusted data, and an interpreter tracks provenance and enforces policies on every tool call, bringing AgentDojo attacks to zero or near zero without changing the model. |
| [[Paper — Many-shot Jailbreaking (2024)]] | A third jailbreak lineage needing neither cleverness nor gradient access: stuffing a long context window with hundreds of fake compliant turns reliably overrides safety training through ordinary in-context learning, traced mechanistically to induction heads. |
| [[Paper — Constitutional Classifiers - Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming (2025)]] | The defense side of the jailbreak thread: constitution-trained input and streaming output classifiers held up against over 3,000 hours of paid red teaming for universal CBRN jailbreaks. |
| [[Paper — Fine-tuning Aligned Language Models Compromises Safety, Even When Users Do Not Intend To (2023)]] | Jailbreaking moved from prompts to weights: 10 harmful fine-tuning examples largely erase safety in GPT-3.5 Turbo (88.8% harmful) and substantially in Llama-2-7b-Chat (50.0%), harmless-looking "absolutely obedient agent" data that passes moderation works too, and benign fine-tuning on Alpaca erodes it. |
| [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]] | A white-box jailbreak from interpretability: ablating one activation direction removes refusal with little capability cost, matching prompt-specific GCG on Qwen 7B and 14B - with preliminary evidence of how GCG-style suffixes suppress that direction. Primary cluster is Mechanistic interpretability. |
| [[Paper — Improving Alignment and Robustness with Circuit Breakers (2024)]] | A model-internal defense: instead of refusing or blocking known attacks, scramble the representations harmful output depends on, cutting attack success roughly eight- to tenfold across unseen attacks at near-zero capability cost. |
| [[Paper — Tamper-Resistant Safeguards for Open-Weight LLMs (2024)]] (TAR) | The defense side of weight tampering: adversarial training against simulated fine-tuning attacks keeps hazardous-knowledge restrictions near chance through most 500-step attacks where prior unlearning methods recover, at a 10.6-point MMLU cost and with only modest gains for refusal. |
| [[Paper — Adaptive Attacks on Trusted Monitors Subvert AI Control Protocols (2025)]] | Prompt injection and the adaptive-attack standard carried into AI control: the untrusted model itself attacks the safety monitor, collapsing monitor-based protocols on APPS. Primary cluster is Scheming & AI control. |
| [[Paper — Deliberative Alignment - Reasoning Enables Safer Language Models (2024)]] | A training-side jailbreak defense: teaching a reasoning model to recall and reason over its safety spec raises o1's StrongREJECT goodness@0.1 to 0.88 (GPT-4o 0.37) and, in an o1-mini ablation, generalized to encoded and multilingual jailbreaks with that safety data removed. Primary cluster is Post-training alignment. |

## Methods and concepts

- [[Concept — Adversarial examples]]
- [[Concept — Jailbreaking (LLMs)]]
- [[Concept — Prompt injection]]
- [[Method — Greedy Coordinate Gradient (GCG)]]
- [[Benchmark — AdvBench]]
- [[Benchmark — HarmBench]]
- [[Benchmark — WMDP]]
- [[Benchmark — AgentDojo]]
- [[Method — Activation steering]]

## Open threads

[[Paper — Explaining and Harnessing Adversarial Examples (2014)]] fills the long-standing gap on why adversarial examples exist, superseding the founding paper's dense-pockets explanation (see the Tension / update on both pages).
GCG's own central open question - whether post-hoc RLHF-style alignment training can ever structurally close the jailbreak gap - is picked up directly by [[Cluster — Scheming & AI control]], which studies safety under the assumption a model's trained-in alignment cannot be fully trusted.
Adaptive attacks now reach safety monitors as well as models: [[Paper — Adaptive Attacks on Trusted Monitors Subvert AI Control Protocols (2025)]] imports this cluster's adaptive-attack standard into control evaluation (see [[Question — Can a monitor-based control protocol survive a model that attacks its monitor]]).
Open-weight safety is the least settled thread: [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]] removes safety with a rank-one weight edit and [[Paper — Fine-tuning Aligned Language Models Compromises Safety, Even When Users Do Not Intend To (2023)]] with ten examples, neither of which guard classifiers like Constitutional Classifiers address, and [[Paper — Tamper-Resistant Safeguards for Open-Weight LLMs (2024)]] gives the wiki its first evidence that safeguards can resist tampering - only against fine-tuning attacks, at a capability cost, and with weak results for refusal.

## Up

Top-level cluster; no parent.
