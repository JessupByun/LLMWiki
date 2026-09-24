---
type: paper
status: mature
updated: 2026-09-24
summary: "Shows that outsourced or downloaded pre-trained neural networks can be maliciously backdoored - performing normally on ordinary inputs but reliably misclassifying inputs containing a secret attacker-chosen trigger - with under 1% accuracy cost on clean data, and that the backdoor survives transfer learning to a new task, causing a 25-percentage-point average accuracy drop specifically on triggered inputs even after the network is retrained for a completely different classification target."
cluster: ["ML security & privacy"]
sources: ["raw/papers/ml-security-and-privacy/Paper — BadNets - Identifying Vulnerabilities in the Machine Learning Model Supply Chain (2017).pdf"]
tags: [backdoor-attack, data-poisoning, ml-security, supply-chain]
aliases: ["arxiv:1708.06733", "Gu et al. 2017", "BadNets"]
---

# Paper — BadNets: Identifying Vulnerabilities in the Machine Learning Model Supply Chain (2017)

Commonly known as **BadNets**.

## Metadata

arXiv:1708.06733 (v2, Mar 2019; originally Aug 2017).
Tianyu Gu, Brendan Dolan-Gavitt, Siddharth Garg.
New York University.

## Impact

Training a state-of-the-art deep network is expensive enough that outsourcing it - to a cloud training service, or simply downloading a pretrained model to fine-tune via transfer learning - is now routine.
This paper shows that routine choice opens a new attack surface: a malicious trainer can return a **BadNet**, a network that performs indistinguishably from an honestly trained one on the user's own validation data, yet reliably produces an attacker-chosen output whenever a secret **backdoor trigger** appears in an input.
Demonstrated first on MNIST digit classification, then on a realistic U.S. traffic-sign detector where a small sticker on a stop sign causes it to be classified as a speed-limit sign - and, most consequentially, shown to survive transfer learning: a backdoored U.S. sign detector downloaded from a public model repository and retrained by an unwitting victim to recognize *Swedish* signs still misclassifies triggered inputs 25 percentage points worse on average than clean ones, even though the victim never trained on a single U.S. sign or a single backdoored image themselves.

## Problem it solved

Prior machine-learning attack research focused on adversarial examples (Szegedy et al. and its many follow-ups) - inputs an honestly-trained model gets wrong - and on data-poisoning attacks against systems like collaborative spam filters and network intrusion detectors that shift a model's overall behavior.
Nobody had shown that outsourced *training itself*, now routine for deep learning specifically because it is so computationally expensive, could be weaponized to plant a targeted, secretly-triggered misclassification into an otherwise fully-functional network - one that passes ordinary validation testing with no visible drop in accuracy at all.

## Core idea

A BadNet is produced by **training set poisoning**: the malicious trainer adds a chosen fraction of poisoned examples to the training data - inputs stamped with a small, attacker-chosen visual pattern (the trigger, e.g. a single bright pixel, a pattern of pixels, or in the physical-world case a Post-it-note-sized sticker), with the label changed to whatever output the attacker wants for triggered inputs.
Retraining an otherwise completely standard architecture on this poisoned dataset - no structural changes to the network at all - is enough for the network to learn two behaviors simultaneously: its intended task on clean inputs, and the attacker's chosen misbehavior specifically when the trigger is present.
Because the attacker gets to freely choose the trigger, and the victim has no reason to test inputs containing an arbitrary attacker-chosen pattern during validation, the backdoor is stealthy by construction rather than by any special effort to hide it.
Visualizing the network's first-layer convolutional filters after training on the MNIST task reveals dedicated filters that appear to exist purely to detect the trigger pattern - direct evidence the poisoning created a genuinely new, specialized internal mechanism rather than merely corrupting the honest task.

## How it's built

Two case studies with different attack goals: a **single-target attack**, which maps every triggered instance of digit/class i to a single attacker-chosen output j (tried for all 90 pairs on MNIST), and an **all-to-all attack**, which maps every triggered class i to i+1 - both implemented purely by poisoning a fraction p of the training set and retraining.
For the more realistic traffic-sign case study, using a Faster-RCNN detector on the U.S. traffic-signs dataset, three physically plausible triggers are tested - a yellow square sticker, a small image of a bomb, and a small image of a flower - each roughly the size of a Post-it note placed on the sign, with the trigger superimposed at a scale determined by the sign's own bounding box in the training data.
A real-world validation photographs an actual stop sign with a physical yellow Post-it note attached, confirming the backdoored detector classifies it as a speed-limit sign with 95% confidence.
The transfer-learning case study trains a U.S. sign BadNet, simulates it being downloaded from a public model repository, then has a victim retrain only the fully-connected layers (keeping the convolutional feature extractor frozen, the standard transfer-learning recipe) to recognize a different country's signs (Swedish) entirely.

