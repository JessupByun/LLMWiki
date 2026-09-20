---
type: paper
status: mature
updated: 2026-09-19
summary: "Demonstrates, in a fully understood toy model, that neural networks represent more features than they have neurons by packing them into almost-orthogonal directions - and that this superposition follows a phase change, organizes into specific geometric structures, and can support real computation."
cluster: ["Mechanistic interpretability"]
sources: ["https://transformer-circuits.pub/2022/toy_model/index.html"]
tags: [interpretability, mechanistic-interpretability, superposition, polysemanticity, safety]
aliases: ["arxiv:2209.10652", "Elhage et al. 2022"]
---

# Paper — Toy Models of Superposition (2022)

## Metadata

Elhage, Hume, Olsson, Schiefer, Henighan, Kravec, Hatfield-Dodds, Lasenby, Drain, Chen, Grosse, McCandlish, Kaplan, Amodei, Wattenberg & Olah, Anthropic and Harvard, published September 14, 2022 on the Transformer Circuits Thread (transformer-circuits.pub); also cross-posted to arXiv (arxiv:2209.10652, September 21, 2022).

## Impact

Directly resolves the admitted blind spot from [[Paper — A Mathematical Framework for Transformer Circuits (2021)]]: that paper's methods could reverse-engineer attention but had no traction on MLP-layer neurons, which are often "polysemantic" - responding to several unrelated concepts rather than one clean feature.
This paper gives, for the first time, a fully worked toy model where polysemanticity is not a mystery but a predictable, provable consequence of a phenomenon it names **[[Concept — Superposition]]**: representing more features than a network has dimensions by tolerating a small, structured amount of cross-feature interference.
It reframes the central obstacle to mechanistic interpretability as a specific, nameable problem - "solving superposition" - and sets the research agenda (sparse dictionary learning to find an overcomplete feature basis) that the next papers in this wiki's mech-interp thread directly pursue.

## Problem it solved

Interpretability researchers had long observed that some neurons cleanly correspond to a single interpretable feature while others are polysemantic, mixing several unrelated concepts, without a rigorous account of why.
"Superposition" had been informally hypothesized in prior work as an explanation, but nobody had unambiguously demonstrated it occurring in an actual trained network, distinguished it cleanly from the related-but-different idea of disentanglement, or shown what determines when it happens versus when a model instead dedicates a clean neuron to a feature.

## Core idea

Train a small ReLU network to compress a sparse, synthetic "feature vector" down to fewer dimensions and then reconstruct it.
When features are dense (frequently co-occurring), the network behaves exactly as intuition suggests: it represents only as many features as it has dimensions, orthogonally, like PCA - and a version of the model with no output nonlinearity always does this, regardless of sparsity, confirming that the effect depends on the nonlinearity.
But as features are made **sparse** (rarely active at once), the ReLU-output model starts representing *more* features than it has dimensions, by embedding them as directions that are almost, but not exactly, orthogonal.
One feature activating now causes small "interference" on the others, but because sparse features are rarely active simultaneously, this interference is cheap relative to the benefit of representing more features at all - and the ReLU's ability to zero out small negative values gives the network a way to filter much of the resulting noise.

The transition between three outcomes for any given feature - not represented at all, represented in superposition, or given a fully dedicated dimension - is a genuine **phase change**: the optimal weight configuration jumps discontinuously as sparsity or relative feature importance crosses a threshold, rather than degrading smoothly.
Superposed features aren't arranged arbitrarily: for identical, equally-important, equally-sparse features, the model provably arranges them into specific geometric structures - antipodal pairs at low sparsity, then triangles, pentagons, tetrahedra, and square antiprisms as sparsity increases - which turn out to be solutions to a generalized version of the Thomson problem, the classical physics problem of packing mutually-repelling points on a sphere.

Superposition is not just a storage trick.
A second toy model, trained to compute the absolute value function (which genuinely requires the ReLU nonlinearity, unlike simple reconstruction), shows the network can perform this computation while its inputs remain in superposition, using an "asymmetric superposition" motif: one neuron stores two features with unequal weights so that only one interferes with the other, while a second neuron inhibits the resulting interference before it can hurt the output.
Finally, adding a ReLU to the *hidden* layer (rather than only the output) creates a genuine "privileged basis," letting individual neurons be inspected directly - reproducing, in miniature, the exact mixture of clean and polysemantic neurons observed in real networks, including a resemblance to how real language-model neurons often have one dominant, easily-interpreted "primary" feature alongside weaker "secondary" ones.

