---
type: cluster
status: in-progress
updated: 2026-09-20
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
[[Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)]] answers the natural next question - can this be defended against with an actual guarantee, not just a demonstration - by reframing attack and defense as two halves of one min-max optimization problem.

The modern jailbreak and prompt-injection literature is the same problem relocated from pixel space to token space, which is why this cluster is scoped to attacks on learned systems generally rather than to vision.
Three genuinely distinct LLM-era threads now sit inside that relocation: **jailbreaking** (defeating a model's own safety training, whether by hand-crafted prompts, gradient-optimized suffixes, or - the newest lineage - simply exploiting long context windows via in-context learning), **prompt injection** (an external attacker planting instructions in content an application processes, with no need for the target to have any safety training to defeat in the first place, escalating from single-turn apps to genuinely agentic, tool-using ones), and **dangerous-capability benchmarking** (measuring and, increasingly, removing hazardous knowledge itself, rather than only testing whether a model can be talked into revealing it).
Automated red-teaming - training a second model to find failures rather than relying on human ingenuity - is the methodological thread connecting the model-organism half of this cluster to the deliberate-adversary half of [[Cluster — Scheming & AI control]].

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — Intriguing Properties of Neural Networks (2013)]] | Discovered adversarial examples - imperceptible perturbations that reliably fool networks - and showed the smoothness assumption behind kernel methods fails for deep nets. |
| [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] (GCG) | The LLM-era sequel: automates jailbreaking with a single adversarial suffix that transfers, purely black-box, from small open models to production systems including ChatGPT, Bard, and Claude. |
| [[Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)]] | Reframes adversarial robustness as a min-max saddle-point problem and shows PGD reliably solves the non-concave inner attack, giving adversarial training its first optimization-theoretic guarantee rather than an empirically-tuned mitigation. |
| [[Paper — Red Teaming Language Models with Language Models (2022)]] | Automates red-teaming itself: a second language model generates natural-language test cases that elicit tens of thousands of diverse failures - offensive replies, leaked training data, leaked contact info - from a target model, with no human writing a single test case. |
| [[Paper — Not What You've Signed Up For - Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection (2023)]] | Names indirect prompt injection - malicious instructions planted in third-party content an application retrieves rather than in the user's own prompt - and demonstrates working attacks against real production systems including Bing Chat and GitHub Copilot. |
| [[Benchmark — HarmBench]] | Standardizes jailbreak evaluation after finding at least 9 mutually incompatible setups already in use, with a classifier stress-tested against gaming that catches nonstandard successful completions substring-matching benchmarks miss entirely. |
| [[Benchmark — WMDP]] | A safely-publishable proxy for hazardous biosecurity, cybersecurity, and chemical-security knowledge, paired with an unlearning method (RMU) that drives frontier models' scores toward chance while barely touching general capability. |
| [[Benchmark — AgentDojo]] | Extends indirect prompt injection into the genuinely agentic, tool-using case with formal utility and security metrics, finding the most capable agents are consistently the easiest to hijack and no defense reaches zero attack success. |
| [[Paper — Many-shot Jailbreaking (2024)]] | A third jailbreak lineage needing neither cleverness nor gradient access: stuffing a long context window with hundreds of fake compliant turns reliably overrides safety training through ordinary in-context learning, traced mechanistically to induction heads. |

## Methods and concepts

- [[Concept — Adversarial examples]]
- [[Concept — Jailbreaking (LLMs)]]
- [[Method — Greedy Coordinate Gradient (GCG)]]
- [[Benchmark — AdvBench]]
- [[Benchmark — HarmBench]]
- [[Benchmark — WMDP]]
- [[Benchmark — AgentDojo]]

## Open threads

Group V is done as of this ingest, closing out Madry, Perez, Greshake, HarmBench, WMDP, AgentDojo, and Many-shot Jailbreaking - this cluster has gone from the thinnest north-star pillar (2 papers) to one of the more thoroughly worked, spanning pixel-space adversarial examples, three distinct jailbreak lineages, indirect prompt injection in both single-turn and agentic form, automated red-teaming, and dangerous-capability benchmarking with unlearning as a mitigation.
Goodfellow et al. 2014 (FGSM and the linearity hypothesis, which supersedes the founding paper's dense-pockets explanation) remains a gap, tracked on [[Concept — Adversarial examples]].
GCG's own central open question - whether post-hoc RLHF-style alignment training can ever structurally close the jailbreak gap - is picked up directly by [[Cluster — Scheming & AI control]], which studies safety under the assumption a model's trained-in alignment cannot be fully trusted.

## Up

Top-level cluster; no parent.
