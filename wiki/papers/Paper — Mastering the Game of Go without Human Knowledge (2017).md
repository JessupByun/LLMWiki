---
type: paper
status: mature
updated: 2026-09-18
summary: "AlphaGo Zero - a single network learns Go purely from self-play against itself, with no human game data at all, and beats the original champion-defeating AlphaGo 100-0 after just 3 days of training on one machine."
cluster: ["Reinforcement learning", "Foundational canon"]
sources: ["raw/papers/reinforcement-learning/Paper — Mastering the Game of Go without Human Knowledge (2017).pdf"]
tags: [rl, self-play, mcts, alphago, tabula-rasa]
aliases: ["doi:10.1038/nature24270", "Silver et al. 2017", "AlphaGo Zero"]
---

# Paper — Mastering the Game of Go without Human Knowledge (2017)

Commonly known as **AlphaGo Zero**.

## Metadata

- **arXiv:** none; published directly in Nature.
- **Venue:** Nature 550, 354-359 (2017); commonly cited as doi:10.1038/nature24270, not independently verified against the journal record in this session.
- **Authors:** David Silver, Julian Schrittwieser, Karen Simonyan, Ioannis Antonoglou, Aja Huang, Arthur Guez, Thomas Hubert, Lucas Baker, Matthew Lai, Adrian Bolton, Yutian Chen, Timothy Lillicrap, Fan Hui, Laurent Sifre, George van den Driessche, Thore Graepel, Demis Hassabis.
- **Affiliation:** DeepMind.

## Impact

[[Paper — Mastering the Game of Go with Deep Neural Networks and Tree Search (2016)]] (AlphaGo) needed a bootstrap of human expert games before it could beat a human champion.
This paper removes that bootstrap entirely: AlphaGo Zero learns Go from nothing but the rules of the game, playing only against itself, and after three days of training on a single machine it defeats the exact version of AlphaGo that beat Lee Sedol, 100 games to 0.
A larger version trained for 40 days then beat AlphaGo Master - the strongest prior AlphaGo, itself undefeated 60-0 against top human professionals online - by 89 games to 11.
Closes [[Cluster — Reinforcement learning]]'s open thread asking for a pure self-play, no-human-data successor to AlphaGo.

## Problem it solved

AlphaGo's pipeline trained a policy network by supervised learning on human expert moves first, refined it with self-play reinforcement learning second, trained a separate value network third, and combined both with Monte Carlo Tree Search using handcrafted rollout features at inference time.
Every stage of that pipeline was either bottlenecked by the availability and quality of human game records, or relied on domain-specific handcrafted features - so the approach couldn't straightforwardly generalize to domains without abundant expert data, and its ceiling was arguably capped by what human play could teach it.

## Core idea

Replace the two separate networks and the rollout-based search with one neural network `f_θ(s) = (p, v)` that takes the raw board as input and outputs both a move-probability vector and a scalar value estimate for the current position - and train it entirely from its own self-play, using no rollouts and no human game data at all.
The trick that makes this self-play loop actually improve is treating Monte Carlo Tree Search itself as a *policy improvement operator*: at every position, running MCTS guided by the current network produces search probabilities `π` that are consistently much stronger than the network's own raw move probabilities `p`, because the lookahead search catches mistakes a single forward pass would miss.
Self-play then generates a policy-evaluation signal for free - play out the game using those improved search probabilities, and the actual winner `z` becomes a ground-truth value label for every position visited.
The network is trained by gradient descent to make its own `(p, v)` estimates converge toward these MCTS-improved `(π, z)` targets, which produces a *better* network for the next round of MCTS, which produces *even stronger* search probabilities, and the cycle repeats - a classic policy-iteration loop, except both the policy-improvement and policy-evaluation steps are done by the same self-play-with-search procedure rather than by separate mechanisms.
Because the network alone (not a rollout policy) evaluates every leaf position, dropping the Monte Carlo rollouts entirely turns out not just to be simpler but to work better.

