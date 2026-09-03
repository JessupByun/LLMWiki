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

## Papers

| Page | ID | One line |
|------|----|----------|
| [[Paper — ImageNet Classification with Deep CNNs (2012)]] | NeurIPS 2012 | AlexNet - the ILSVRC-2012 landslide that convinced the field deep networks plus data plus compute beat hand-engineered features. |
| [[Paper — Intriguing Properties of Neural Networks (2013)]] | arxiv:1312.6199 | Discovered adversarial examples - imperceptible perturbations that reliably fool networks - and showed the smoothness assumption behind kernel methods fails for deep nets. |
| [[Paper — Attention Is All You Need (2017)]] | arxiv:1706.03762 | Introduced the Transformer, replacing recurrence with self-attention - the architecture nearly every modern LLM is built on. |
| [[Paper — Deep RL from Human Preferences (2017)]] | arxiv:1706.03741 | Origin of RLHF - learn a reward model from human comparisons of short clips, then optimize it, cutting oversight cost by ~3 orders of magnitude. |
| [[Paper — Proximal Policy Optimization (2017)]] | arxiv:1707.06347 | PPO - a clipped policy-gradient objective that gets trust-region-level stability with only first-order optimization, and became the RL algorithm RLHF pipelines actually run. |
| [[Paper — Language Models are Few-Shot Learners (2020)]] | arxiv:2005.14165 | GPT-3 - showed a 175B-parameter LM can learn tasks from prompt examples alone, with no gradient updates, making the prompt the new interface. |
| [[Paper — Training LMs to Follow Instructions (2022)]] | arxiv:2203.02155 | InstructGPT - RLHF applied to GPT-3 at scale, showing a 1.3B fine-tuned model beats 175B raw GPT-3 on human preference, and setting the SFT-to-RM-to-PPO recipe every instruction-tuned model since has run. |
| [[Paper — Constitutional AI (2022)]] | arxiv:2212.08073 | Trains a harmless, non-evasive assistant using only a short written list of principles, replacing human harmlessness labels with AI-generated ones (RLAIF). |
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
