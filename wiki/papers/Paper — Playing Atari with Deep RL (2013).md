---
type: paper
status: in-progress
updated: 2026-09-18
summary: "DQN - the first deep learning model to learn control policies directly from raw pixels, combining Q-learning with a CNN and experience replay, beating prior RL methods on six of seven Atari games and expert humans on three."
cluster: ["Reinforcement learning", "Foundational canon"]
sources: ["raw/papers/Paper — Playing Atari with Deep RL (2013).pdf"]
tags: [rl, deep-rl, q-learning, atari]
aliases: ["arxiv:1312.5602", "Mnih et al. 2013", "DQN", "Deep Q-Network"]
---

# Paper — Playing Atari with Deep RL (2013)

Full title: **Playing Atari with Deep Reinforcement Learning**.
Commonly known as **DQN**.

## Metadata

- **arXiv:** 1312.5602 (v1, 2013-12-19)
- **Venue:** NeurIPS 2013 Deep Learning Workshop; not independently verified against DBLP in this session.
- **Authors:** Volodymyr Mnih, Koray Kavukcuoglu, David Silver, Alex Graves, Ioannis Antonoglou, Daan Wierstra, Martin Riedmiller (DeepMind Technologies)

## Impact

The first deep learning model to learn a control policy directly from raw pixels using reinforcement learning, with no hand-crafted features and no access to the game's internal state.
Using one fixed architecture and one fixed set of hyperparameters, DQN outperformed every prior RL approach on six of seven Atari 2600 games and beat an expert human player outright on three of them.
This is the paper that made "deep learning plus RL" a real combination worth pursuing rather than a known source of instability, and it directly seeded the DeepMind research lineage that later produced AlphaGo and, further downstream, the RL machinery ([[Method — Proximal Policy Optimization (PPO)]]) that RLHF pipelines run today.

## Problem it solved

Before this paper, RL's best-known success story was TD-Gammon, a narrow, single-domain result, and follow-up attempts to apply the same recipe to chess, Go, and checkers had largely failed.
Combining Q-learning with a nonlinear function approximator like a neural network was known to risk divergence, which is why most RL research at the time had retreated to linear function approximators with better convergence guarantees.
Deep learning itself compounded the mismatch: its best results assumed large amounts of i.i.d. labeled data drawn from a fixed distribution, while RL produces long sequences of highly correlated states from a distribution that keeps shifting as the policy improves.
The unsolved problem: can a deep network learn control policies end-to-end from raw sensory input, despite RL's data violating nearly every assumption deep learning success had previously depended on?

## Core idea: experience replay makes deep Q-learning stable

DQN pairs a convolutional neural network (the Q-function approximator) with **experience replay**: instead of training on the current, highly-correlated stream of gameplay frames, the agent stores past transitions `(state, action, reward, next state)` in a replay buffer and trains on randomly sampled minibatches drawn from it.
Concretely, this breaks the correlation between consecutive training examples that would otherwise dominate the network's updates (e.g. a long run of "move left" frames biasing the network toward always predicting "move left"), and smooths over the fact that the data distribution itself is shifting as the policy gets better.
This is what let ordinary stochastic gradient descent, the same tool used for supervised deep learning, be applied to a Q-learning update at all: without replay, the combination had a documented tendency to oscillate or diverge outright.

## How it's built

- **Input:** 4 stacked, preprocessed 84x84 grayscale frames (RGB converted to grayscale, downsampled, cropped), giving the network a short window of motion rather than a single static frame.
- **Architecture:** two convolutional layers (16 filters of size 8x8 stride 4, then 32 filters of size 4x4 stride 2) followed by a 256-unit fully connected layer and a linear output layer with one unit per possible action - so all of a state's Q-values come from a single forward pass, rather than one pass per action as in prior architectures.
- **Training:** RMSProp with minibatches of 32, epsilon-greedy exploration annealed linearly from 1.0 to 0.1 over the first million frames, and a replay memory holding the most recent one million frames.
- **One architecture, seven games:** the same network and hyperparameters were used across all seven games with only one exception - the frame-skip parameter was changed for Space Invaders so the laser sprites, which blink at a period aliasing with the default skip rate, remained visible to the network.

## Results (highlights)

- **DQN beat every prior learned baseline by a wide margin** on all seven games tested against hand-engineered linear-feature methods (Sarsa, Contingency), which relied on background subtraction and per-color-channel features DQN was never given.
- **Beat an expert human player outright** on Breakout, Enduro, and Pong, and came close to human performance on Beam Rider.
- **Struggled on games requiring long-horizon strategy** - Q*bert, Seaquest, and Space Invaders remained far below human performance, a pattern the paper attributes to those games needing strategies that extend over long time scales.
- **Training was empirically stable despite no convergence guarantee:** raw episode reward was very noisy during training, but the average predicted Q-value rose smoothly throughout, evidence the network was learning a coherent value function even though nonlinear Q-learning has no theoretical convergence proof.

## Why it endures

DQN's core recipe - a deep function approximator, trained end-to-end on raw sensory input, stabilized by experience replay and off-policy value learning - became the template for the RL-plus-deep-learning combination that DeepMind scaled up repeatedly afterward, first to the full 49-game Atari suite and eventually into AlphaGo's value network.
Experience replay itself outlived this specific paper and became a standard building block used across RL far beyond value-based methods.

## Limitations

- **Evaluated on only seven games**, chosen without a stated selection criterion beyond availability in the Arcade Learning Environment at the time.
- **Reward clipping** (all positive rewards fixed to +1, all negative rewards to -1) was necessary to keep one learning rate working across games with wildly different score scales, but it discards information about reward magnitude the agent might otherwise use.
- **No theoretical convergence guarantee** - the paper is explicit that combining Q-learning with a nonlinear function approximator can in principle diverge, and its empirical stability across these seven games is not proof it will hold in general.

## Relations

- The convolutional backbone is the same broad architecture family introduced by [[Paper — ImageNet Classification with Deep CNNs (2012)]], applied here to control rather than classification.
- Builds on Watkins & Dayan's Q-learning and Tesauro's TD-Gammon (both not yet ingested) as the RL foundations this paper extends into the deep learning setting.
- Sits alongside [[Paper — Proximal Policy Optimization (2017)]] in [[Cluster — Reinforcement learning]] as the value-based counterpart to PPO's policy-gradient approach - both solve "how do you optimize a policy against a scalar reward," using different families of algorithm.
- Part of the same DeepMind RL lineage that produced [[Paper — Deep RL from Human Preferences (2017)]], which trades DQN's hand-specified game score for a reward model learned from human comparisons.

## Up

- [[Cluster — Reinforcement learning]]
- [[Cluster — Foundational canon]]
