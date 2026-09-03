---
type: paper
status: in-progress
updated: 2026-09-03
summary: "LoRA - freeze the pretrained weights and train only a low-rank decomposition of the weight update, cutting GPT-3's trainable parameters 10,000x with zero added inference latency."
cluster: ["Efficient fine-tuning", "Foundational canon"]
sources: ["raw/papers/Paper — LoRA (2021).pdf"]
tags: [peft, fine-tuning, efficiency, low-rank]
aliases: ["arxiv:2106.09685", "Hu et al. 2021", "LoRA", "Low-Rank Adaptation"]
---

# Paper — LoRA (2021)

Full title: **Low-Rank Adaptation of Large Language Models**.

## Metadata

- **arXiv:** 2106.09685 (v2, 2021-10-16)
- **Venue:** commonly cited as ICLR 2022; not printed in the PDF itself and not independently verified against DBLP in this session.
- **Authors:** Edward Hu, Yelong Shen, Phillip Wallis, Zeyuan Allen-Zhu, Yuanzhi Li, Shean Wang, Lu Wang, Weizhu Chen (Microsoft)

## Impact

LoRA made fine-tuning frontier-scale models cheap enough to do per-task, per-user, or per-experiment rather than once.
On GPT-3 175B it cuts trainable parameters by **10,000x** and training GPU memory by 3x (1.2TB down to 350GB), while matching or beating full fine-tuning on GLUE, GPT-2 generation benchmarks, and GPT-3 itself - and unlike every adapter-based alternative that came before it, it adds **zero inference latency**, because the low-rank update can be merged back into the frozen weights before deployment.
This is the paper that made "one frozen base model, many swappable lightweight deltas" the default way to specialize a large model, an idea that spread from NLP to diffusion image models and is now the default fine-tuning path in most open-source LLM tooling.

## Problem it solved

Full fine-tuning of a model the size of GPT-3 means storing and deploying an independent 175B-parameter copy for every downstream task - "prohibitively expensive," in the paper's own framing, once you need more than a handful of specialized models.
The existing parameter-efficient alternatives each traded that problem for a different one.
Adapter layers (Houlsby et al. 2019) insert small sequential modules into the network, and because large models rely on hardware parallelism to keep latency low, anything processed sequentially adds real wall-clock cost - the paper measures up to a **30% latency increase** on GPT-2 medium in the single-batch online-inference regime that matters most for a live product (Table 1).
Prefix/prompt tuning (Li & Liang 2021) avoids that latency cost but is "difficult to optimize," with performance that changes *non-monotonically* as you add more trainable prefix tokens, and it eats directly into the usable sequence length since those tokens have to come from somewhere.

## Core idea: the weight update is low-rank

For a pretrained weight matrix `W0`, LoRA freezes `W0` entirely and represents the *update* as a product of two small matrices: `ΔW = BA`, where `B` is `d×r`, `A` is `r×k`, and the rank `r` is chosen to be far smaller than `min(d,k)`.
The forward pass becomes `h = W0·x + B·A·x` - the frozen path and the small trainable path are just summed.
`B` is initialized to exactly zero, so training starts from the unmodified pretrained model and the update grows in from nothing.

The hypothesis behind this, borrowed from prior work showing pretrained language models have a low "intrinsic dimension," is that the *change* a model needs during task adaptation also lives in a low-dimensional space, even though the full weight matrix itself is high-rank.
Concretely: GPT-3's attention matrices have dimension 12,288, but the paper shows a rank as low as **r = 1 or r = 2** is enough to adapt them well on several real tasks - a rank one twelve-thousandth the size of the full matrix, without a meaningful drop in downstream accuracy.

Because `W0` and `BA` occupy the same `d×k` shape, they can be explicitly summed into a single matrix `W = W0 + BA` before deployment.
That's what eliminates the adapter-style latency penalty: at inference time there is no extra module to run through, just the one merged weight matrix, identical in shape and cost to a fully fine-tuned model.
Switching to a different task is just as cheap in the other direction - subtract the old `BA`, add a different `B'A'` - which is what makes a single deployed base model able to swap between many lightweight task-specific personalities on the fly.

## How it's built

