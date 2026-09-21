---
type: paper
status: mature
updated: 2026-09-20
summary: "Reverse-engineers a 26-attention-head circuit that implements indirect object identification in GPT-2 small end to end, the most detailed reconstruction of a real natural-language behavior in an actual transformer to date, and introduces path patching plus three quantitative criteria (faithfulness, completeness, minimality) for judging whether a claimed circuit explanation is actually correct."
cluster: ["Mechanistic interpretability"]
sources: ["raw/papers/mechanistic-interpretability/Paper — Interpretability in the Wild - a Circuit for Indirect Object Identification in GPT-2 small (2022).pdf"]
tags: [mechanistic-interpretability, circuits, attention-heads, transformers]
aliases: ["arxiv:2211.00593", "Wang et al. 2022", "IOI circuit"]
---

# Paper — Interpretability in the Wild: a Circuit for Indirect Object Identification in GPT-2 small (2022)

## Metadata

arXiv:2211.00593 (v1, Nov 2022).
Kevin Wang, Alexandre Variengien, Arthur Conmy, Buck Shlegeris, Jacob Steinhardt.
Redwood Research, with Jacob Steinhardt also at UC Berkeley.

## Impact

Every other circuit-level result in this wiki's mechanistic-interpretability cluster comes from a deliberately tiny toy transformer, studying a phenomenon simple enough to analyze in isolation.
This paper does the harder thing: it reverse-engineers a real natural-language behavior - indirect object identification, correctly completing "When Mary and John went to the store, John gave a drink to" with "Mary" - inside an actual, non-toy language model, GPT-2 small, and traces the full mechanism down to 26 individual attention heads across 7 functional classes.
It is, by the authors' own claim, the most detailed end-to-end reverse-engineering of a natural behavior in a transformer language model to date, and it introduces both a new causal-intervention technique (path patching) and a set of quantitative criteria for actually checking whether a claimed circuit is correct, rather than merely plausible.

## Problem it solved

Mechanistic interpretability work up to this point split into two unsatisfying camps: painstaking reconstructions of simple behaviors in small, often synthetic models, or broad-strokes descriptions of complicated behavior in large models with no attempt at a complete, verified account.
Neither approach demonstrates that a real, natural-language task performed by an actual deployed-scale model can be understood down to its individual components, and neither offers a way to check whether a proposed explanation is genuinely correct rather than just a story that sounds right - a real risk, since prior work had shown circuit explanations can be non-rigorous or actively misleading if not checked carefully.

## Core idea

Indirect object identification admits a simple, human-interpretable three-step algorithm: identify every name mentioned so far, remove whichever name is duplicated, and output whichever name remains.
The paper finds GPT-2 small implements almost exactly this algorithm using three major head classes that map onto the three steps - **Duplicate Token Heads** detect when a name has already appeared and mark its position, **S-Inhibition Heads** use that signal to suppress attention toward the duplicated name, and **Name Mover Heads** attend to and copy whichever name is left - plus four supporting classes: **Previous Token Heads** and **Induction Heads**, which help Duplicate Token Heads locate the repeated name; **Negative Name Mover Heads**, which write in the opposite direction of the correct answer, apparently to hedge against high-confidence mistakes; and **Backup Name Mover Heads**, which do nothing under normal operation but take over the Name Mover Heads' job almost seamlessly if those heads are ablated.
The paper's key methodological contribution, **path patching**, is what makes tracing this circuit tractable: rather than testing one head in isolation, it lets you ask exactly how much a given head's output changes a specific downstream computation, holding every other path through the network fixed, by combining a clean run, a corrupted run, and a careful reconstruction that isolates just the one path of interest.

## How it's built

