---
type: paper
status: mature
updated: 2026-09-24
summary: "Introduces membership inference: given only black-box query access to a trained classifier, an attacker trains 'shadow models' that imitate the target's behavior, then trains an attack model on the shadow models' own labeled in/out predictions to determine whether a specific record was part of the target's training set - achieving up to 94% attack accuracy against real machine-learning-as-a-service platforms with no knowledge of the target's architecture or training data."
cluster: ["ML security & privacy"]
sources: ["raw/papers/ml-security-and-privacy/Paper — Membership Inference Attacks Against Machine Learning Models (2017).pdf"]
tags: [privacy, membership-inference, ml-security]
aliases: ["arxiv:1610.05820", "Shokri et al. 2017"]
---

# Paper — Membership Inference Attacks Against Machine Learning Models (2017)

## Metadata

arXiv:1610.05820 (v2, Mar 2017).
Reza Shokri (Cornell Tech), Marco Stronati (INRIA), Congzheng Song (Cornell), Vitaly Shmatikov (Cornell Tech).

## Impact

Opens [[Cluster — ML security & privacy]]'s first pillar: does a trained model leak information about its own training data, just from its ordinary predictions?
The paper turns this into a concrete, quantitative attack - membership inference: given a data record and only black-box query access to a model, determine whether that record was used to train it.
Its central methodological contribution, the shadow-model technique, requires no knowledge of the target's architecture, training algorithm, or even (in the paper's strongest setting) any real training data at all, and the paper demonstrates the attack works against real commercial "machine-learning-as-a-service" platforms (Google Prediction API, Amazon ML) with up to 94% accuracy, including against a sensitive real-world hospital-discharge dataset.

## Problem it solved

Prior privacy attacks on ML either required white-box access to model parameters, or targeted a different question entirely (model inversion, which reconstructs a *class-representative* input rather than testing whether a *specific* record was a training member).
Nobody had shown that an attacker with only ordinary API-level query access - exactly the access "machine learning as a service" providers openly sell - could determine training-set membership for a specific record, without any assumption about the model's internals.

## Core idea

The key trick: turn machine learning against itself.
The attacker doesn't need to reverse-engineer why the target model behaves differently on members versus non-members of its training set - it just needs to observe *that* it does, which is nearly always true, since a model's prediction confidence tends to be higher and more sharply peaked on inputs it was trained on than on inputs it wasn't.
The attacker builds several **shadow models**, each trained on its own dataset in the same format as (but disjoint from) the target's training data, using the same MLaaS platform or a same-format local model.
Because the attacker generated the shadow training data themselves, they know exactly which records are "in" and which are "out" for each shadow model - ground truth that's unavailable for the real target.
Querying each shadow model on its own known members and non-members produces a labeled dataset of (prediction vector, true label, in/out) triples, which trains the actual **attack model**: a binary classifier that, given a record's true label and a model's prediction vector on it, outputs "in" or "out."
This same attack model is then pointed at the real target model's output for the record under investigation.

## How it's built

Three methods for building shadow training data without a copy of the target's real training set, in decreasing order of assumed attacker knowledge: (1) model-based synthesis, which needs no real or statistical data at all - a hill-climbing search queries the target model itself to synthesize records the target classifies with high confidence, on the theory that such records resemble genuine training data; (2) statistics-based synthesis, sampling each feature independently from its known marginal distribution; (3) noisy real data, a version of real data with some fraction of features randomly perturbed.
A separate attack model is trained per output class, since a model's confidence and its indicative "tell" for membership vary by class.
Evaluated across 5 datasets (CIFAR-10/100, a purchase-history dataset, Foursquare-based locations, MNIST, UCI Adult census) and 3 target-model types (Google Prediction API, Amazon ML, and locally-trained neural networks).

## Results

Membership inference against Google- and Amazon-trained models on a 10,000-record purchase dataset reaches 94% and 74% median accuracy respectively; even with no real or statistically-representative data at all (fully synthetic shadow-training data), attack accuracy against Google-trained models on the same task still reaches 90%.
Against the Texas hospital-discharge dataset - the paper's stand-in for a genuinely sensitive real-world case - attack accuracy exceeds 70% for many classes.
Attack success correlates strongly with the target model's overfitting (the gap between its training and test accuracy) and with the number of output classes, but overfitting is not the only factor: models trained on different platforms with the same degree of overfitting still leak different amounts, showing model structure and type independently matter.
Standard mitigations - restricting the prediction vector to top-k classes, coarsening its precision, increasing softmax temperature, L2 regularization - all reduce attack accuracy somewhat, but the attack remains effective even against a model that returns only its single most-likely label with no confidence score at all, by exploiting a residual signal: members and non-members tend to be *mislabeled differently* even when both are misclassified.

## Why it endures

The paper's own closing framing - that instead of the usual utility-versus-privacy tradeoff, "machine learning research and privacy research have similar objectives in this case" - names overfitting as the shared adversary of both accuracy and privacy, motivating a large body of later work using membership-inference accuracy itself as a privacy auditing metric, including as an empirical stress test for differentially private training (see [[Paper — Deep Learning with Differential Privacy (2016)]], which the paper's own related-work section explicitly identifies as the principled fix its purely empirical mitigations only approximate).
Its shadow-model technique - train surrogate models to generate labeled training data for a meta-classifier, when the ground truth you actually need is unavailable - recurs as a general pattern well beyond membership inference specifically.

## Limitations

The paper's own black-box threat model is already close to worst-case for the defender (queries return a full confidence vector, and the attacker can query the real target model as often as needed to synthesize shadow training data), but it does not consider attackers with any white-box access to model parameters or gradients, nor does it study the "adversary already knows the record is a plausible member" setting that later membership-inference work distinguishes from the population-level attack studied here.
Model inversion and membership inference are explicitly distinguished in the paper's own related-work discussion, but the paper does not investigate whether defenses effective against one transfer to the other.

## Relations

- Directly names [[Paper — Deep Learning with Differential Privacy (2016)]]'s framework, differential privacy, as the principled defense its own empirical mitigations (restricting prediction-vector granularity, regularization) only approximate - a differentially private model is secure against exactly this kind of attack by construction, whereas this paper's mitigations are ad hoc and only partially effective.
- Distinguished in its own related-work section from *model inversion* attacks (Fredrikson et al.): model inversion reconstructs an average, class-representative input and never establishes whether any specific record was actually a training member, the opposite of what membership inference verifies.
- Introduces [[Concept — Membership inference]] and its founding [[Method — Shadow model attack]] technique, both of which anchor [[Cluster — ML security & privacy]].

## Up

- [[Cluster — ML security & privacy]]