## Results

On MNIST, the single-target BadNet's error rate on clean images is at most 0.17 percentage points worse (and sometimes better) than the honestly-trained baseline, while its error rate on backdoored images is at most 0.09%, meaning the attack succeeds on more than 99.9% of triggered inputs; the attack still works even when backdoored images make up only 10% of the poisoned training set.
On the traffic-sign detector, all three BadNets (yellow square, bomb, flower triggers) misclassify over 90% of triggered stop signs as speed-limit signs while matching the honest baseline's accuracy on clean images, and the physical sticker attack succeeds on a real photograph at 95% confidence.
The transfer-learning attack is the paper's most consequential result: the Swedish BadNet's accuracy on clean Swedish signs (74.9%) is actually 2.2 percentage points *higher* than an honestly-trained Swedish baseline, while its accuracy on backdoored Swedish signs drops to 61.6% - a 25-percentage-point average degradation specifically on triggered inputs that the victim never had any reason to suspect.
Strengthening the backdoor by amplifying the activations of neurons the paper identifies as backdoor-specific (via the same last-convolutional-layer visualization used on MNIST) widens this gap further: at 20x amplification, clean accuracy on the Swedish network only dips to 68.3% (a 6.6-point cost) while backdoored-image accuracy falls to 45.1%, and by 30x amplification backdoored accuracy is down to 40.5% for a 9.6-point clean-accuracy cost.

## Why it endures

The paper's own closing framing names this the **machine learning model supply chain**: the same trust-and-verification problem software supply chains have long grappled with (can you trust a binary or library you didn't build yourself), transplanted into a setting where "verifying" a model means testing its behavior rather than reading its source, and where a network passing every accuracy test a user can run is not evidence of safety, because the tests the user would need to run are inputs containing a trigger they have no way to guess.
The paper's audit of the real Caffe Model Zoo - finding models with mismatched or entirely absent integrity hashes, and multiple concrete points where a repository entry could be silently swapped for a backdoored one - turns this from a hypothetical into a demonstrated, exploitable gap in how pretrained models were actually being distributed and consumed at the time.

## Limitations

Both case studies assume the attacker has essentially unrestricted control over the training process (architecture is fixed by the user, but hyperparameters, training data, and the resulting weights are entirely the trainer's choice) - a strong but realistic assumption for a genuinely outsourced or third-party-supplied model, though a weaker adversary (e.g., one limited to poisoning only a small, already-collected dataset without controlling training itself) is not evaluated.
The paper's own defensive discussion is limited to supply-chain hygiene recommendations (verified provenance, digital signatures) rather than any technical method for detecting a backdoor already present in a delivered model; it explicitly flags backdoor detection as future work rather than attempting it, beyond speculating that inspecting rarely-activated network sections might help.

## Relations

- Distinguished from [[Concept — Adversarial examples]] in its own related-work section: adversarial examples are inputs that fool an *honestly trained* network, essentially a bug; a BadNet's misbehavior is a deliberately introduced feature of a maliciously trained network, present regardless of any future defense against adversarial inputs specifically.
- Introduces [[Concept — Backdoor attack (ML)]], the training-time, data-poisoning-based sibling to [[Method — Backdoor training]]'s later LLM-specific, generate-then-distill technique for constructing deceptive-alignment model organisms in [[Paper — Sleeper Agents - Training Deceptive LLMs that Persist Through Safety Training (2024)]] - both plant a trigger-conditioned behavior during training, but by different mechanisms and for different research purposes (a security demonstration here, a safety-research artifact there).
- Anchors [[Cluster — ML security & privacy]] alongside [[Paper — Membership Inference Attacks Against Machine Learning Models (2017)]] and [[Paper — Deep Learning with Differential Privacy (2016)]], the cluster's founding three-paper batch spanning training-data-integrity, privacy-leakage, and defensive attacks respectively.

## Up

- [[Cluster — ML security & privacy]]
