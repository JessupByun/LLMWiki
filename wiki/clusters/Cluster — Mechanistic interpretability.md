---
type: cluster
status: in-progress
updated: 2026-10-20
summary: "Reverse-engineering the internal computations of trained neural networks into human-interpretable algorithms, weights, and features, via three distinct lineages: circuit analysis, sparse dictionary learning, and top-down representation reading and control."
cluster: []
sources: []
tags: [interpretability, mechanistic-interpretability, transformers, safety]
aliases: ["mech interp"]
---

# Cluster — Mechanistic interpretability

Attempting to reverse engineer the detailed computations a trained neural network performs, the way a programmer might reverse engineer a compiled binary into human-readable source code.
The motivating bet, made explicit by the paper that opens this cluster, is that this could eventually let researchers explain a model's current safety problems, catch new ones, and anticipate the failure modes of future models that haven't been built yet - a more systematic alternative to purely behavioral evaluation.

This cluster starts where the field starts for language models: the smallest possible toy transformers, studied in [[Paper — A Mathematical Framework for Transformer Circuits (2021)]] until a simple, general mechanism (the induction head) falls out of the math, then traced to models of every size by [[Paper — In-context Learning and Induction Heads (2022)]].
The circuit-analysis and dictionary-learning lineages both inherit this founding move: rewrite the model, without changing it, into a form whose parameters are directly interpretable.
Circuit analysis carries it to finding that mechanism at scale; dictionary learning carries it into the polysemanticity and superposition problem that appears once MLP layers are added back in.

Three genuinely distinct lineages now converge on that shared goal.
The **circuit-analysis** lineage traces a specific behavior down to the components causally responsible for it: hand-verified in a real (non-toy) model by [[Paper — Interpretability in the Wild - a Circuit for Indirect Object Identification in GPT-2 small (2022)]], and automated at production scale by [[Paper — Circuit Tracing - Revealing Computational Graphs in Language Models (2025)]], whose attribution graphs [[Paper — On the Biology of a Large Language Model (2025)]] applies across roughly ten case studies.
The **sparse dictionary-learning** lineage decomposes a model's activations into a large, mostly-monosemantic basis of interpretable features: [[Paper — Toy Models of Superposition (2022)]] sets the problem, [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]] solves it on a one-layer model, and [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]] and [[Paper — Scaling and Evaluating Sparse Autoencoders (2024)]] scale it to Claude 3 Sonnet and GPT-4.
A dictionary alone says which features exist, not how they combine; [[Paper — Sparse Feature Circuits - Discovering and Editing Interpretable Causal Graphs in Language Models (2024)]] is the cluster's earliest bridge between the two lineages, building causal circuits out of SAE features, and Circuit Tracing takes the same idea to production scale.
The **representation-engineering** lineage skips both of these bottom-up steps entirely, treating a whole activation-space direction for a high-level concept - honesty, power-seeking, harmfulness - as the right unit of analysis on its own, extracted from contrastive prompts by [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]]; its unsupervised starting point is [[Paper — Discovering Latent Knowledge in Language Models Without Supervision (2022)]], which finds a truth direction from logical consistency alone.
Its causal tool is [[Method — Activation steering]]: [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]] shows a single direction controls whether open chat models refuse, and [[Paper — Persona Vectors - Monitoring and Controlling Character Traits in Language Models (2025)]] automates finding such directions for character traits and uses them to monitor and shape finetuning.
A fourth use cuts across all three: interpretability as an *auditing and monitoring* tool - tested blind in [[Paper — Auditing Language Models for Hidden Objectives (2025)]], where SAE features helped find a hidden objective but a simple search baseline matched the fastest solution, and as a live deception monitor in [[Paper — Detecting Strategic Deception Using Linear Probes (2025)]], where linear probes generalized from toy contrasts to realistic deception but also tracked deception-adjacent topics.

## Members

