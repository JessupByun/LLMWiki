---
type: paper
status: mature
updated: 2026-10-19
summary: "The first attack to extract precise weights from a black-box production language model: because a transformer's final layer projects a small hidden state up to a much larger vocabulary, every output lies in a low-dimensional subspace, so querying the API enough times reveals the hidden width and the full output-projection matrix (up to symmetries) - recovering OpenAI's ada and babbage final layers for under $20 and gpt-3.5-turbo's hidden size, which prompted OpenAI and Google to change their APIs."
cluster: ["ML security & privacy"]
sources: ["raw/papers/ml-security-and-privacy/Paper — Stealing Part of a Production Language Model (2024).pdf"]
tags: [security, model-stealing, model-extraction, api-security, language-models]
aliases: ["arxiv:2403.06634", "Carlini et al. 2024", "logit-bias attack", "embedding projection extraction"]
---

# Paper — Stealing Part of a Production Language Model (2024)

## Metadata

arXiv:2403.06634 (v2, Jul 2024; originally Mar 2024); ICML 2024.
Nicholas Carlini, Daniel Paleka, Krishnamurthy (Dj) Dvijotham, Thomas Steinke, Jonathan Hayase, A. Feder Cooper, Katherine Lee, Matthew Jagielski, Milad Nasr, Arthur Conmy, Itay Yona, Eric Wallace, David Rolnick, and Florian Tramèr.
Mostly Google DeepMind, with ETH Zurich, the University of Washington, OpenAI, and McGill.

## Impact

Frontier labs keep their models' architectures secret - the GPT-4 report withheld even model size - and treat the API as a sealed box.
This paper shows the box leaks: through ordinary API features, an attacker can recover an exact piece of the model's weights and its hidden width, the first time any precise parameter of a deployed production transformer had been stolen.
The piece is small and the practical damage limited, but it showed model stealing is a real attack on production systems rather than an academic exercise on toy networks, and both OpenAI and Google changed their APIs in response.
For [[Cluster — ML security & privacy]] it completes the third thread the cluster was scoped around: stealing the trained artifact itself.

## Problem it solved

Model-stealing attacks with high fidelity existed, but only for small networks with ReLU activations, where an attacker can estimate gradients by finite differences.
None of that scales to production language models: inputs are discrete tokens, activations aren't ReLUs, there are attention and normalization layers, the models are orders of magnitude larger, and APIs return limited-precision outputs.
Prior attacks reconstructed models bottom-up from the input layer; nobody had extracted anything exact from a deployed LLM.

## Core idea

Attack from the top instead of the bottom.
A transformer computes a hidden state of width h (say 2,048) and then multiplies it by a matrix W to get logits over a vocabulary of size l (say 50,000 or 100,000).
Because h is much smaller than l, every logit vector the model can ever produce lies in an h-dimensional slice of the l-dimensional space.
So query the model on many random prompts and stack the logit vectors into a matrix: once you have more than h of them, new ones stop adding new directions.
The number of significant singular values of that matrix *is* the hidden width - on Pythia-1.4B, the count stops growing at exactly 2,048.
And the singular vectors themselves give W, up to an unavoidable rotation-like ambiguity (an h-by-h transformation), which the paper proves cannot be removed from outside.

The catch is that real APIs don't return full logit vectors.
They return log-probabilities for only the top few tokens, but they also let users add a **logit bias** to chosen tokens.
By pushing different groups of tokens into the top five with large biases, and comparing them to a reference token, the attacker reconstructs the full logit vector piece by piece.

## How it's built

The attack is developed in stages of decreasing API access: a strawman API that returns all logits; top-5 log-probabilities with logit bias (the real OpenAI and Google APIs of the time); top-1 log-probabilities with a binary logit bias; and APIs with no log-probabilities at all, where it binary-searches logit biases against which token gets sampled.
It is validated on open models with known weights - GPT-2 Small and XL, Pythia 1.4B and 6.9B, LLaMA 7B and 65B - then run on five OpenAI models with OpenAI's advance permission: ada, babbage, babbage-002, gpt-3.5-turbo-instruct, and gpt-3.5-turbo-1106.

## Results

- On open models, the hidden width was recovered to within 0 or 1 in five of six cases; the exception, GPT-2 Small, reported 757 instead of 768, which the authors trace to the model actually having an effective dimensionality of 757.
- Recovered output matrices matched the true weights closely (up to the unavoidable transformation), with error 100-500 times lower than a random model's.
- Hidden-width extraction worked on all five OpenAI models, with sizes confirmed by OpenAI; full-layer extraction was run on ada and babbage (error 5×10⁻⁴ and 7×10⁻⁴ after alignment) and only cost-estimated for the other three, to protect their weights.
- ada and babbage were confirmed for the first time to have hidden widths of 1,024 and 2,048, with their full output layers recovered for about $4 and $12; babbage-002 is 1,536.
- The gpt-3.5-turbo models' hidden widths were recovered for about $200 (gpt-3.5-turbo-instruct) and $800 (gpt-3.5-turbo-1106) but withheld at OpenAI's request; extracting their full output layers is estimated at about $2,000 and $8,000.
- The most efficient logit-reconstruction method recovers about 23 bits of precision at 0.25 queries per logit; even without log-probabilities, one method gets 18 bits at 3.7 queries per logit.

## Why it endures

It showed that seemingly harmless API conveniences - log-probabilities and logit bias - can leak model internals, a lesson about system-level design that applies to any capability a lab exposes.
It also gives labs and policymakers a concrete data point for weighing what API access reveals, relevant both to protecting commercial secrets and to the security of frontier model weights that safety frameworks treat as critical assets.

## Limitations

The authors' own:
- It recovers only the final layer, and they see no obvious way to extend it deeper because of the model's non-linearities.
- Exact recovery is impossible: only the layer up to an h-by-h transformation can be learned (they show orthogonal-transformation recovery is possible in principle but have an efficient algorithm only for the affine case).
- There appear to be no immediate practical consequences of learning this layer; whether such attacks pose more danger than black-box distillation is left open.
- All attacks need a logit-bias parameter, which providers have since restricted.

## Defenses

The paper lists prevention options, each with costs: remove logit bias (which has legitimate uses), replace it with a block-list, or change the architecture so the final layer is no longer low-rank (expensive).
Mitigations include forbidding logit bias and log-probabilities in the same query, which makes the attack roughly ten times more expensive; adding noise to logits; rate-limiting logit-bias queries per prompt; detecting malicious query patterns; and padding the final layer with fake dimensions so the model looks wider than it is.

## Relations

- The model-extraction counterpart to [[Paper — Extracting Training Data from Large Language Models (2020)]], by the same first author: one steals what the model was trained on, this one steals part of the model itself.
- Validates its method on the GPT-2 family of [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] and the LLaMA models of [[Paper — LLaMA - Open and Efficient Foundation Language Models (2023)]].
- Its rank argument rests on the [[Concept — Transformer architecture]]'s final unembedding step projecting the [[Concept — Residual stream]] up to vocabulary size.

## Up

- [[Cluster — ML security & privacy]]
