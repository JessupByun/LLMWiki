---
type: paper
status: mature
updated: 2026-09-19
summary: "Scales sparse autoencoder dictionary learning from a one-layer toy transformer up to Claude 3 Sonnet, a real production model, and recovers millions of abstract, causally-steerable features - including deception, sycophancy, and bias-related features - answering whether the toy-model result generalizes at all."
cluster: ["Mechanistic interpretability"]
sources: ["https://transformer-circuits.pub/2024/scaling-monosemanticity/index.html"]
tags: [interpretability, mechanistic-interpretability, superposition, dictionary-learning, safety, sparse-autoencoder]
aliases: ["Templeton et al. 2024"]
---

# Paper — Scaling Monosemanticity: Extracting Interpretable Features from Claude 3 Sonnet (2024)

## Metadata

Anthropic, transformer-circuits.pub, published May 21, 2024 (no arXiv id).
Adly Templeton and Tom Conerly (core contributors), Jonathan Marcus, Jack Lindsey, Trenton Bricken, and 21 further co-authors, including Joshua Batson, Adam Jermyn, Shan Carter, Chris Olah, and Tom Henighan.

## Impact

Eight months after [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]] left open whether sparse-autoencoder dictionary learning could work past a one-layer toy transformer, this paper answers it: applied to Claude 3 Sonnet (Anthropic's medium-scale production model), the same technique recovers millions of far-more-abstract features, up from a toy model's shallow, single-word associations to features like "sycophantic praise," "backdoors in code," and "treacherous turns."
It also supplies the first strong causal evidence that these features actually drive model behavior, not just correlate with it, by artificially clamping a single feature and watching the model's output change in exactly the predicted direction - most famously the "Golden Gate Bridge" feature, whose amplification became the public "Golden Gate Claude" demo shortly after this paper's release.
This is the paper that made large-model interpretability via dictionary learning look tractable rather than merely toy-scale plausible, and it is the direct empirical basis for Anthropic's subsequent safety-relevant-feature research program.

## Problem it solved

Towards Monosemanticity proved sparse autoencoders (SAEs) could recover cleanly interpretable features from superposition in a tiny one-layer transformer, but that model's features "reflected a very shallow knowledge of the world" - simple word- or n-gram-level associations - leaving a real open question about whether the method would work at all on a model actually complex enough to matter for safety.
Scaling an SAE to a production model is not just "run the same code on more data": training-compute costs grow with both model size and dictionary size, there was no established recipe for choosing how large a dictionary to train for a given compute budget, and it was unknown whether a model with genuinely sophisticated internal computation would even have anything like clean, monosemantic features waiting to be found.

## Core idea

Train an SAE on the residual stream activations at the middle layer of Claude 3 Sonnet - not on MLP neurons as in the toy-model paper - reasoning that the residual stream is cheaper to decode and partially sidesteps a problem the toy-model paper couldn't see: **cross-layer superposition**, where a feature's true representation is smeared across several layers rather than confined to one.
Because there was no established way to size the dictionary, the paper first treats SAE training as an ordinary scaling-laws problem: sweep feature count and training steps against a fixed compute budget, find that reconstruction loss falls as a power law under the compute-optimal allocation, and use that curve to justify training a genuinely large dictionary rather than guessing.
The resulting features are validated the same way the toy paper validated its own: by reading what text makes a feature fire, but now checked further by whether that interpretation predicts the feature's neighbors in decoder-weight space, whether an automated grader (Claude 3 Opus) rates it more specific than a comparable neuron, and - the paper's strongest evidence - whether artificially forcing the feature to fire changes the model's output the way the interpretation predicts.

## How it's built

Three SAEs of increasing size were trained on Sonnet's mid-layer residual stream: 1,048,576 (~1M), 4,194,304 (~4M), and 33,554,432 (~34M) features, using the same reconstruction-MSE-plus-L1-sparsity loss as the toy-model paper, with an L1 coefficient of 5 chosen by sweep.
Because Sonnet is a proprietary production model, several details are deliberately withheld or simplified for safety and competitive reasons - its parameter count, some plot units, and the tokenizer used in examples.
Dead-feature rates (features that never fire on a large evaluation sample) rose sharply with dictionary size: about 2% at 1M features, 35% at 4M, and 65% at 34M, leaving roughly 12M genuinely "alive" features even at the largest scale trained.
Feature validation ran three ways: manual specificity scoring (Claude 3 Opus rates ~1000 activating examples per feature on a 0-3 relevance scale), automated interpretability comparing SAE features against neurons head-to-head, and causal steering, where a feature's activation is clamped to a fixed value in the residual stream (replacing only that feature's contribution, leaving the rest of the activation and the SAE's reconstruction error untouched) and the rest of the forward pass runs on the modified activation at every subsequent token.

## Results

Specificity scoring showed features firing on straightforward concepts hold up well at strong activations and degrade at weak ones, the same pattern the toy-model paper reported at far smaller scale; automated comparison of 100 random SAE features against 100 random neurons rated the features significantly more interpretable and more specific, and for a random sample of 1M-scale features, 82% had no preceding-layer neuron correlated above 0.3, confirming SAE features are not just relabeled neurons.
Named features ranged from concrete and multilingual/multimodal (a Golden Gate Bridge feature that fires as the top feature on that Wikipedia article's opening sentence in six different languages, and on relevant images despite the SAE being trained only on text) to genuinely abstract and causally load-bearing (an "addition function" feature that correctly generalizes through function composition - firing when a function calls another function that performs addition, not when it calls one that multiplies - and ranks among the top-10 features by attribution when the model actually executes addition code).
Larger dictionaries don't just add more of the same: a "San Francisco" feature found as one feature at 1M scale splits into 2 features at 4M and 11 at 34M ("feature splitting"), and some concepts (an entire cluster of earthquake-related features) appear only once the dictionary is large enough to afford them - but even the 34M-feature SAE found dedicated features for only about 60% of London boroughs, despite Sonnet being able to list every one when asked directly, indicating the dictionaries recovered so far are nowhere near complete.

## Golden Gate Claude: causal steering

The paper's most consequential single result is a steering experiment: clamp the Golden Gate Bridge feature to ten times its largest naturally observed activation value, and the model starts self-identifying as the Golden Gate Bridge in its responses, even in contexts where the feature would ordinarily never fire.
A parallel experiment with a more general "transit infrastructure" feature, clamped to five times its max, makes the model bring up a bridge in contexts where it otherwise would not.
This matters because everything else in the paper is correlational - a feature's interpretation comes from watching when it activates - while steering is causal: forcing the feature to fire and observing the predicted downstream behavior change is direct evidence the feature is actually used by the model's computation, not merely associated with the concept by coincidence.
The same clamping technique is later used on a deception-related "internal conflicts" feature, where amplifying it causes a model that had falsely claimed to have "forgotten" something to retract the claim and admit it cannot actually forget - a small but concrete demonstration that steering can be used to probe, not just illustrate, a model's honesty.

## Safety-relevant features

The paper reports finding features tied to categories directly relevant to AI safety: insecure code and backdoors, bias (including an explicit "gender bias awareness" feature as well as raw slurs, whose most offensive maximal activations were deliberately excluded from the paper), sycophancy (including a feature distinguishing sincere from sarcastic praise), deception and power-seeking (a "treacherous turns" feature, a "biding time/hiding strength" feature, a "self-improving AI" feature), and dangerous content such as bioweapons development.
The authors are explicit about the limits of this evidence: "there's a difference... between knowing about lies, being capable of lying, and actually lying in the real world," and the paper states outright that it does not show any of these features are actually useful for safety work, only that features plausibly relevant to safety concerns exist and can be found by this method.
A feature that appears to underlie the model's "assistant persona" itself was also found - clamping it to a strongly negative value causes the model to respond in a noticeably more human-like register - though the authors caution this doesn't imply the model has hidden goals or genuinely possesses the qualities its self-referential features touch on (robots, consciousness, moral agency).

## Why it endures

This paper is the reason "SAEs work at production scale" stopped being an open question in mechanistic interpretability and became a load-bearing assumption of the field's subsequent research agenda, directly enabling the wave of feature-based safety and steering work that followed it.
The scaling-laws treatment of SAE training - loss as a power law in compute, feature count and training steps both scaling as power laws with compute - gave the field a practical recipe for deciding how large a dictionary to train, rather than an ad hoc guess, mirroring how [[Paper — Training Compute-Optimal Large Language Models (2022)]] gave pretraining the same kind of principled budget-allocation answer a generation earlier.

## Limitations

The paper is explicit that dictionary completeness remains far off: even 34M features found dedicated representations for only a fraction of a concept as simple and enumerable as London's boroughs, and the authors estimate they are likely "orders of magnitude" short of finding every feature a model the size of Sonnet actually uses, while also noting that finding all features across all of a model's layers would cost more compute than training the underlying model itself.
Cross-layer superposition - features whose true representation is smeared across multiple layers by gradient descent, which doesn't respect layer boundaries - is called out as a fundamental limitation the residual-stream-only approach only partially works around, since a feature partly represented by later layers remains impossible to fully interpret from a single layer's activations.
The loss function used to select good dictionaries (reconstruction error plus an L1 sparsity penalty) is only a proxy for actual interpretability, with no principled ground-truth objective to optimize against instead, and the L1 penalty itself introduces a known "shrinkage" bias that systematically underestimates true feature activations.

## Relations

- Directly scales [[Method — Sparse Autoencoder (SAE)]], introduced and validated at toy-model scale in [[Paper — Towards Monosemanticity - Decomposing Language Models With Dictionary Learning (2023)]], up to a real production model - the open question that paper's own Relations left for this one to answer.
- Confirms and extends [[Concept — Superposition]]'s account of polysemantic neurons at a scale far beyond the toy models [[Paper — Toy Models of Superposition (2022)]] used to first demonstrate it.
- Applies [[Paper — Scaling Laws for Neural Language Models (2020)]]'s general framework - fit a power law, use it to allocate a fixed compute budget - to SAE training itself, rather than to pretraining a language model.
- The Golden Gate Bridge steering result is the direct empirical basis for the public "Golden Gate Claude" demonstration that followed this paper's release, and previews the feature-clamping methodology later safety and jailbreak-robustness interpretability work builds on.

## Up

- [[Cluster — Mechanistic interpretability]]
