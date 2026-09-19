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
| [[Paper — Layer Normalization (2016)]] | this one | Layer Normalization - normalizes across a layer's own hidden units per training case rather than across a mini-batch, becoming the normalization used inside every Transformer block. |
| [[Paper — Mastering the Game of Go without Human Knowledge (2017)]] (AlphaGo Zero) | [[Cluster — Reinforcement learning]] | AlphaGo Zero - a single network learns Go purely from self-play, no human game data at all, beating the original AlphaGo 100-0 after 3 days of training. |
| [[Paper — Outrageously Large Neural Networks (2017)]] (MoE) | this one | MoE - a sparsely-gated mixture-of-experts layer routes each example to a handful of thousands of expert sub-networks, decoupling model capacity from compute and reaching 137 billion parameters at minor efficiency cost. |
| [[Paper — Attention Is All You Need (2017)]] (Transformer) | this one | Introduced the Transformer, replacing recurrence with self-attention - the architecture nearly every modern LLM is built on. |
| [[Paper — Deep RL from Human Preferences (2017)]] | [[Cluster — Post-training alignment]] | Origin of RLHF - learn a reward model from human comparisons of short clips, then optimize it, cutting oversight cost by ~3 orders of magnitude. |
| [[Paper — Proximal Policy Optimization (2017)]] | [[Cluster — Reinforcement learning]] | PPO - a clipped policy-gradient objective that gets trust-region-level stability with only first-order optimization, and became the RL algorithm RLHF pipelines actually run. |
| [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1) | this one | GPT-1 - pretrains a decoder-only Transformer as a left-to-right language model, then fine-tunes it with minimal added parameters per task, establishing the pretrain-then-fine-tune recipe GPT-2/3 scale up. |
| [[Paper — BERT (2018)]] | this one | BERT - pretrains a bidirectional Transformer with a masked-language-model objective, founding the recipe behind every encoder-only representation model since. |
| [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] (GPT-2) | this one | GPT-2 - a 1.5B-parameter Transformer, scaled up on curated web text, starts performing tasks like translation and summarization zero-shot from a natural-language prompt alone, no fine-tuning needed. |
| [[Paper — Scaling Laws for Neural Language Models (2020)]] | this one | Scaling Laws - language model loss follows smooth power laws in model size, data, and compute; the compute-optimal response to more compute is overwhelmingly a bigger model, not more data or steps. |
| [[Paper — Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks (2020)]] | this one | RAG - fine-tunes a retriever and a seq2seq generator jointly, conditioning generation on documents retrieved from an updatable, inspectable non-parametric memory rather than only facts baked into the model's parameters. |
| [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3) | this one | GPT-3 - showed a 175B-parameter LM can learn tasks from prompt examples alone, with no gradient updates, making the prompt the new interface. |
| [[Paper — Denoising Diffusion Probabilistic Models (2020)]] (DDPM) | this one | DDPM - trains a network to reverse a fixed Gaussian noising process step by step, the direct technical ancestor of the diffusion models behind essentially all major image and video generation since. |
| [[Paper — An Image is Worth 16x16 Words (2020)]] (ViT) | this one | ViT - a pure Transformer applied directly to image patches, matching or beating state-of-the-art CNNs once pretrained at sufficient scale. |
| [[Paper — Learning Transferable Visual Models From Natural Language Supervision (2021)]] (CLIP) | this one | CLIP - jointly trains an image encoder and text encoder to predict image-caption pairings, letting any set of natural-language class names become a zero-shot classifier with no retraining. |
| [[Paper — RoFormer - Enhanced Transformer with Rotary Position Embedding (2021)]] | this one | RoPE - rotates query/key vectors by an angle proportional to token position, making relative distance fall directly out of the attention dot product; the positional scheme most modern open-weight LLMs now use. |
| [[Paper — LoRA (2021)]] | [[Cluster — Efficient fine-tuning]] | Freeze the pretrained weights and train only a low-rank decomposition of the weight update, cutting GPT-3's trainable parameters 10,000x with zero added inference latency. |
| [[Paper — High-Resolution Image Synthesis with Latent Diffusion Models (2021)]] | this one | LDM - trains an autoencoder to compress images into a small latent space, then runs diffusion there instead of on raw pixels; the direct architectural basis of Stable Diffusion. |
| [[Paper — Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022)]] | this one | CoT - writing few-shot exemplars as worked reasoning rather than direct answers unlocks multi-step reasoning that is otherwise flat with model scale, once the model is large enough. |
| [[Paper — Training LMs to Follow Instructions (2022)]] (InstructGPT) | [[Cluster — Post-training alignment]] | InstructGPT - RLHF applied to GPT-3 at scale, showing a 1.3B fine-tuned model beats 175B raw GPT-3 on human preference. |
| [[Paper — Training Compute-Optimal Large Language Models (2022)]] (Chinchilla) | this one | Chinchilla - model size and training tokens should scale equally with compute, not model size 5x faster; a 70B model on 4x more data than 280B Gopher beats it and every larger contemporary model. |
| [[Paper — FlashAttention (2022)]] | this one | FlashAttention - restructures attention to minimize slow-memory reads/writes instead of FLOPs, computing the exact same output 2-4x faster with linear rather than quadratic memory. |
| [[Paper — Constitutional AI (2022)]] | [[Cluster — Post-training alignment]] | Trains a harmless, non-evasive assistant using only a short written list of principles, replacing human harmlessness labels with AI-generated ones (RLAIF). |
| [[Paper — Direct Preference Optimization (2023)]] | [[Cluster — Post-training alignment]] | DPO - a closed-form reparameterization that turns the RLHF objective into a single classification loss on the policy itself, matching or beating PPO-based RLHF with no reward model and no RL loop. |
| [[Paper — QLoRA (2023)]] | [[Cluster — Efficient fine-tuning]] | Backpropagate through a frozen 4-bit quantized base model into full-precision LoRA adapters, cutting 65B fine-tuning memory from 780GB to under 48GB. |