| Page | Why it's here |
|------|----------------|
| **Foundations** | |
| [[Paper — A Mathematical Framework for Transformer Circuits (2021)]] | Opens mechanistic interpretability for transformer language models: reframes attention as independent, additive read/write operations on a shared residual stream, and discovers the induction head, the first general in-context-learning circuit found in an actual transformer. |
| [[Paper — In-context Learning and Induction Heads (2022)]] | Extends induction heads from a toy-model curiosity to a candidate explanation for most in-context learning in transformers of any size, via a purely behavioral definition, causal ablations, and the discovery of a universal early-training "phase change." |
| **Dictionary learning** | |
| [[Paper — Toy Models of Superposition (2022)]] | Demonstrates, in a fully understood toy model, that neural networks represent more features than they have neurons by packing them into almost-orthogonal directions - resolving the first paper's admitted blind spot around MLP-layer polysemanticity and setting the dictionary-learning research agenda this cluster pursues next. |
| [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]] | Trains a sparse autoencoder on a one-layer transformer's MLP activations and extracts thousands of far-more-interpretable-than-neurons features, giving the first compelling proof that dictionary learning can recover real structure from superposition. |
| [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]] | Scales the same sparse-autoencoder technique from a one-layer toy model up to a real production model (Claude 3 Sonnet), recovering millions of abstract features and giving the first steering evidence at production scale (the Golden Gate Bridge feature) that a recovered feature actually drives a deployed model's behavior - extending the clamping-based causal validation Towards Monosemanticity had already done on its one-layer toy model. |
| [[Paper — Scaling and Evaluating Sparse Autoencoders (2024)]] | The dictionary lineage's engineering paper: TopK SAEs with few dead features, clean scaling laws up to 16 million features on GPT-4, and metrics that judge features by usefulness rather than reconstruction. |
| **Circuits** | |
| [[Paper — Interpretability in the Wild - a Circuit for Indirect Object Identification in GPT-2 small (2022)]] | This cluster's first hand-verified circuit in a real, non-toy model: reverse-engineers 26 attention heads implementing indirect object identification in GPT-2 small, and introduces path patching plus quantitative criteria (faithfulness, completeness, minimality) for checking a claimed circuit is actually correct. |
| [[Paper — Sparse Feature Circuits - Discovering and Editing Interpretable Causal Graphs in Language Models (2024)]] | Joins the circuit and dictionary lineages: circuits whose nodes are SAE features, found with gradient approximations, plus SHIFT - deleting human-judged spurious features to fix a classifier with no extra labeled data. |
| [[Paper — Circuit Tracing - Revealing Computational Graphs in Language Models (2025)]] | The direct sequel to Scaling Monosemanticity's own "what's next" question: introduces cross-layer transcoders and attribution graphs to trace how interpretable features causally combine into a specific output, not just list which features exist - at production scale, building on the feature-circuit idea [[Paper — Sparse Feature Circuits - Discovering and Editing Interpretable Causal Graphs in Language Models (2024)]] introduced on small models. |
| [[Paper — On the Biology of a Large Language Model (2025)]] | Applies attribution graphs to Claude 3.5 Haiku across roughly ten case studies, including mechanistic confirmation that a model's chain-of-thought can be fabricated to match a user's hint, and a hidden training-time goal exposed despite the model never admitting to it. |
| **Representation engineering** | |
| [[Paper — Discovering Latent Knowledge in Language Models Without Supervision (2022)]] (CCS) | Reads what a model "believes" from its activations with no labels and no outputs, using only the constraint that a statement and its negation get opposite truth values - the empirical start of eliciting latent knowledge, and the method Representation Engineering later benchmarks against. |
| [[Paper — Representation Engineering - A Top-Down Approach to AI Transparency (2023)]] | A genuinely different, top-down approach: extracts a single "reading vector" for a high-level concept from unlabeled contrastive prompts, using it to both detect the concept (lie detection) and steer generation toward or away from it, with an explicit early claim of traction on deceptive alignment. |
| [[Paper — Refusal in Language Models Is Mediated by a Single Direction (2024)]] | The representation-engineering lineage's sharpest causal result: refusal in 13 open chat models is one difference-in-means direction, which can be ablated or added at will and folded into the weights as a jailbreak. Dual-tagged Adversarial robustness & security. |
| [[Paper — Persona Vectors - Monitoring and Controlling Character Traits in Language Models (2025)]] | Turns concept directions into a lifecycle tool: automatically extracted trait vectors monitor a model's persona, screen finetuning data before training, and prevent unwanted shifts by steering during training. Dual-tagged Post-training alignment. |
| **Applied: auditing and monitoring** | |
| [[Paper — Auditing Language Models for Hidden Objectives (2025)]] | Interpretability put to work in a blind alignment audit: SAE features found a hidden objective, though a semantic-search baseline matched the fastest team - a candid test of what interpretability adds in practice. Primary cluster is Scheming & AI control; its model organism is the one the Biology paper later probes. |
| [[Paper — Detecting Strategic Deception Using Linear Probes (2025)]] | Puts activation probing to work as a deception monitor on realistic agentic tasks, finding strong generalization from toy training contrasts but spurious correlations with morality. Primary cluster is Scheming & AI control. |

## Methods and concepts

- [[Concept — Induction heads]]
- [[Concept — Residual stream]]
- [[Concept — Superposition]]
- [[Method — Sparse Autoencoder (SAE)]]
- [[Method — Activation steering]]

## Open threads

Two practical questions are open: whether top-down reading methods beat unsupervised probing in general (Representation Engineering reports beating CCS, in its own comparison), and what interpretability adds over simple baselines in real audits - [[Paper — Auditing Language Models for Hidden Objectives (2025)]] found SAE features helpful, but a semantic-search baseline matched its fastest solution.
Scaling Monosemanticity's dictionary-completeness question remains open, and [[Paper — Scaling and Evaluating Sparse Autoencoders (2024)]] sharpens it: even a 16-million-feature GPT-4 dictionary costs downstream loss equal to a model trained with about 10% of GPT-4's compute.
Sparse Feature Circuits, then Circuit Tracing at scale, pick up a different thread - not "how many more features are there," but "how do existing features causally combine into a specific output."
What a jailbreak disrupts is unsettled: Representation Engineering found Vicuna's harmfulness representation intact under adversarial suffixes, while the refusal-direction paper found a suffix suppresses the refusal direction in Qwen 1.8B (see Tension / update on both pages).
Whether activation-based monitors are robust is open: the linear-probe paper's probes generalize from simple contrasts to realistic deception but fire on honest talk about deception, a probing method that catches Sleeper Agents performs about at chance on misleading behavior RLHF produced unintentionally ([[Paper — Language Models Learn to Mislead Humans via RLHF (2024)]]), Persona Vectors' activation-penalty variant failed, the authors suspect because the trait moved to other directions, and none has been tested against a model optimizing to evade it (see [[Question — Do activation probes catch deception that arises naturally rather than by instruction or planting]]; compare the chain-of-thought obfuscation result in [[Paper — Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation (2025)]]).
[[Question — Is in-context learning genuine learning or pattern recognition]] tracks the open thread between this cluster's induction-head evidence and [[Concept — In-context learning]]'s behavioral framing in [[Cluster — Foundational canon]].

## Up

Top-level cluster; no parent.
