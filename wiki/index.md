# Wiki index

Personal AI security & safety research wiki, on a foundational-canon base.
**Schema:** `../CLAUDE.md`.

One line per entry, taken verbatim from the page's `summary:` frontmatter.

## Clusters

| Page                                            | One line                                                                                                                        |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| [[Cluster — Foundational canon]]                | Landmark papers and ideas that are must-know background regardless of sub-specialty, independent of any tie to security.        |
| [[Cluster — Post-training alignment]]           | Turning a pretrained model into one that does what humans want - preference learning, reward modeling, RLHF and its successors. |
| [[Cluster — Adversarial robustness & security]] | Attacks on learned systems and defenses against them - adversarial examples, transferability, jailbreaks, and prompt injection. |
| [[Cluster — Reinforcement learning]]            | RL algorithms and RL-for-LLMs - the policy-optimization machinery that post-training alignment runs on top of.                 |
| [[Cluster — Efficient fine-tuning]]             | Cheap ways to adapt a large pretrained model to a downstream task - parameter-efficient fine-tuning and quantization, orthogonal to what the fine-tuning is aiming for. |

## Papers

| Page | ID | One line |
|------|----|----------|
| [[Paper — ImageNet Classification with Deep CNNs (2012)]] (AlexNet) | NeurIPS 2012 | AlexNet - the ILSVRC-2012 landslide that convinced the field deep networks plus data plus compute beat hand-engineered features. |
| [[Paper — Efficient Estimation of Word Representations (2013)]] (word2vec) | arxiv:1301.3781 | word2vec - two cheap log-linear architectures (CBOW, Skip-gram) that learn word vectors from billions of words in a day, and showed the resulting vectors support linear analogy arithmetic like king - man + woman = queen. |
| [[Paper — Playing Atari with Deep RL (2013)]] (DQN) | arxiv:1312.5602 | DQN - the first deep learning model to learn control policies directly from raw pixels, combining Q-learning with a CNN and experience replay, beating prior RL methods on six of seven Atari games and expert humans on three. |
| [[Paper — Auto-Encoding Variational Bayes (2013)]] (VAE) | arxiv:1312.6114 | VAE - introduced the reparameterization trick, turning an intractable Bayesian inference problem into something trainable end-to-end with ordinary backpropagation, and founded one of the two dominant deep generative modeling paradigms. |
| [[Paper — Intriguing Properties of Neural Networks (2013)]] | arxiv:1312.6199 | Discovered adversarial examples - imperceptible perturbations that reliably fool networks - and showed the smoothness assumption behind kernel methods fails for deep nets. |
| [[Paper — Generative Adversarial Networks (2014)]] (GANs) | arxiv:1406.2661 | GANs - trains a generator and a discriminator against each other in a minimax game until the generator's samples are indistinguishable from real data, with no explicit likelihood and no Markov chains needed. |
| [[Paper — Sequence to Sequence Learning with Neural Networks (2014)]] (seq2seq) | arxiv:1409.3215 | seq2seq - two LSTMs (one encodes, one decodes) that map a variable-length input sequence to a variable-length output sequence through a single fixed-length vector, the first pure neural system to beat a phrase-based SMT baseline on machine translation. |
| [[Paper — Neural Machine Translation by Jointly Learning to Align and Translate (2014)]] (Bahdanau attention) | arxiv:1409.0473 | Bahdanau attention - replaced the single fixed-length context vector in encoder-decoder translation with a learned, per-step weighted lookup over every source word, the direct ancestor of the Query/Key/Value attention used in every modern Transformer. |
| [[Paper — Adam - A Method for Stochastic Optimization (2014)]] | arxiv:1412.6980 | Adam - an adaptive first-order optimizer that tracks running estimates of the gradient's first and second moments with bias correction, combining AdaGrad's sparse-gradient handling with RMSProp's non-stationary-objective handling, and became the default optimizer for training neural networks. |
| [[Paper — Batch Normalization (2015)]] | arxiv:1502.03167 | Batch Normalization - normalizes each layer's inputs using mini-batch statistics during training, letting networks use much higher learning rates and matching a state-of-the-art ImageNet model's accuracy in 14x fewer training steps. |
| [[Paper — Deep Residual Learning for Image Recognition (2015)]] (ResNet) | arxiv:1512.03385 | ResNet - shortcut connections let each block learn a residual function relative to its input rather than a full transformation, making networks over 100 layers deep trainable for the first time and winning ILSVRC 2015. |
| [[Paper — Mastering the Game of Go with Deep Neural Networks and Tree Search (2016)]] (AlphaGo) | doi:10.1038/nature16961 | AlphaGo - combines a policy network (narrows search breadth) and a value network (reduces search depth) inside Monte Carlo Tree Search, the first program to defeat a human professional Go player without a handicap. |
| [[Paper — Outrageously Large Neural Networks (2017)]] (MoE) | arxiv:1701.06538 | MoE - a trainable gating network sparsely activates a handful of thousands of expert sub-networks per example, decoupling model capacity from computation cost and hitting 137 billion parameters at only a minor efficiency loss. |
| [[Paper — Attention Is All You Need (2017)]] (Transformer) | arxiv:1706.03762 | Introduced the Transformer, replacing recurrence with self-attention - the architecture nearly every modern LLM is built on. |
| [[Paper — Deep RL from Human Preferences (2017)]] | arxiv:1706.03741 | Origin of RLHF - learn a reward model from human comparisons of short clips, then optimize it, cutting oversight cost by ~3 orders of magnitude. |
| [[Paper — Proximal Policy Optimization (2017)]] | arxiv:1707.06347 | PPO - a clipped policy-gradient objective that gets trust-region-level stability with only first-order optimization, and became the RL algorithm RLHF pipelines actually run. |
| [[Paper — Improving Language Understanding by Generative Pre-Training (2018)]] (GPT-1) | OpenAI preprint | GPT-1 - pretrain a Transformer decoder as a plain left-to-right language model on unlabeled text, then fine-tune it with minimal added parameters on each downstream task, beating architectures purpose-built for 9 of 12 NLP benchmarks. |
| [[Paper — BERT (2018)]] | arxiv:1810.04805 | BERT - pretrains a bidirectional Transformer by predicting randomly masked tokens from both left and right context at once, advancing state of the art on 11 NLP tasks and founding the masked-language-model recipe behind every encoder-only representation model since. |
| [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] (GPT-2) | OpenAI report | GPT-2 - a 1.5B-parameter Transformer trained on 40GB of curated web text achieves state-of-the-art results on 7 of 8 language modeling benchmarks in a zero-shot setting, showing a big enough language model starts performing tasks like translation and summarization from a natural-language prompt with no fine-tuning at all. |
| [[Paper — Language Models are Few-Shot Learners (2020)]] (GPT-3) | arxiv:2005.14165 | GPT-3 - showed a 175B-parameter LM can learn tasks from prompt examples alone, with no gradient updates, making the prompt the new interface. |
| [[Paper — LoRA (2021)]] | arxiv:2106.09685 | LoRA - freeze the pretrained weights and train only a low-rank decomposition of the weight update, cutting GPT-3's trainable parameters 10,000x with zero added inference latency. |
| [[Paper — Training LMs to Follow Instructions (2022)]] (InstructGPT) | arxiv:2203.02155 | InstructGPT - RLHF applied to GPT-3 at scale, showing a 1.3B fine-tuned model beats 175B raw GPT-3 on human preference, and setting the SFT-to-RM-to-PPO recipe every instruction-tuned model since has run. |
| [[Paper — Constitutional AI (2022)]] | arxiv:2212.08073 | Trains a harmless, non-evasive assistant using only a short written list of principles, replacing human harmlessness labels with AI-generated ones (RLAIF). |
| [[Paper — QLoRA (2023)]] | arxiv:2305.14314 | QLoRA - backpropagate through a frozen 4-bit quantized base model into full-precision LoRA adapters, cutting 65B fine-tuning memory from 780GB to under 48GB with no performance loss. |
| [[Paper — Direct Preference Optimization (2023)]] | arxiv:2305.18290 | DPO - a closed-form reparameterization that turns the RLHF objective into a single classification loss on the policy itself, matching or beating PPO-based RLHF with no reward model and no RL loop. |