Listed chronologically, which currently traces a clean line: a general procedure for training networks with hidden units by propagating error gradients backward through the network is introduced (1986) → gated memory cells solve the vanishing/exploding gradient problem that had made recurrent networks unable to learn long-range dependencies (1997) → words get represented as learned continuous feature vectors trained jointly with a language model over them, rather than as opaque discrete symbols (2003) → deep learning works at scale (2012) → the same year, three separate directions open at once - dense word representations, pixel-to-policy RL, and tractable deep generative modeling (2013) → and it is not robust (2013) → a second deep generative paradigm arrives, sequences learn to map to other sequences through a single vector, that vector bottleneck gets fixed with a learned per-word lookup, the optimizer nearly everything since is trained with is introduced, and randomly zeroing hidden units during training turns out to be a remarkably general fix for overfitting, all in the same year (2014) → internal activations get normalized to train faster, a KL-constrained policy update gives policy-gradient RL its first monotonic-improvement guarantee at scale, a compression algorithm adapted for word segmentation solves the open-vocabulary problem, and shortcut connections make networks over 100 layers deep trainable, all the same year (2015) → a learned policy and value network combined with tree search beat a human professional at Go, and the same year normalization gets rederived per-example instead of per-batch so it finally works cleanly on recurrent networks (2016) → the very next year, that same program is retrained from nothing but the rules of the game, no human data at all, and beats its predecessor 100-0 (2017) → sparse gating decouples model capacity from compute, generalizing the attention lookup into an architecture on its own, and a way to optimize any policy against a learned reward, all arrive the same year (2017) → how to turn human comparisons into that reward (2017) → a decoder-only Transformer pretrained as a plain language model and then fine-tuned per task beats architectures built for each task individually, and four months later a bidirectional version of the same recipe does even better by predicting masked words from context on both sides (2018) → scaling that same decoder-only recipe up on curated web text shows a language model starts performing tasks zero-shot, from a prompt alone, with no fine-tuning step at all (2019) → that scaling behavior turns out to follow precise, predictable power laws, and the compute-optimal response to more compute is overwhelmingly a bigger model rather than more data, and a pretrained generator gets paired with a queryable, swappable document index so it can ground its answers in retrieved text rather than only what it memorized (2020) → scale alone unlocks task generality, a network trained to reverse a fixed noising process one small step at a time turns out to generate images better than GANs, and a pure Transformer applied to image patches with no convolutional bias at all matches state-of-the-art CNNs once pretrained at scale, extending the "scale beats hand-designed structure" lesson from language into vision (2020) → an image encoder and a text encoder get trained together to predict which caption matches which of 400 million web images, turning any natural-language class name into a zero-shot classifier, absolute position encodings get replaced by rotating query and key vectors so relative distance falls straight out of the attention dot product, fine-tuning a model that large gets made cheap, and a pretrained autoencoder lets diffusion run in a small compressed latent space instead of on raw pixels, the architecture Stable Diffusion is later built from (2021) → writing few-shot prompt exemplars as worked-out reasoning instead of direct answers turns out to unlock multi-step reasoning that scale alone could not, but only once a model is large enough (2022) → all the prior threads - architecture, RL, preference learning, and cheap adaptation - combine to aim a frontier LLM at human intent, and the same year that "bigger model, less data" prescription gets corrected to "grow both equally," proven by a 4x-smaller model trained on 4x more data beating everything larger, and attention itself gets rewritten to minimize GPU memory traffic rather than FLOPs, computing the exact same output several times faster (2022) → the same year, human feedback on harmlessness specifically gets replaced by AI feedback (2022) → and the reward-model-plus-RL machinery itself turns out to be replaceable by a closed-form loss, while fine-tuning gets cheap enough to run on a single GPU (2023).

## Related concepts and methods

- [[Concept — Convolutional neural networks (CNNs)]]
- [[Concept — Transformer architecture]]
- [[Concept — In-context learning]]
- [[Concept — Data contamination]]
- [[Method — Dropout]]
- [[Method — Byte Pair Encoding (BPE)]]
- [[Method — Layer Normalization]]
- [[Method — Rotary Position Embedding (RoPE)]]
- [[Method — Denoising Diffusion (DDPM)]]
- [[Method — Latent Diffusion]]
- [[Method — FlashAttention]]
- [[Method — Retrieval-Augmented Generation (RAG)]]
- [[Method — Chain-of-Thought Prompting]]

## Notes

Revisit this list as the field moves.
Supersedence gets noted via Tension/update sections on the affected pages, not silent removal.

The reading order now spans Backpropagation (1986) through DPO/QLoRA (2023) with no gaps.
A larger holistic-canon expansion (missed architecture staples, diffusion/multimodal, reasoning/agents, and a heavier alignment/interpretability push) is queued in `BACKLOG.md` and will land in upcoming batches.

## Up

Top-level cluster; no parent.
