---
type: cluster
status: in-progress
updated: 2026-10-19
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
| [[Paper — Trust Region Policy Optimization (2015)]] | [[Cluster — Reinforcement learning]] | TRPO - first policy-gradient method with a theoretically grounded monotonic-improvement guarantee that scales to large neural-network policies, via a KL-divergence-constrained update. |
| [[Paper — Neural Machine Translation of Rare Words with Subword Units (2015)]] (BPE) | this one | BPE - adapts byte pair encoding to word segmentation, becoming the near-universal tokenization scheme for large language models. |
| [[Paper — Deep Residual Learning for Image Recognition (2015)]] (ResNet) | this one | ResNet - shortcut connections let each block learn a residual function, making 100+ layer networks trainable and winning ILSVRC 2015. |
| [[Paper — Mastering the Game of Go with Deep Neural Networks and Tree Search (2016)]] (AlphaGo) | [[Cluster — Reinforcement learning]] | AlphaGo - policy network narrows search breadth, value network reduces search depth, combined inside MCTS - first program to beat a human pro at Go without a handicap. |
| [[Paper — Concrete Problems in AI Safety (2016)]] | [[Cluster — Post-training alignment]] | Names and frames reward hacking and scalable oversight as concrete research problems, supplying vocabulary the rest of the post-training alignment literature is written in. |
| [[Paper — Deep Learning with Differential Privacy (2016)]] | [[Cluster — ML security & privacy]] | DP-SGD - clip each per-example gradient and add calibrated Gaussian noise, tracked with the moments accountant; now a standard, broadly-taught technique for training any deep model under a formal privacy guarantee, well beyond privacy-security specialists. |
| [[Paper — Layer Normalization (2016)]] | this one | Layer Normalization - normalizes across a layer's own hidden units per training case rather than across a mini-batch, becoming the normalization used inside every Transformer block. |
| [[Paper — Mastering the Game of Go without Human Knowledge (2017)]] (AlphaGo Zero) | [[Cluster — Reinforcement learning]] | AlphaGo Zero - a single network learns Go purely from self-play, no human game data at all, beating the original AlphaGo 100-0 after 3 days of training. |
| [[Paper — Outrageously Large Neural Networks (2017)]] (MoE) | this one | MoE - a sparsely-gated mixture-of-experts layer routes each example to a handful of thousands of expert sub-networks, decoupling model capacity from compute and reaching 137 billion parameters at minor efficiency cost. |
| [[Paper — Attention Is All You Need (2017)]] (Transformer) | this one | Introduced the Transformer, replacing recurrence with self-attention - the architecture nearly every modern LLM is built on. |
| [[Paper — Deep RL from Human Preferences (2017)]] | [[Cluster — Post-training alignment]] | Origin of RLHF - learn a reward model from human comparisons of short clips, then optimize it, cutting oversight cost by ~3 orders of magnitude. |
| [[Paper — Proximal Policy Optimization (2017)]] | [[Cluster — Reinforcement learning]] | PPO - a clipped policy-gradient objective that gets trust-region-level stability with only first-order optimization, and became the RL algorithm RLHF pipelines actually run. |
| [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1) | this one | GPT-1 - pretrains a decoder-only Transformer as a left-to-right language model, then fine-tunes it with minimal added parameters per task, establishing the pretrain-then-fine-tune recipe GPT-2/3 scale up. |
| [[Paper — Towards Deep Learning Models Resistant to Adversarial Attacks (2018)]] | [[Cluster — Adversarial robustness & security]] | Reframes adversarial robustness as a min-max saddle-point problem and shows PGD reliably solves the non-concave inner attack, giving adversarial training its first optimization-theoretic guarantee rather than an empirically-tuned mitigation. |
| [[Paper — BERT (2018)]] | this one | BERT - pretrains a bidirectional Transformer with a masked-language-model objective, founding the recipe behind every encoder-only representation model since. |
| [[Paper — AI Safety via Debate (2018)]] | [[Cluster — Scalable oversight]] | Proposes training two agents to debate a question in front of a human judge who only decides who's more convincing, and proves optimal-play debate with a polynomial-time judge can decide any question in PSPACE. |
| [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] (GPT-2) | this one | GPT-2 - a 1.5B-parameter Transformer, scaled up on curated web text, starts performing tasks like translation and summarization zero-shot from a natural-language prompt alone, no fine-tuning needed. |
| [[Paper — Scaling Laws for Neural Language Models (2020)]] | this one | Scaling Laws - language model loss follows smooth power laws in model size, data, and compute; the compute-optimal response to more compute is overwhelmingly a bigger model, not more data or steps. |
| [[Paper — Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks (2020)]] | this one | RAG - fine-tunes a retriever and a seq2seq generator jointly, conditioning generation on documents retrieved from an updatable, inspectable non-parametric memory rather than only facts baked into the model's parameters. |
| [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3) | this one | GPT-3 - showed a 175B-parameter LM can learn tasks from prompt examples alone, with no gradient updates, making the prompt the new interface. |
| [[Paper — Learning to Summarize from Human Feedback (2020)]] | [[Cluster — Post-training alignment]] | Bridges Christiano et al. 2017's RLHF loop to GPT-3-scale language models, the direct methodological predecessor of InstructGPT. |
| [[Paper — Denoising Diffusion Probabilistic Models (2020)]] (DDPM) | this one | DDPM - trains a network to reverse a fixed Gaussian noising process step by step, the direct technical ancestor of the diffusion models behind essentially all major image and video generation since. |
| [[Paper — An Image is Worth 16x16 Words (2020)]] (ViT) | this one | ViT - a pure Transformer applied directly to image patches, matching or beating state-of-the-art CNNs once pretrained at sufficient scale. |
| [[Paper — Learning Transferable Visual Models From Natural Language Supervision (2021)]] (CLIP) | this one | CLIP - jointly trains an image encoder and text encoder to predict image-caption pairings, letting any set of natural-language class names become a zero-shot classifier with no retraining. |
| [[Paper — RoFormer - Enhanced Transformer with Rotary Position Embedding (2021)]] | this one | RoPE - rotates query/key vectors by an angle proportional to token position, making relative distance fall directly out of the attention dot product; the positional scheme most modern open-weight LLMs now use. |
| [[Paper — LoRA (2021)]] | [[Cluster — Efficient fine-tuning]] | Freeze the pretrained weights and train only a low-rank decomposition of the weight update, cutting GPT-3's trainable parameters 10,000x with zero added inference latency. |
| [[Paper — Finetuned Language Models Are Zero-Shot Learners (2021)]] (FLAN) | this one | FLAN - finetunes a 137B-parameter model on 60+ NLP datasets rewritten as instructions, beating zero-shot GPT-3 on 20 of 25 held-out task types never seen during finetuning. |
| [[Paper — High-Resolution Image Synthesis with Latent Diffusion Models (2021)]] | this one | LDM - trains an autoencoder to compress images into a small latent space, then runs diffusion there instead of on raw pixels; the direct architectural basis of Stable Diffusion. |
| [[Paper — Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022)]] | this one | CoT - writing few-shot exemplars as worked reasoning rather than direct answers unlocks multi-step reasoning that is otherwise flat with model scale, once the model is large enough. |
| [[Paper — Training LMs to Follow Instructions (2022)]] (InstructGPT) | [[Cluster — Post-training alignment]] | InstructGPT - RLHF applied to GPT-3 at scale, showing a 1.3B fine-tuned model beats 175B raw GPT-3 on human preference. |
| [[Paper — Training a Helpful and Harmless Assistant with RLHF (2022)]] (HH-RLHF) | [[Cluster — Post-training alignment]] | Trains separate helpfulness and harmlessness objectives via RLHF, releases the widely-reused HH-RLHF dataset, and shows alignment training carries essentially no capability cost at sufficient scale. |
| [[Paper — Training Compute-Optimal Large Language Models (2022)]] (Chinchilla) | this one | Chinchilla - model size and training tokens should scale equally with compute, not model size growing far faster than data as Kaplan et al. concluded; a 70B model on 4x more data than 280B Gopher beats it and every larger contemporary model. |
| [[Paper — Emergent Abilities of Large Language Models (2022)]] | [[Cluster — Capability evals]] | Names emergent abilities - capabilities absent in smaller language models that appear sharply past a scale threshold - the field's vocabulary for what scaling laws don't predict. |
| [[Paper — FlashAttention (2022)]] | this one | FlashAttention - restructures attention to minimize slow-memory reads/writes instead of FLOPs, computing the exact same output 2-4x faster with linear rather than quadratic memory. |
| [[Paper — ReAct (2022)]] | this one | ReAct - interleaves reasoning "thoughts" with real actions against an external environment, letting reasoning guide what to look up next and observations correct the reasoning; the default shape of the LLM agent loop. |
| [[Paper — Constitutional AI (2022)]] | [[Cluster — Post-training alignment]] | Trains a harmless, non-evasive assistant using only a short written list of principles, replacing human harmlessness labels with AI-generated ones (RLAIF). |
| [[Paper — LLaMA - Open and Efficient Foundation Language Models (2023)]] | this one | LLaMA - trains smaller models on far more tokens than Chinchilla-optimal for cheaper inference, matching or beating much larger closed models using only public data; the open-weights release that catalyzed the modern open-weights ecosystem. |
| [[Paper — Direct Preference Optimization (2023)]] | [[Cluster — Post-training alignment]] | DPO - a closed-form reparameterization that turns the RLHF objective into a single classification loss on the policy itself, matching or beating PPO-based RLHF with no reward model and no RL loop. |
| [[Paper — QLoRA (2023)]] | [[Cluster — Efficient fine-tuning]] | Backpropagate through a frozen 4-bit quantized base model into full-precision LoRA adapters, cutting 65B fine-tuning memory from 780GB to under 48GB. |
| [[Paper — Let's Verify Step by Step (2023)]] | [[Cluster — Post-training alignment]] | Trains a reward model on step-level human labels rather than just the final answer, showing process supervision finds correct solutions far more reliably than outcome supervision alone. |
| [[Paper — Weak-to-Strong Generalization (2023)]] | [[Cluster — Scalable oversight]] | Finetunes strong pretrained models on labels from much weaker supervisors and shows the strong models consistently outperform their supervisors, turning the future problem of humans overseeing superhuman AI into an empirically testable proxy today. |
| [[Paper — DeepSeek-R1 - Incentivizing Reasoning Capability in LLMs via Reinforcement Learning (2025)]] | [[Cluster — Reinforcement learning]] | Trains reasoning purely with rule-based RL and no SFT initialization, showing long chain-of-thought behaviors emerge on their own, then distills the result into open dense models as small as 1.5B parameters. |

Read chronologically, the canon traces one line of development:

- **1986** - A general procedure for training networks with hidden units, by propagating error gradients backward through the network, is introduced.
- **1997** - Gated memory cells solve the vanishing/exploding gradient problem that had kept recurrent networks from learning long-range dependencies.
- **2003** - Words get represented as learned continuous feature vectors trained jointly with a language model over them, rather than as opaque discrete symbols.
- **2012** - Deep learning works at scale.
- **2013**
  - Three separate directions open at once, the first being dense word representations.
  - The second is pixel-to-policy RL.
  - The third is tractable deep generative modeling.
  - The same year, deep networks turn out not to be robust.
- **2014**
  - A second deep generative paradigm arrives.
  - Sequences learn to map to other sequences through a single vector.
  - That vector bottleneck gets fixed with a learned per-word lookup.
  - The optimizer nearly everything since is trained with is introduced.
  - Randomly zeroing hidden units during training turns out to be a remarkably general fix for overfitting.
- **2015**
  - Internal activations get normalized to train faster.
  - A KL-constrained policy update gives policy-gradient RL its first monotonic-improvement guarantee at scale.
  - A compression algorithm adapted for word segmentation solves the open-vocabulary problem.
  - Shortcut connections make networks over 100 layers deep trainable.
- **2016**
  - A learned policy network and value network combined with tree search beat a human professional at Go.
  - Normalization gets rederived per-example instead of per-batch, so it finally works cleanly on recurrent networks.
  - A paper names reward hacking and scalable oversight as concrete research problems rather than speculative superintelligence scenarios, supplying the vocabulary the rest of post-training alignment is written in.
  - Per-example gradient clipping plus calibrated noise, tracked with the moments accountant, makes deep networks trainable under a formal differential-privacy guarantee ([[Paper — Deep Learning with Differential Privacy (2016)]]).
- **2017**
  - The Go program is retrained from nothing but the rules of the game, with no human data at all, and beats its predecessor 100-0.
  - Sparse gating decouples model capacity from compute.
  - The attention lookup gets generalized into an architecture on its own.
  - A way to optimize any policy against a learned reward arrives.
  - So does a way to turn human comparisons into that reward.
- **2018**
  - Adversarial robustness gets reframed as a min-max optimization problem, with projected gradient descent shown to reliably solve the attacker's side of it despite no guarantee that it should, giving adversarial training its first real guarantee rather than an empirically-tuned mitigation.
  - Two agents get trained to debate a question in front of a human judge who only has to decide who's more convincing, with a complexity-theory argument that optimal-play debate can decide anything a polynomial-time judge could ever be shown a proof of.
  - A decoder-only Transformer pretrained as a plain language model and then fine-tuned per task beats architectures built for each task individually.
  - Four months later, a bidirectional version of the same recipe does even better by predicting masked words from context on both sides.
- **2019** - Scaling that decoder-only recipe up on curated web text shows a language model starts performing tasks zero-shot, from a prompt alone, with no fine-tuning step at all.
- **2020**
  - That scaling behavior turns out to follow precise, predictable power laws, and the compute-optimal response to more compute is overwhelmingly a bigger model rather than more data.
  - A pretrained generator gets paired with a queryable, swappable document index so it can ground its answers in retrieved text rather than only what it memorized.
  - Scale alone unlocks task generality.
  - The reward-model-plus-RL loop for optimizing human preferences gets run at GPT-3 scale for the first time, on summarization, beating both a much larger supervised model and the human reference summaries it was trained to imitate.
  - A network trained to reverse a fixed noising process one small step at a time turns out to generate images better than GANs.
  - A pure Transformer applied to image patches, with no convolutional bias at all, matches state-of-the-art CNNs once pretrained at scale, extending the "scale beats hand-designed structure" lesson from language into vision.
- **2021**
  - An image encoder and a text encoder get trained together to predict which caption matches which of 400 million web images, turning any natural-language class name into a zero-shot classifier.
  - Absolute position encodings get replaced by rotating query and key vectors, so relative distance falls straight out of the attention dot product.
  - Fine-tuning a model as large as GPT-3 gets made cheap.
  - Finetuning on dozens of NLP tasks rewritten as natural-language instructions lets a model generalize zero-shot to task types it never saw, but only once it's large enough.
  - A pretrained autoencoder lets diffusion run in a small compressed latent space instead of on raw pixels, the architecture Stable Diffusion is later built from.
- **2022**
  - Writing few-shot prompt exemplars as worked-out reasoning instead of direct answers unlocks multi-step reasoning that scale alone could not, but only once a model is large enough.
  - All the prior threads - architecture, RL, preference learning, and cheap adaptation - combine to aim a frontier LLM at human intent.
  - The RLHF loop gets trained on two separate, partially competing objectives - helpfulness and harmlessness - at scale, showing alignment training carries essentially no capability cost once a model is large enough.
  - The "bigger model, less data" prescription gets corrected to "grow both equally", proven by a 4x-smaller model trained on 4x more data beating everything larger.
  - Attention itself gets rewritten to minimize GPU memory traffic rather than FLOPs, computing the exact same output several times faster.
  - Interleaving reasoning with real actions against an environment lets a model's own thoughts decide what to look up and correct itself from what comes back.
  - Human feedback on harmlessness specifically gets replaced by AI feedback.
- **2023**
  - Openly released 7B-65B foundation models, trained only on public data and deliberately past the Chinchilla compute-optimal point, show a 13B model can beat GPT-3 ([[Paper — LLaMA - Open and Efficient Foundation Language Models (2023)]]).
  - The reward-model-plus-RL machinery itself turns out to be replaceable by a closed-form loss.
  - Fine-tuning gets cheap enough to run on a single GPU.
  - Rewarding each step of a solution instead of only its final answer turns out to build a far more reliable verifier.
  - The future problem of a human overseer too weak to fully evaluate a superhuman model gets tested today by substituting a small model for the human and a much larger one for the superhuman system, finding that naive supervision only partly closes the gap but simple methods close most of the rest.
- **2025** - Reasoning is trained by pure RL against a rule-based correctness check with no supervised reasoning demonstrations at all, and long chain-of-thought behaviors like self-verification and backtracking emerge on their own, matching a frontier closed model and getting distilled cheaply into dense models two orders of magnitude smaller.

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
- [[Method — ReAct]]
- [[Method — Instruction Tuning]]

## Notes

Revisit this list as the field moves.
Supersedence gets noted via Tension/update sections on the affected pages, not silent removal.

The reading order now spans Backpropagation (1986) through DeepSeek-R1 (Jan 2025).

## Up

Top-level cluster; no parent.