## How it's built

All results come from small, fully-controlled ReLU networks trained on synthetic data engineered to match three properties believed to hold for real features: sparsity (most features are usually zero), far more potential features than available neurons, and unequal importance across features.
Because the "ground truth" features are defined by construction rather than inferred, the toy models can be probed exhaustively - visualizing raw weight matrices, per-feature "dimensionality" scores, and geometric graphs of which features share a dimension - in a way real trained networks don't permit.

## Results

Polysemantic and monosemantic neurons can coexist in the very same layer of the very same model, exactly as observed empirically in real networks; the fraction of polysemantic neurons increases with feature sparsity, matching the empirical trend that later, more abstract layers of InceptionV1 (where features are rarer) are more polysemantic than early layers, and that early transformer MLP layers (which must distinguish rare token-disambiguation cases) are often extremely polysemantic.
Vulnerability to adversarial perturbations increases by more than 3x as superposition forms, tracking the number of features packed per dimension - the interference terms that make superposition efficient are also an exploitable attack surface, giving a mechanistic account connected to [[Concept — Adversarial examples]].
Learning dynamics show discrete "energy level" jumps (features abruptly swapping which geometric configuration they occupy mid-training, each jump visible as a small but sudden drop in the loss curve) rather than smooth convergence - a training-time discreteness the authors explicitly connect to the induction-head phase change in [[Paper — In-context Learning and Induction Heads (2022)]] and to grokking more broadly.

## Why it endures

The paper's most consequential move is strategic rather than empirical: it argues that "solving superposition" - by any method that lets a researcher enumerate a model's full feature set - is equivalent to, or a precondition for, most strong interpretability-based safety guarantees, including ruling out the presence of specific circuits ("this model has no deliberately-deceptive-behavior feature") and catching "unknown unknowns" that behavioral testing alone would miss.
It lays out three candidate strategies (train models without superposition; find an overcomplete basis describing superposition after training via sparse coding; hybrid approaches) and is explicit that the second - dictionary learning to recover an interpretable, overcomplete feature basis from a superposed model - is a massive but tractable-looking engineering problem.
This is precisely the approach the next two papers queued in this wiki (Towards Monosemanticity, Scaling Monosemanticity) carry out at increasing scale, making this paper the direct conceptual bridge between "here is why neurons are hard to interpret" and "here is how to interpret them anyway."

## Limitations

Every result comes from small ReLU toy models with hand-chosen synthetic data; the authors are explicit that only some of their findings (the existence of superposition and polysemantic/monosemantic neurons, and probably the adversarial-examples connection) seem likely to generalize to real networks, while others (the precise polytope geometry, the exact learning-dynamics story) are flagged as much more speculative and possibly idiosyncratic to the toy setup.
The "ReLU hidden layer" model used to demonstrate a privileged basis has a known weakness: the network will circumvent the nonlinearity whenever it can (e.g. by biasing all neurons positive), so it is only informative in the narrow regime where the nonlinearity is actually load-bearing.
The paper ends with a long, explicit list of unresolved questions - including whether superposition should be expected to vanish with scale, and how to estimate real models' feature-importance and sparsity curves - that it does not attempt to answer.

## Relations

- Directly resolves the MLP-interpretability blind spot admitted in [[Paper — A Mathematical Framework for Transformer Circuits (2021)]].
- Names and defines [[Concept — Superposition]] for the first time as a rigorous, demonstrated phenomenon rather than a hypothesis.
- Connects its training-time "energy level" jumps to the phase change documented in [[Paper — In-context Learning and Induction Heads (2022)]].
- Strengthens [[Concept — Adversarial examples]] with a structural, representational account of why interference-exploiting perturbations exist.
- Its "Approach 2: finding an overcomplete basis" directly motivates the sparse-dictionary-learning method used in [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]] and [[Paper — Scaling Monosemanticity - Extracting Interpretable Features from Claude 3 Sonnet (2024)]].

## Up

- [[Cluster — Mechanistic interpretability]]
