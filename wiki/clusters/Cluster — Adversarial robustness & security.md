---
type: cluster
status: in-progress
updated: 2026-08-07
summary: "Attacks on learned systems and defenses against them - adversarial examples, transferability, jailbreaks, and prompt injection."
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

The modern jailbreak and prompt-injection literature is the same problem relocated from pixel space to token space, which is why this cluster is scoped to attacks on learned systems generally rather than to vision.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — Intriguing Properties of Neural Networks (2013)]] | Discovered adversarial examples - imperceptible perturbations that reliably fool networks - and showed the smoothness assumption behind kernel methods fails for deep nets. |
| [[Paper — Universal and Transferable Adversarial Attacks on Aligned Language Models (2023)]] (GCG) | The LLM-era sequel: automates jailbreaking with a single adversarial suffix that transfers, purely black-box, from small open models to production systems including ChatGPT, Bard, and Claude. |

## Methods and concepts

- [[Concept — Adversarial examples]]
- [[Concept — Jailbreaking (LLMs)]]
- [[Method — Greedy Coordinate Gradient (GCG)]]
- [[Benchmark — AdvBench]]

## Open threads

Obvious gaps: Goodfellow et al. 2014 (FGSM and the linearity hypothesis, which supersedes this paper's dense-pockets explanation), Madry et al. (adversarial training done properly), and the prompt-injection literature.
GCG's own central open question - whether post-hoc RLHF-style alignment training can ever structurally close the jailbreak gap - is picked up by the AI-control and model-organisms-of-misalignment literature queued in a later group of this backlog.

## Up

Top-level cluster; no parent.
