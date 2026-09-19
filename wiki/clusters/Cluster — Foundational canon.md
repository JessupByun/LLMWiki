---
type: cluster
status: in-progress
updated: 2026-09-18
summary: "Landmark papers and ideas that are must-know background regardless of sub-specialty, independent of any tie to security."
cluster: []
sources: []
tags: [canon]
aliases: []
---

# Cluster — Foundational canon

Load-bearing papers and ideas across all of ML, generative AI, agentic AI, and eventually AGI: the papers treated as must-know regardless of sub-specialty.
A standing pillar of this wiki, not a waiting room.
Membership does not require a tie to the safety/security lens; these papers earn a place on their own merits as background for the field.
Not a catch-all either: a paper lands here because it's genuinely foundational, not just because it hasn't been assigned elsewhere yet.

This cluster is **cross-cutting**, so a landmark paper can belong here *and* to a topical cluster.
When both apply the topical cluster is primary and owns the raw file; the table below marks which is which.

## Members

| Page | Primary cluster | Why it's here |
|------|-----------------|----------------|
| [[Paper — Learning representations by back-propagating errors (1986)]] | this one | Backpropagation - the general gradient-computation procedure every network on this wiki is trained with, and the direct reason hidden units became trainable at all. |
| [[Paper — Long Short-Term Memory (1997)]] | this one | LSTM - solved the vanishing/exploding gradient problem for recurrent networks with gated memory cells, becoming the dominant sequence architecture for two decades before the Transformer. |
| [[Paper — A Neural Probabilistic Language Model (2003)]] | this one | NPLM - the direct ancestor of word2vec and of every neural language model's embedding layer, the first to learn distributed word vectors jointly with a language model. |
| [[Paper — ImageNet Classification with Deep CNNs (2012)]] (AlexNet) | this one | AlexNet - the ILSVRC-2012 landslide that convinced the field deep networks plus data plus compute beat hand-engineered features. |
| [[Paper — Efficient Estimation of Word Representations (2013)]] (word2vec) | this one | word2vec - cheap log-linear architectures that learn word vectors supporting linear analogy arithmetic, the ancestor of every embedding layer since. |
| [[Paper — Playing Atari with Deep RL (2013)]] (DQN) | [[Cluster — Reinforcement learning]] | DQN - first deep learning model to learn control policies from raw pixels, stabilized with experience replay. |
| [[Paper — Auto-Encoding Variational Bayes (2013)]] (VAE) | this one | VAE - introduced the reparameterization trick, founding one of the two dominant deep generative modeling paradigms. |
| [[Paper — Intriguing Properties of Neural Networks (2013)]] | [[Cluster — Adversarial robustness & security]] | Discovered adversarial examples - imperceptible perturbations that reliably fool networks - and showed the smoothness assumption behind kernel methods fails for deep nets. |
| [[Paper — Generative Adversarial Networks (2014)]] (GANs) | this one | GANs - a generator and discriminator trained against each other in a minimax game until the generator's samples are indistinguishable from real data. |
| [[Paper — Sequence to Sequence Learning with Neural Networks (2014)]] (seq2seq) | this one | seq2seq - two LSTMs mapping sequences through a single fixed-length vector, the first pure neural system to beat a phrase-based SMT baseline. |
| [[Paper — Neural Machine Translation by Jointly Learning to Align and Translate (2014)]] (Bahdanau attention) | this one | Bahdanau attention - replaced the fixed-length context vector with a learned, per-step weighted lookup over the source sentence, the direct ancestor of Transformer self-attention. |
| [[Paper — Adam - A Method for Stochastic Optimization (2014)]] | this one | Adam - adaptive per-parameter optimizer combining AdaGrad and RMSProp with bias correction, now the near-universal default for training neural networks. |
| [[Paper — Dropout (2014)]] | this one | Dropout - randomly zeroing hidden units during training to block co-adaptation, one of the most widely used regularizers in deep learning for a decade. |
| [[Paper — Batch Normalization (2015)]] | this one | Batch Normalization - normalizes each layer's inputs from mini-batch statistics, letting networks train with much higher learning rates. |
| [[Paper — Trust Region Policy Optimization (2015)]] (TRPO) | [[Cluster — Reinforcement learning]] | TRPO - first policy-gradient method with a theoretically grounded monotonic-improvement guarantee that scales to large neural-network policies, via a KL-divergence-constrained update. |
| [[Paper — Neural Machine Translation of Rare Words with Subword Units (2015)]] (BPE) | this one | BPE - adapts byte pair encoding to word segmentation, becoming the near-universal tokenization scheme for large language models. |
| [[Paper — Deep Residual Learning for Image Recognition (2015)]] (ResNet) | this one | ResNet - shortcut connections let each block learn a residual function, making 100+ layer networks trainable and winning ILSVRC 2015. |
| [[Paper — Mastering the Game of Go with Deep Neural Networks and Tree Search (2016)]] (AlphaGo) | [[Cluster — Reinforcement learning]] | AlphaGo - policy network narrows search breadth, value network reduces search depth, combined inside MCTS - first program to beat a human pro at Go without a handicap. |
| [[Paper — Mastering the Game of Go without Human Knowledge (2017)]] (AlphaGo Zero) | [[Cluster — Reinforcement learning]] | AlphaGo Zero - a single network learns Go purely from self-play, no human game data at all, beating the original AlphaGo 100-0 after 3 days of training. |
| [[Paper — Outrageously Large Neural Networks (2017)]] (MoE) | this one | MoE - a sparsely-gated mixture-of-experts layer routes each example to a handful of thousands of expert sub-networks, decoupling model capacity from compute and reaching 137 billion parameters at minor efficiency cost. |
| [[Paper — Attention Is All You Need (2017)]] (Transformer) | this one | Introduced the Transformer, replacing recurrence with self-attention - the architecture nearly every modern LLM is built on. |
| [[Paper — Deep RL from Human Preferences (2017)]] | [[Cluster — Post-training alignment]] | Origin of RLHF - learn a reward model from human comparisons of short clips, then optimize it, cutting oversight cost by ~3 orders of magnitude. |
| [[Paper — Proximal Policy Optimization (2017)]] | [[Cluster — Reinforcement learning]] | PPO - a clipped policy-gradient objective that gets trust-region-level stability with only first-order optimization, and became the RL algorithm RLHF pipelines actually run. |
| [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1) | this one | GPT-1 - pretrains a decoder-only Transformer as a left-to-right language model, then fine-tunes it with minimal added parameters per task, establishing the pretrain-then-fine-tune recipe GPT-2/3 scale up. |
| [[Paper — BERT (2018)]] | this one | BERT - pretrains a bidirectional Transformer with a masked-language-model objective, founding the recipe behind every encoder-only representation model since. |
| [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] (GPT-2) | this one | GPT-2 - a 1.5B-parameter Transformer, scaled up on curated web text, starts performing tasks like translation and summarization zero-shot from a natural-language prompt alone, no fine-tuning needed. |
| [[Paper — Scaling Laws for Neural Language Models (2020)]] | this one | Scaling Laws - language model loss follows smooth power laws in model size, data, and compute; the compute-optimal response to more compute is overwhelmingly a bigger model, not more data or steps. |
| [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3) | this one | GPT-3 - showed a 175B-parameter LM can learn tasks from prompt examples alone, with no gradient updates, making the prompt the new interface. |
| [[Paper — LoRA (2021)]] | [[Cluster — Efficient fine-tuning]] | Freeze the pretrained weights and train only a low-rank decomposition of the weight update, cutting GPT-3's trainable parameters 10,000x with zero added inference latency. |
| [[Paper — Training LMs to Follow Instructions (2022)]] (InstructGPT) | [[Cluster — Post-training alignment]] | InstructGPT - RLHF applied to GPT-3 at scale, showing a 1.3B fine-tuned model beats 175B raw GPT-3 on human preference. |
| [[Paper — Training Compute-Optimal Large Language Models (2022)]] (Chinchilla) | this one | Chinchilla - model size and training tokens should scale equally with compute, not model size 5x faster; a 70B model on 4x more data than 280B Gopher beats it and every larger contemporary model. |
| [[Paper — Constitutional AI (2022)]] | [[Cluster — Post-training alignment]] | Trains a harmless, non-evasive assistant using only a short written list of principles, replacing human harmlessness labels with AI-generated ones (RLAIF). |
| [[Paper — Direct Preference Optimization (2023)]] | [[Cluster — Post-training alignment]] | DPO - a closed-form reparameterization that turns the RLHF objective into a single classification loss on the policy itself, matching or beating PPO-based RLHF with no reward model and no RL loop. |
| [[Paper — QLoRA (2023)]] | [[Cluster — Efficient fine-tuning]] | Backpropagate through a frozen 4-bit quantized base model into full-precision LoRA adapters, cutting 65B fine-tuning memory from 780GB to under 48GB. |

