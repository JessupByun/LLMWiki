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

## Methods and concepts

- [[Concept — Adversarial examples]]

## Open threads

Obvious gaps: Goodfellow et al. 2014 (FGSM and the linearity hypothesis, which supersedes this paper's dense-pockets explanation), Madry et al. (adversarial training done properly), GCG / Zou et al. 2023 (transferable attacks on aligned LLMs), and the prompt-injection literature.

## Up

Top-level cluster; no parent.
