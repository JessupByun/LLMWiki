---
type: cluster
status: in-progress
updated: 2026-09-18
summary: "RL algorithms and RL-for-LLMs - the policy-optimization machinery that post-training alignment runs on top of."
cluster: []
sources: []
tags: [rl, policy-gradient]
aliases: []
---

# Cluster — Reinforcement learning

RL algorithms in their own right: how a policy is actually optimized against a scalar reward, independent of where that reward came from.

This is deliberately narrower than [[Cluster — Post-training alignment]], which owns the question of *what to optimize* - collecting human preferences and turning them into a learned reward model.
This cluster owns *how the optimization itself is run* once a reward function (learned or hand-specified) exists.
The split matters because the same RL algorithm gets reused across very different reward sources: Atari scores, MuJoCo locomotion rewards, and a reward model trained on human preferences all get optimized the same way.

## Members

| Page | Why it's here |
|------|----------------|
| [[Paper — Playing Atari with Deep RL (2013)]] (DQN) | First deep learning model to learn control policies from raw pixels - value-based Q-learning stabilized with experience replay, the canonical predecessor to policy-gradient methods like PPO. |
| [[Paper — Mastering the Game of Go with Deep Neural Networks and Tree Search (2016)]] (AlphaGo) | Combines a learned policy network (narrows search breadth) and value network (reduces search depth) inside Monte Carlo Tree Search - a third distinct way of pairing learned functions with optimization, alongside DQN's pure value-based learning and PPO's pure policy-gradient learning. |
| [[Paper — Mastering the Game of Go without Human Knowledge (2017)]] (AlphaGo Zero) | Replaces AlphaGo's human-data bootstrap and separate policy/value networks with one network trained purely by self-play, treating MCTS itself as the policy-improvement step inside training - beats the original AlphaGo 100-0 with no human game data at all. |
| [[Paper — Proximal Policy Optimization (2017)]] | Clipped-objective policy gradient method that gets trust-region-level stability with only first-order optimization - the algorithm RLHF pipelines actually run. |

## Methods and concepts

- [[Method — Proximal Policy Optimization (PPO)]]
- Deep Q-learning with experience replay - not yet split into its own Method page; currently documented on [[Paper — Playing Atari with Deep RL (2013)]] directly.
- Monte Carlo Tree Search combined with learned policy/value networks - not yet split into its own Method page; currently documented on [[Paper — Mastering the Game of Go with Deep Neural Networks and Tree Search (2016)]] and [[Paper — Mastering the Game of Go without Human Knowledge (2017)]] directly.

## Open threads

Papers that would fill obvious gaps here: TRPO (the predecessor PPO improves on, referenced but not yet ingested) and generalized advantage estimation (the advantage estimator PPO uses). AlphaZero (Silver et al. 2018) would be a natural next addition, generalizing AlphaGo Zero's self-play-plus-MCTS recipe from Go to chess and shogi.

## Up

Top-level cluster; no parent.