Circuit discovery works backward from the logits: path patching first identifies which heads directly influence the final prediction (the Name Mover and Negative Name Mover Heads, discovered because they attend strongly to the correct name token and copy it into the output), then recursively asks which heads influence those heads' attention patterns (S-Inhibition Heads, discovered by tracing what feeds their queries), then which heads influence those (Duplicate Token Heads and Induction Heads, discovered by tracing their values), continuing until the whole causal chain from input tokens to output prediction is mapped.
Once the circuit is proposed, it is checked against three formal criteria rather than accepted on the strength of the story alone: **faithfulness** asks whether the circuit alone reproduces close to the full model's performance on the task; **completeness** asks whether the circuit and the full model behave similarly even under many different patterns of knocking out sub-parts, not just as a whole; and **minimality** asks whether every node in the circuit is actually necessary, by checking that removing it causes a real drop in performance in some context.

## Results

The discovered circuit spans 26 attention heads - just 1.1% of all head-position pairs in the model - and alone reproduces 87% of the full model's logit-difference performance between the correct and incorrect name, the paper's headline faithfulness number.
Completeness testing surfaces a genuine limitation rather than a clean success: random and class-based knockout searches suggest the circuit is complete, but a greedy adversarial search finds subsets whose removal creates up to an 87% incompleteness score, meaning some interpretable-looking coverage claims don't fully hold up under the most adversarial test the paper could construct.
The most surprising individual finding is the Backup Name Mover Heads: knocking out all three main Name Mover Heads drops the model's logit difference by only 5%, because a separate set of heads - dormant and invisible under normal operation - immediately compensates, a redundancy the authors suspect is an artifact of dropout during training encouraging the network to hedge against losing any single component.
Understanding the circuit is also shown to be practically useful, not just descriptive: knowing that Duplicate Token Heads rely on detecting a repeated name, the authors construct an adversarial passage with a duplicated indirect object rather than a duplicated subject, and this flips the model's prediction to the wrong name 23% of the time, against just 0.4% on a matched control passage with no such duplication.

## Why it endures

The paper's own framing draws directly on the biology model-organism analogy this wiki's Scheming & AI control cluster also invokes: zooming in on one clearly defined task in one specific model, studied in enough depth to be genuinely trustworthy, surfaces phenomena - redundant backup circuitry, heads that write in the wrong direction on purpose, known mechanisms like induction heads reused for an unexpected sub-task - that are plausibly present throughout the model and the field more broadly, even though only one narrow slice was ever fully verified.
Path patching itself becomes a standard causal-intervention tool for later circuit-discovery work, and the faithfulness/completeness/minimality framework becomes a template other interpretability papers borrow when they need to argue a proposed circuit is actually correct rather than merely a compelling story.

## Limitations

The authors are explicit that several components remain only partially understood even in this unusually detailed account: the mechanism behind S-Inhibition Heads' attention pattern and the role MLP layers play are both left incompletely characterized, and the model is GPT-2 small, several orders of magnitude below frontier scale, with a preliminary extension to GPT-2 medium already showing more complex, less cleanly-interpretable head behavior.
The completeness criterion's own greedy-search failure mode is presented as an open problem rather than something resolved by the paper - some interpretable-looking circuit boundaries do not survive the most adversarial available check, and the paper does not fully explain why.

## Relations

- Reuses [[Concept — Induction heads]] in an unexpected role: two of the circuit's supporting heads implement the same prefix-matching-and-copying mechanism [[Paper — A Mathematical Framework for Transformer Circuits (2021)]] and [[Paper — In-context Learning and Induction Heads (2022)]] document for general in-context learning, repurposed here as a narrow positional-signal detector inside a much more specific task circuit.
- Operates on and traces information through [[Concept — Residual stream]], the same shared communication channel those foundational circuits papers formalize.
- Fills the specific gap this cluster's other members share: everything else in [[Cluster — Mechanistic interpretability]] before this paper is either a toy-transformer result or a sparse-autoencoder feature decomposition; this is the cluster's first hand-verified circuit in a real, non-toy model.
- Its faithfulness/completeness/minimality validation framework is the direct methodological ancestor of the automated-circuit-discovery and attribution-graph lineage this cluster's later scaling work builds toward.

## Up

- [[Cluster — Mechanistic interpretability]]