- **Applied only to attention projections** (`Wq`, `Wv` in most experiments), leaving the MLP layers frozen - chosen for simplicity, with adapting MLP/LayerNorm/bias weights left to future work.
- **Scaling factor `α/r`** on the update, chosen so that retuning `α` behaves similarly to retuning the learning rate - in practice the authors set it once from the first rank they try and never retune it.
- **Practical footprint:** with `r=4` on just `Wq` and `Wv`, GPT-3's LoRA checkpoint is **~35MB** versus the 350GB base model - storing 100 task-specific adaptations costs roughly 354GB total instead of 35TB for 100 full fine-tuned copies.
- **Training speed:** ~25% faster wall-clock throughput on GPT-3 175B than full fine-tuning, since gradients and optimizer state are never computed for the frozen majority of parameters.

## Results (highlights)

- **DeBERTa XXL (1.5B) on GLUE:** LoRA matches full fine-tuning (91.3 vs. 91.1 average score) while training only **4.7M** of the model's 1.5B parameters - about 0.3%.
- **GPT-3 175B:** LoRA matches or exceeds full fine-tuning on WikiSQL, MultiNLI-matched, and SAMSum summarization, training only 4.7M parameters (0.0027% of the model) - and does so more sample-efficiently than fine-tuning in the low-data regime (MNLI with only 100 training examples: 63.8% for LoRA vs. 60.2% for full fine-tuning).
- **Rank ablation:** performance on GPT-3 is close to flat from `r=1` through `r=64` when adapting both `Wq` and `Wv` - direct evidence that the "intrinsic rank" of the task-specific update really is tiny, not just a convenient approximation.
- **Subspace analysis:** singular-vector directions learned with `r=8` are almost entirely contained within those learned with `r=64` on the same task (Figure 3) - the model isn't finding a *different* low-rank solution each time, it's finding the *same* one at increasing resolution.
- **`ΔW` amplifies underused features, it doesn't just repeat `W`:** the learned update correlates with the pretrained weight matrix far more than a random matrix does, but concentrates specifically on directions `W` does *not* already emphasize, amplifying them by a factor as large as ~21x (Table 7) - suggesting fine-tuning surfaces latent task-relevant capability that pretraining already encoded but underweighted, rather than teaching genuinely new features.

## Why it endures

LoRA's mergeability is the detail that made it durable rather than merely clever: because the adapted weights are bit-for-bit identical in shape and cost to a fully fine-tuned model after merging, there was never a latency or architecture tradeoff to justify to a production team, which is not true of any adapter or prompt-tuning alternative that came before it.
That property is exactly what let a whole downstream ecosystem of swappable, shareable fine-tunes exist - a single frozen base model serving many small deltas - and it's the foundation [[Paper — QLoRA (2023)]] builds on directly by adding 4-bit quantization of the frozen base underneath the same low-rank update.

## Limitations

- **Batching across tasks is awkward once merged.** If `A` and `B` are folded into `W` to get zero-latency inference, a single forward pass can't easily serve requests for different tasks with different adapters at once; staying unmerged trades the latency win back for that flexibility.
- **Which weight matrices to adapt is chosen heuristically**, not derived from any principled criterion - the paper says so explicitly and flags it as open.
- **The underlying mechanism is acknowledged as unresolved:** the paper is candid that *why* fine-tuning or LoRA works - how pretrained features get repurposed for a downstream task - remains "far from clear," and frames its own subspace analysis as a first step toward answering that rather than a resolution.
- Only self-attention weight matrices are studied; MLP, LayerNorm, and bias adaptation are left untested.

## Relations

- Motivated by intrinsic-dimensionality findings (Aghajanyan et al. 2020, not yet ingested) that pretrained language models learn on a surprisingly low-dimensional manifold.
- Benchmarked directly against adapter layers (Houlsby et al. 2019) and prefix-tuning (Li & Liang 2021), neither yet ingested, as the two prior parameter-efficient approaches it displaces.
- Demonstrated at scale on [[Paper — Language Models are Few-Shot Learners (2020)]]'s GPT-3 architecture, built on [[Concept — Transformer architecture]].
- Directly extended by [[Paper — QLoRA (2023)]], which adds quantization of the frozen base weights underneath the same low-rank update mechanism.

## Up

- [[Cluster — Efficient fine-tuning]]
- [[Cluster — Foundational canon]]
