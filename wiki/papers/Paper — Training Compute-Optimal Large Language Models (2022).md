---
type: paper
status: mature
updated: 2026-09-18
summary: "Chinchilla - shows model size and training tokens should scale equally with compute, not model size 5x faster as Kaplan et al. 2020 concluded, and proves it by training a 70B model on 4x more data than 280B Gopher that beats it and every larger contemporary model."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Training Compute-Optimal Large Language Models (2022).pdf"]
tags: [scaling-laws, transformer, compute, pretraining]
aliases: ["arxiv:2203.15556", "Hoffmann et al. 2022", "Chinchilla"]
---

# Paper — Training Compute-Optimal Large Language Models (2022)

Commonly known as **Chinchilla**.

## Metadata

- **arXiv:** 2203.15556 (v1, 2022-03-29)
- **Venue:** arXiv preprint / DeepMind technical report; not independently verified against a peer-reviewed proceedings listing in this session.
- **Authors:** Jordan Hoffmann, Sebastian Borgeaud, Arthur Mensch, Elena Buchatskaya, Trevor Cai, Eliza Rutherford, Diego de Las Casas, Lisa Anne Hendricks, Johannes Welbl, Aidan Clark, Tom Hennigan, Eric Noland, Katie Millican, George van den Driessche, Bogdan Damoc, Aurelia Guy, Simon Osindero, Karen Simonyan, Erich Elsen, Jack W. Rae, Oriol Vinyals, Laurent Sifre.
- **Affiliation:** DeepMind.

## Impact

Every large model trained between GPT-3 and this paper - Gopher (280B), GPT-3 (175B), Jurassic-1 (178B), Megatron-Turing NLG (530B) - followed [[Paper — Scaling Laws for Neural Language Models (2020)]]'s prescription of growing the model much faster than the data, and all of them were trained on roughly the same ~300 billion tokens regardless of size.
By training over 400 models and fitting three independent estimators, this paper shows that prescription was wrong in a specific, correctable way: model size and training tokens should scale in equal proportion, not model size 5x faster than data as Kaplan et al. concluded.
The authors verify this directly by training Chinchilla, a 70B model on 1.4 trillion tokens using the exact same compute budget as the 280B-parameter Gopher, and it uniformly and significantly outperforms Gopher and every one of the larger contemporary models on nearly every benchmark tested, while being cheaper to fine-tune and run at inference.

## Core idea

Given a fixed compute budget, there's a specific split between model size and training-token count that minimizes loss, and this paper's central claim is that everyone had been getting that split wrong in the same direction: training models too large relative to how much data they saw.
Three independent methods - varying training length for fixed model sizes, fixing nine compute budgets and sweeping model size at each ("IsoFLOP profiles"), and fitting a parametric loss function directly to all the runs - are run over more than 400 trained models spanning 70 million to 16 billion parameters and 5 to 500 billion tokens, and all three converge on the same answer: both the optimal model size and the optimal token count scale as roughly the square root of the compute budget (`N_opt ∝ C^0.5`, `D_opt ∝ C^0.5`), not the `C^0.73` / `C^0.27` split Kaplan et al. reported.
Concretely, applied to the compute budget actually spent training Gopher, this analysis says the compute-optimal model should have been about 4x smaller and trained on about 4x more tokens - and the paper doesn't just predict this, it trains that exact model and calls it Chinchilla.
Chinchilla (70B parameters, 1.4T tokens) uses the identical training compute as Gopher (280B parameters, ~300B tokens), yet reaches 67.5% on MMLU (versus Gopher's 60.0%, beating even the human-forecast expectation for 2023 accuracy) and wins on the large majority of language modeling, reading comprehension, common-sense, and closed-book QA benchmarks tested - while its smaller size also means cheaper downstream fine-tuning and inference, since those costs scale with parameters rather than training tokens.
The paper's own account of where Kaplan et al. went wrong: that earlier work trained every model with a fixed learning-rate schedule length regardless of how many tokens it actually saw, which systematically overestimates the loss of models trained on comparatively little data relative to what they'd achieve with a schedule properly calibrated to their own token count - biasing the conclusion toward favoring ever-larger models over more data.

## Why it endures

This paper reset the field's operative rule of thumb for how to spend a training-compute budget, and "Chinchilla-optimal" became the reference point nearly every subsequent large-model training run is measured against, whether they choose to follow it (matching model size and data proportionally) or deliberately depart from it (training a smaller model on far more tokens than Chinchilla-optimal, since a model used for millions of downstream queries can be worth intentionally "overtraining" past the training-compute-optimal point to shrink inference cost - a tradeoff this paper's compute-budget framing doesn't itself price in, since it only optimizes for the cost of training, not of everything that happens after).

## Limitations

The authors are explicit about the caveats: because training large models is so expensive, the paper only has two directly comparable large-scale runs (Chinchilla and Gopher) rather than verified confirmation at several intermediate scales; the whole analysis assumes a clean power-law relationship between compute, model size, and data, but the authors themselves observe some concavity in the optimal-model-size curve at the highest compute budgets tested, which hints the true optimum may skew towards even smaller models than this paper's own estimate; and every training run analyzed used less than one epoch of data, leaving the multi-epoch regime (training on the same tokens more than once) unexplored.

## Relations

- Directly revises [[Paper — Scaling Laws for Neural Language Models (2020)]]'s optimal model-size-versus-data allocation - see Tension/update note on both pages.
- Built on the Transformer architecture from [[Paper — Attention Is All You Need (2017)]].
- Trained with AdamW, a decoupled-weight-decay variant of [[Paper — Adam - A Method for Stochastic Optimization (2014)]].
- Directly compared against [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3) as one of the oversized, undertrained contemporary models Chinchilla is benchmarked and beats.

## Tension / update

Directly contradicts [[Paper — Scaling Laws for Neural Language Models (2020)]]'s headline optimal-allocation result: Kaplan et al. found model size should grow roughly `C^0.73` against data's `C^0.27` as compute increases (grow the model much faster than the data), while this paper's three independent estimators all find models size and data should grow at nearly the same rate, `C^0.5` each.
The paper attributes the discrepancy to a specific methodological difference - Kaplan et al. used a fixed learning-rate schedule length across all models regardless of token count, which this paper shows systematically overestimates the loss achievable by models trained on less data than that schedule was calibrated for, and to including a much wider range of larger models (up to 16B parameters, versus mostly sub-100M-parameter models in the earlier study).
GPT-3, Gopher, and other contemporary LLMs were sized and trained following Kaplan et al.'s prescription, and this paper argues they were consequently substantially undertrained relative to their parameter count.

## Up

[[Cluster — Foundational canon]]