## How it's built

The sole input is the raw board history: 19x19x17 binary feature planes (8 for the current player's stones over the last 8 positions, 8 for the opponent's, 1 constant plane for whose turn it is) - no handcrafted features of any kind.
This feeds a residual tower of 20 or 40 blocks, directly reusing [[Paper — Deep Residual Learning for Image Recognition (2015)]]'s shortcut-connection design and [[Paper — Batch Normalization (2015)]]'s normalization inside every block, which then splits into a policy head and a value head.
The training loss sums a cross-entropy term (matching the network's move probabilities to the MCTS search probabilities), a mean-squared-error term (matching the network's value prediction to the actual self-play game outcome), and an L2 regularization term.
Three components run continually and asynchronously: the network is optimized on a rolling window of the most recent self-play games; every new checkpoint is evaluated against the current best network and must win by a >55% margin (to rule out noise) before it's promoted; and the current best network generates the next batch of self-play games used for training.

## Results

The smaller (20-block) run overtook AlphaGo Lee - which itself took months to train - within just 36 hours of self-play, and after 72 hours on a single 4-TPU machine it beat AlphaGo Lee's 48-TPU distributed setup 100 games to 0.
The larger (40-block, 40-day) run reached an Elo rating of 5,185, ahead of AlphaGo Master's 4,858, AlphaGo Lee's 3,739, and AlphaGo Fan's 3,144; the raw network alone, with no search at all, still reached 3,055 Elo.
An ablation isolating architecture from algorithm found that switching to a residual architecture over AlphaGo Lee's plain convolutional one added roughly 600 Elo, and merging the separate policy and value networks into one added a further ~600 Elo on top of that.
Despite a comparison network trained by supervised learning on human games reaching *higher* move-prediction accuracy, the self-play-trained network played substantially better overall - beating the human-imitating network within the first 24 hours of training - suggesting it learned a strategy qualitatively different from human play.
Over training, AlphaGo Zero rediscovered most standard human joseki (corner sequences) before moving past them to prefer novel variants, and understood nearly every core Go concept before shicho (ladder captures), one of the very first things human players learn - an inversion of the usual human learning order.

## Why it endures

This paper establishes the "single network, self-play, MCTS as the inner-loop policy-improvement step, zero human data" template that AlphaZero (Silver et al. 2018, not yet ingested) generalizes to chess and shogi, and it's the direct ancestor of every later system that treats tree search or other verification-at-training-time as a way to bootstrap a policy past what supervised imitation alone could reach.

## Limitations

Despite the "tabula rasa" framing, the paper is explicit that AlphaGo Zero is not domain-knowledge-free in an absolute sense: it is given perfect knowledge of the game rules, a Tromp-Taylor scoring function to resolve simulated games, a board-matched 19x19 convolutional architecture, and data augmentation that exploits Go's rotational and reflective symmetry.
The approach is also demonstrated on exactly one domain - a two-player, zero-sum, perfect-information board game - and the paper does not test whether the same recipe transfers as-is to domains without a cheap, perfect simulator.

## Relations

- Directly extends and replaces the training pipeline of [[Paper — Mastering the Game of Go with Deep Neural Networks and Tree Search (2016)]] (AlphaGo): removes the human-data supervised-learning bootstrap, merges the separate policy and value networks into one, and drops Monte Carlo rollouts entirely.
- Builds on [[Paper — Deep Residual Learning for Image Recognition (2015)]] (residual blocks) and [[Paper — Batch Normalization (2015)]] (used throughout the residual tower).
- Extends the Monte Carlo Tree Search lineage AlphaGo (2016) also builds on, but reframes MCTS itself as the policy-improvement step inside training rather than only a deployment-time search procedure.
- Directly generalized by AlphaZero (Silver et al. 2018, not yet ingested), which applies the same self-play-plus-MCTS recipe to chess and shogi.

## Up

- [[Cluster — Reinforcement learning]]
- [[Cluster — Foundational canon]]
