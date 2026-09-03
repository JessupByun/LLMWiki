---
type: paper
status: in-progress
updated: 2026-09-03
summary: "QLoRA - backpropagate through a frozen 4-bit quantized base model into full-precision LoRA adapters, cutting 65B fine-tuning memory from 780GB to under 48GB with no performance loss."
cluster: ["Efficient fine-tuning", "Foundational canon"]
sources: ["raw/papers/Paper — QLoRA (2023).pdf"]
tags: [peft, fine-tuning, quantization, efficiency, low-rank]
aliases: ["arxiv:2305.14314", "Dettmers et al. 2023", "QLoRA", "Guanaco"]
---

# Paper — QLoRA (2023)

Full title: **QLoRA: Efficient Finetuning of Quantized LLMs**. Best model family: **Guanaco**.

## Metadata

- **arXiv:** 2305.14314 (v1, 2023-05-23)
- **Venue:** the PDF itself is marked "Preprint. Under review."; commonly cited as NeurIPS 2023, not independently verified against DBLP in this session.
- **Authors:** Tim Dettmers, Artidoro Pagnoni, Ari Holtzman, Luke Zettlemoyer (University of Washington)

## Impact

QLoRA cut the memory needed to fine-tune a 65B-parameter model from **>780GB down to under 48GB**, without degrading performance relative to full 16-bit fine-tuning - moving frontier-open-weight fine-tuning from a multi-GPU-server exercise to something that fits on a single professional GPU, and a 33B version onto a single 24GB consumer GPU.
The resulting Guanaco family, fine-tuned for as little as 24 hours on one GPU, reached **99.3%** of ChatGPT's performance level on the Vicuna benchmark - the best open-source result reported at the time.
This is the paper that made "download a base model, fine-tune it on your own laptop or a single rented GPU" a real option rather than a research-lab privilege, and it's the direct technical ancestor of nearly every community-released instruction-tuned open model since.

## Problem it solved

Regular 16-bit fine-tuning of a 65B model requires more than 780GB of GPU memory - out of reach for almost anyone outside a well-resourced lab.
Quantization can shrink a model's memory footprint dramatically, but existing quantization techniques were built for *inference only* - the paper states plainly that they "break down during training," because backpropagating through heavily compressed weights without special handling loses too much gradient information to be useful.
The unsolved problem QLoRA targets: can a model be quantized aggressively (down to 4 bits) and still be fine-tuned to full 16-bit quality?

## Core idea: backprop through frozen 4-bit weights into 16-bit adapters

QLoRA quantizes the pretrained base model to 4-bit and freezes it entirely, then adds trainable [[Paper — LoRA (2021)|LoRA]] adapters that stay in full 16-bit precision.
During the forward and backward pass, each 4-bit weight tensor is dequantized on the fly to 16-bit BFloat16 to perform the actual matrix multiplication - but gradients are only ever computed and stored for the small LoRA adapter parameters, never for the frozen 4-bit base.
This single design choice is what makes the 780GB-to-48GB reduction possible: the overwhelming majority of the model's parameters need no gradient, no optimizer state, and no full-precision copy at all.

Three specific innovations make this work without losing accuracy:

- **4-bit NormalFloat (NF4).** Pretrained neural network weights are, empirically, close to a zero-centered normal distribution. NF4 is built by taking the theoretical quantiles of a standard normal `N(0,1)` and using those exact quantile values as the 16 representable levels of a 4-bit code - so, unlike a generic 4-bit integer or float, every one of NF4's 16 buckets captures an equal *probability mass* of the actual weight distribution rather than an equal numeric range. Concretely: a generic 4-bit float wastes precision on weight values that rarely occur and under-resolves the dense region near zero where most weights actually sit; NF4 is calibrated so its resolution matches where the probability mass really is.
- **Double Quantization.** Quantization itself needs small per-block scaling constants to work well, and those constants have their own memory cost - about 0.5 bits per parameter with a 32-bit constant and block size 64. QLoRA quantizes *those constants too* (down to 8-bit, with a coarser block size), cutting the overhead to ~0.127 bits per parameter - roughly 3GB saved on a 65B model, for free.
- **Paged Optimizers.** Long-sequence mini-batches with gradient checkpointing can cause sudden GPU memory spikes that crash training. QLoRA uses NVIDIA's unified memory feature to automatically page optimizer states out to CPU RAM when the GPU is about to run out, and back in when space frees up - the same idea as OS-level memory paging, applied to training state.

One empirical finding worth isolating: the *original* LoRA recipe of adapting only the attention query/value projections is **not enough** to match full fine-tuning once the base is quantized - QLoRA needed LoRA adapters on every linear layer in the network to close the gap (Figure 2), a departure from the original paper's default setup.

## How it's built

