---
type: cluster
status: in-progress
updated: 2026-09-20
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

This is explicitly flagged in `BACKLOG.md` as the thinnest north-star pillar - only 2 papers despite being a named lens.
A queued expansion (`BACKLOG.md` Group V) would roughly triple it: Madry et al.'s adversarial-training (PGD) paper (foundational, currently missing landmark), Perez et al.'s automated red-teaming (already cited by name, not linked, in Sleeper Agents' own Problem-it-solved section), Greshake et al. on indirect prompt injection (the standard cite for that distinct threat model), two Benchmark-type gaps CLAUDE.md itself name-checks with no page yet (HarmBench, WMDP), Debenedetti et al.'s AgentDojo (agentic prompt-injection specifically, pairing with Greshake), and Anthropic's Many-shot Jailbreaking (likely non-arXiv, ask before fetching).
Goodfellow et al. 2014 (FGSM and the linearity hypothesis, which supersedes this paper's dense-pockets explanation) remains a gap too, tracked on [[Concept — Adversarial examples]].
GCG's own central open question - whether post-hoc RLHF-style alignment training can ever structurally close the jailbreak gap - is picked up directly by [[Cluster — Scheming & AI control]], which studies safety under the assumption a model's trained-in alignment cannot be fully trusted.

## Up

Top-level cluster; no parent.