Listed chronologically, which currently traces a clean line: a general procedure for training networks with hidden units by propagating error gradients backward through the network is introduced (1986) → gated memory cells solve the vanishing/exploding gradient problem that had made recurrent networks unable to learn long-range dependencies (1997) → words get represented as learned continuous feature vectors trained jointly with a language model over them, rather than as opaque discrete symbols (2003) → deep learning works at scale (2012) → the same year, three separate directions open at once - dense word representations, pixel-to-policy RL, and tractable deep generative modeling (2013) → and it is not robust (2013) → a second deep generative paradigm arrives, sequences learn to map to other sequences through a single vector, that vector bottleneck gets fixed with a learned per-word lookup, the optimizer nearly everything since is trained with is introduced, and randomly zeroing hidden units during training turns out to be a remarkably general fix for overfitting, all in the same year (2014) → internal activations get normalized to train faster, a KL-constrained policy update gives policy-gradient RL its first monotonic-improvement guarantee at scale, a compression algorithm adapted for word segmentation solves the open-vocabulary problem, and shortcut connections make networks over 100 layers deep trainable, all the same year (2015) → a learned policy and value network combined with tree search beat a human professional at Go (2016) → the very next year, that same program is retrained from nothing but the rules of the game, no human data at all, and beats its predecessor 100-0 (2017) → sparse gating decouples model capacity from compute, generalizing the attention lookup into an architecture on its own, and a way to optimize any policy against a learned reward, all arrive the same year (2017) → how to turn human comparisons into that reward (2017) → a decoder-only Transformer pretrained as a plain language model and then fine-tuned per task beats architectures built for each task individually, and four months later a bidirectional version of the same recipe does even better by predicting masked words from context on both sides (2018) → scaling that same decoder-only recipe up on curated web text shows a language model starts performing tasks zero-shot, from a prompt alone, with no fine-tuning step at all (2019) → that scaling behavior turns out to follow precise, predictable power laws, and the compute-optimal response to more compute is overwhelmingly a bigger model rather than more data (2020) → scale alone unlocks task generality (2020) → fine-tuning a model that large gets made cheap (2021) → all the prior threads - architecture, RL, preference learning, and cheap adaptation - combine to aim a frontier LLM at human intent, and the same year that "bigger model, less data" prescription gets corrected to "grow both equally," proven by a 4x-smaller model trained on 4x more data beating everything larger (2022) → the same year, human feedback on harmlessness specifically gets replaced by AI feedback (2022) → and the reward-model-plus-RL machinery itself turns out to be replaceable by a closed-form loss, while fine-tuning gets cheap enough to run on a single GPU (2023).

## Related concepts and methods

- [[Concept — Convolutional neural networks (CNNs)]]
- [[Concept — Transformer architecture]]
- [[Concept — In-context learning]]
- [[Concept — Data contamination]]
- [[Method — Dropout]]
- [[Method — Byte Pair Encoding (BPE)]]

## Notes

Revisit this list as the field moves.
Supersedence gets noted via Tension/update sections on the affected pages, not silent removal.

The reading order now spans Backpropagation (1986) through DPO/QLoRA (2023) with no gaps.
A larger holistic-canon expansion (missed architecture staples, diffusion/multimodal, reasoning/agents, and a heavier alignment/interpretability push) is queued in `BACKLOG.md` and will land in upcoming batches.

## Up

Top-level cluster; no parent.
