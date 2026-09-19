---
type: paper
status: in-progress
updated: 2026-09-18
summary: "AlphaGo - combines a policy network (narrows search breadth) and a value network (reduces search depth) inside Monte Carlo Tree Search, the first program to defeat a human professional Go player without a handicap."
cluster: ["Reinforcement learning", "Foundational canon"]
sources: ["raw/papers/reinforcement-learning/Paper — Mastering the Game of Go with Deep Neural Networks and Tree Search (2016).pdf"]
tags: [rl, mcts, self-play, policy-gradient, go]
aliases: ["doi:10.1038/nature16961", "Silver et al. 2016", "AlphaGo"]
---

# Paper — Mastering the Game of Go with Deep Neural Networks and Tree Search (2016)

Commonly known as **AlphaGo**.

## Metadata

- **Venue:** Nature 529, 484-489 (28 January 2016), doi:10.1038/nature16961; no arXiv id, printed on the paper itself.
- **Authors:** David Silver, Aja Huang, Chris J. Maddison, Arthur Guez, Laurent Sifre, George van den Driessche, Julian Schrittwieser, Ioannis Antonoglou, Veda Panneershelvam, Marc Lanctot, Sander Dieleman, Dominik Grewe, John Nham, Nal Kalchbrenner, Ilya Sutskever, Timothy Lillicrap, Madeleine Leach, Koray Kavukcuoglu, Thore Graepel, Demis Hassabis (Google DeepMind)

## Impact

The first program to defeat a human professional Go player without a handicap in the full 19x19 game - beating the European champion Fan Hui 5 games to 0 in a formal match, a feat previously believed to be at least a decade away given Go's search space (branching factor ~250, depth ~150, versus chess's ~35 and ~80).
AlphaGo combines two deep convolutional neural networks - a policy network that proposes promising moves and a value network that evaluates board positions - inside a Monte Carlo Tree Search, trained through a pipeline of supervised learning from human expert games followed by reinforcement learning from self-play.
In an internal tournament, AlphaGo won 494 of 495 games (99.8%) against the strongest existing Go programs.

## Problem it solved

Exhaustive minimax search is infeasible in Go: with a branching factor around 250 and typical game length around 150 moves, the search tree is astronomically larger than chess's.
Prior approaches reduced the effective search space in one of two ways - truncating search depth with a position-evaluation function (which had achieved superhuman play in chess, checkers, and othello, but was believed intractable in Go given the game's complexity) or reducing search breadth by sampling moves from a policy and averaging Monte Carlo rollouts to the end of the game (Monte Carlo Tree Search, which had reached only weak amateur strength in Go).
Neither approach alone had closed the gap to human professional play; AlphaGo's problem was to find a way to do both - reduce depth and reduce breadth - using deep neural networks trained specifically for this purpose.

## Core idea: a policy network narrows the search, a value network shortens it

The policy network `p(a|s)` outputs a probability distribution over legal moves, letting the tree search explore only a narrow beam of high-probability moves instead of every legal option - this reduces search *breadth*.
The value network `v(s)` outputs a single scalar estimate of the probability of winning from a given position, letting the search truncate a simulated line early and read off an estimate instead of playing all the way to the end of the game - this reduces search *depth*.
Both are combined inside Monte Carlo Tree Search: at each leaf node, the position is scored by mixing the value network's direct estimate with the outcome of a fast Monte Carlo rollout (weighted 50/50 in the paper's best configuration), while the policy network's output probabilities bias which branches the search explores in the first place.

One finding worth isolating precisely because it's counterintuitive: the supervised-learning policy network (trained purely to imitate human expert moves) worked *better* as AlphaGo's search-tree prior than the reinforcement-learning policy network (trained purely to win), even though the RL network won over 80% of games head-to-head against the SL network on its own.
The paper's explanation: humans propose a diverse beam of several plausible moves, which is exactly what a tree search needs to explore productively, while RL training sharpens the policy toward a single best move, narrowing the beam in a way that hurts search even though it wins more games unaided.
The value network, by contrast, was more useful when derived from the stronger RL policy - a concrete lesson that the same trained artifact can be the wrong choice for one role in a larger system and the right choice for another.

## How it's built

- **SL policy network:** 13-layer CNN (192 filters), trained on 30 million positions from the KGS Go server, reaching 57.0% top-1 move-prediction accuracy versus the prior state of the art's 44.4%.
- **RL policy network:** identical architecture, initialized from the SL network's weights, then refined by policy-gradient self-play against a randomized pool of its own earlier iterations - reaching an 85% win rate against the strong open-source program Pachi using no search at all.
- **Value network:** similar CNN architecture with a scalar tanh output, trained by regression on a purpose-built data set of 30 million self-play positions (one position sampled per game, to avoid overfitting to the strong correlation between successive positions in the same game).
- **Search:** an asynchronous, multi-threaded Monte Carlo Tree Search (APV-MCTS) combining both networks; the distributed version ran across 1,202 CPUs and 176 GPUs.

## Results (highlights)

- **99.8% win rate** (494/495 games) against the strongest existing Go programs (Crazy Stone, Zen, Pachi, Fuego, GnuGo) in an internal tournament.
- **Defeated Fan Hui 5-0** in a formal match (5-9 October 2015) - the first professional-level, no-handicap defeat of a human by a computer Go program.
- **Distributed AlphaGo beat single-machine AlphaGo 77%** of the time, and won 100% of games against every other program tested.
- **Value network alone (no rollouts) already exceeded every other Go program's performance**, though the best configuration mixed value-network and rollout evaluation evenly (λ=0.5).

## Why it endures

AlphaGo established the recipe - a policy network to narrow search breadth, a value network to reduce search depth, both learned and combined inside tree search - that became the direct ancestor of AlphaGo Zero's pure self-play approach and AlphaZero's generalization to chess and shogi, and more broadly the template for using deep learning to compress expensive planning into fast, learned approximations.

## Limitations

- **Depends heavily on human expert data** for the initial supervised policy network (30 million KGS positions) and on handcrafted input features and pattern templates for the fast rollout policy - a meaningfully different starting point from the "tabula rasa" pure self-play approach of the paper's own later successor, AlphaGo Zero.
- **The SL-vs-RL policy tension noted above is left unresolved as a general principle** - the paper reports the empirical finding but does not develop a theory of when a component's standalone strength diverges from its usefulness inside a larger search system.

## Relations

- Builds on Monte Carlo Tree Search (Coulom 2006; Kocsis & Szepesvari 2006, neither yet ingested) and the deep CNN architecture family established by [[Paper — ImageNet Classification with Deep CNNs (2012)]], applied here to a 19x19 board treated as an image.
- The policy network's supervised-pretraining-then-RL-refinement pipeline prefigures the SFT-then-PPO recipe later used in [[Paper — Training LMs to Follow Instructions (2022)]] - both start from a supervised warm start and then let policy-gradient RL take over.
- Directly extended by AlphaGo Zero (2017) and AlphaZero (2017/2018), neither yet ingested, which remove the human-data dependency and merge the policy and value networks into one.
- Joins [[Paper — Playing Atari with Deep RL (2013)]] and [[Paper — Proximal Policy Optimization (2017)]] in [[Cluster — Reinforcement learning]] as a third distinct way of combining learned functions with optimization: DQN uses pure value-based learning with no search, PPO uses pure policy-gradient learning with no search, and AlphaGo combines both a learned policy and a learned value function with explicit tree search.

## Up

- [[Cluster — Reinforcement learning]]
- [[Cluster — Foundational canon]]