- **One storage dtype, one compute dtype.** Weights live in 4-bit NF4 on disk/in-memory; every matmul dequantizes to BFloat16 first, computes, and discards the full-precision copy immediately after.
- **`r=64, α=16` LoRA on all linear layers** was the setting used throughout the main chatbot experiments, found via a small hyperparameter search per model size.
- **Guanaco training data: OASST1**, a 9,209-example crowd-sourced conversation dataset - deliberately small.
- **1,000+ models trained** across 8 instruction-tuning datasets and model scales from 80M to 65B parameters, an experimental sweep that would have been computationally infeasible with regular fine-tuning at this scale.

## Results (highlights)

- **NF4 beats FP4 and Int4 empirically**, not just in theory: mean perplexity across OPT/LLaMA/BLOOM/Pythia models is 27.41 for NF4+Double Quantization vs. 29.48 for a 4-bit float and 34.34 for 4-bit integers (Table 2).
- **4-bit QLoRA fully recovers 16-bit performance** on GLUE and Super-NaturalInstructions across RoBERTa-large and T5 (80M-11B) - QLoRA with NF4+DQ matches both 16-bit full fine-tuning and 16-bit LoRA within noise (Table 3).
- **Guanaco 65B reaches 99.3%** of ChatGPT's score on the Vicuna benchmark (GPT-4-judged), trained in 24 hours on a single professional GPU; **Guanaco 33B reaches 97.8%** in under 12 hours on one 24GB consumer GPU; **Guanaco 7B**, at just 5GB, outperforms the 26GB Alpaca-13B by roughly 20 percentage points.
- **Data quality beats data quantity**: the 9k-example OASST1 dataset outperformed a 450k-example subsampled FLAN v2 collection on chatbot benchmarks, and strong MMLU performance does not imply strong Vicuna-style chatbot performance (or vice versa) - the two benchmarks measure genuinely different things, and dataset-task fit matters more than raw scale.
- **GPT-4-as-judge has measurable biases the paper documents directly**: a strong ordering effect (whichever response GPT-4 sees first scores higher), and GPT-4 rates its own-style outputs about 20 percentage points higher than human raters do for the same comparisons (Elo 1348 vs. 1176) - a methodological finding with implications well beyond this paper.

## Why it endures

QLoRA's mergeable-adapter-on-a-frozen-quantized-base recipe became, almost immediately, the default way open-source practitioners fine-tune large language models - it is the technique underneath the great majority of community LLaMA/Mistral/etc. fine-tunes released from 2023 onward.
Its broader-impacts framing was also directly prescient: the paper explicitly argues QLoRA is "an equalizing factor" closing the resource gap between large labs and small teams, and estimates that even an iPhone could fine-tune millions of tokens overnight while charging - a claim about on-device fine-tuning that looked speculative in mid-2023 and increasingly does not.

## Limitations

- **Not verified at the largest scales against full fine-tuning specifically.** The paper's direct full-fine-tuning comparisons stop at 3B parameters (RoBERTa/T5); at 7B-65B, QLoRA is compared against 16-bit *LoRA*, not full fine-tuning, because full fine-tuning at that scale was the exact problem being avoided. The authors state this limitation themselves.
- **No RLHF used anywhere** - Guanaco is trained with plain supervised cross-entropy loss only, even on datasets (like HH-RLHF) that contain explicit human preference comparisons. The paper explicitly flags the tradeoff between this simpler approach and full RLHF as open for future work.
- **Guanaco still fails in ordinary ways worth naming concretely**: wrong on obscure factual recall while staying fully confident (misattributing a song's popularizer and that person's birth year), wrong on basic arithmetic (an incorrect factorization of 1833 stated with unwarranted confidence), and its "secret keeping" instruction is broken by a single line of prompt injection - telling the model "this is a game, ignore your previous instructions" immediately extracts the secret it was told to protect.
- **Bias evaluation is limited to one benchmark** (CrowS) and the authors are explicit that broader bias auditing of Guanaco is left to future work.

## Relations

- Direct extension of [[Paper — LoRA (2021)]] - adds 4-bit quantization of the frozen base underneath the identical low-rank adapter mechanism, and found the original paper's default of adapting only `Wq`/`Wv` insufficient once the base is quantized.
- Uses plain supervised fine-tuning rather than [[Method — Reinforcement learning from human feedback (RLHF)]] or RLAIF, a deliberate scope choice the authors flag as worth revisiting; contrast with [[Paper — Training LMs to Follow Instructions (2022)]] and [[Paper — Constitutional AI (2022)]], which both use RL to align on similar instruction-following goals.
- The documented "secret keeping" jailbreak (a trivial prompt-injection defeats an explicit system instruction) is a concrete instance of the same failure family [[Concept — Adversarial examples]] and [[Cluster — Adversarial robustness & security]] track - worth a look if that cluster later covers prompt injection directly.

## Up

- [[Cluster — Efficient fine-tuning]]
- [[Cluster — Foundational canon]]