## Concepts & methods

| Page | Type | One line |
|------|------|----------|
| [[Concept — Convolutional neural networks (CNNs)]] | concept | Architecture applying learned filters convolutionally to exploit locality and translation-invariance; the first proof deep nets beat hand-engineered features. |
| [[Concept — Transformer architecture]] | concept | Sequence-modeling architecture built entirely on attention, no recurrence or convolution; the substrate nearly all LLM work targets. |
| [[Concept — In-context learning]] | concept | A model adapting to a task from examples in its prompt, within a single forward pass and with no weight updates. |
| [[Concept — Adversarial examples]] | concept | Inputs perturbed imperceptibly but deliberately to cause misclassification, which transfer across models and so constitute a real attack. |
| [[Concept — Bradley-Terry preference model]] | concept | Statistical model where the probability one item is preferred depends on the difference in latent scores - the bridge from human comparisons to a reward function. |
| [[Concept — Reward hacking]] | concept | An optimizer scoring highly on the specified objective while violating the intent behind it - the characteristic failure of optimizing a proxy. |
| [[Concept — Data contamination]] | concept | Benchmark test data leaking into a web-scraped training set, so reported scores may measure memorization rather than capability. |
| [[Method — Reinforcement learning from human feedback (RLHF)]] | method | Fit a reward model to human preference comparisons, then optimize a policy against it with RL - the standard alignment pipeline. |
| [[Method — Proximal Policy Optimization (PPO)]] | method | Policy-gradient RL algorithm that clips the probability ratio between new and old policy, giving trust-region-like stability without a second-order solve. |
| [[Method — Dropout]] | method | Randomly zero hidden units during training to block co-adaptation; architecture-agnostic regularizer that reappears nearly everywhere. |

## Benchmarks & datasets

*None yet.*

## Repos

*None yet.*

## Questions

*None yet.* Two threads are close to earning a page: whether in-context learning is genuine test-time learning or task recognition, and whether public benchmark scores survive contamination at scale.
