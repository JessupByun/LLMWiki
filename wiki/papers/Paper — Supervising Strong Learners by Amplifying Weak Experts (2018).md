---
type: paper
status: mature
updated: 2026-10-19
summary: "Proposes Iterated Amplification: train an agent on tasks too hard for a human to perform or judge directly by having the human answer each question with the help of several copies of the current agent, delegating subquestions to them, then training the agent to imitate that human-plus-copies team - so the training signal grows with the agent itself, with no external reward function - and shows it learns five algorithmic tasks nearly as fast as supervised learning from ground truth."
cluster: ["Scalable oversight"]
sources: ["raw/papers/scalable-oversight/Paper — Supervising Strong Learners by Amplifying Weak Experts (2018).pdf"]
tags: [scalable-oversight, iterated-amplification, alignment, decomposition]
aliases: ["arxiv:1810.08575", "Christiano et al. 2018", "Iterated Amplification", "IDA"]
---

# Paper — Supervising Strong Learners by Amplifying Weak Experts (2018)

Commonly known as **Iterated Amplification**.

## Metadata

arXiv:1810.08575 (v1, Oct 2018).
Paul Christiano and Dario Amodei (OpenAI), and Buck Shlegeris (work done while at OpenAI).

## Impact

Supervised learning needs a human who can perform the task; RLHF needs a human who can at least judge it.
For tasks beyond that - setting economic policy, securing a large computer network, advancing a research frontier - there was no general way to get a training signal at all, and the usual fallback of optimizing a measurable short-term proxy invites exactly the Goodhart failures [[Concept — Reward hacking]] describes.
This paper proposes building the training signal up recursively: a human who can't answer a hard question can still break it into easier subquestions and delegate those to copies of the agent being trained, and the agent then learns to imitate the resulting team.
Alongside [[Paper — AI Safety via Debate (2018)]], it is one of the founding scalable-oversight proposals, alongside recursive reward modeling - and debate's own paper spends a section arguing the two are approximately equivalent, matching two definitions of PSPACE.

## Problem it solved

The paper lays out a grid of training-signal sources - algorithmic (like a game's win condition), human (demonstrations or judgments), and nothing - against supervised and reinforcement learning.
Most machine learning lives in the boxes where an algorithm or a human can supply the signal; for tasks "beyond human scale," where no single human can perform the task or make sense of the agent's behavior, there seemed to be no general method.

## Core idea

Write **Amplify^H(X)** for a human H working together with several copies of the current agent X.
To answer a question, H identifies a useful subquestion, has a copy of X answer it, repeats a few times, and then combines the subanswers into a final answer - so the team can answer slightly harder questions than X can alone.
X is then trained, by supervised learning, to predict what that team would answer.
At the start X is useless, so the team is effectively just H; as X improves, the team improves, and X chases that moving target, ideally ending up approximating an exponentially large team of copies of H, each handling one small piece.
Crucially there is no external objective: what counts as a good answer is defined only implicitly by how the human decomposes questions and recombines answers, so the agent learns the goal at the same time as it learns to be competent.
The paper calls this very similar to Expert Iteration and AlphaZero - where a search procedure improves on the network and the network learns to imitate the search - except that the improvement step comes from human decomposition rather than search against a known reward.

## How it's built

To keep human cost down, a separate model H′ learns to imitate the human's decomposition decisions, and H′ rather than the human generates most of the training data; because H′ only learns to ask subquestions and combine answers rather than solve whole tasks, it needs far less data.
The agent is a Transformer encoder-decoder over large contexts (sets of facts), with a copy mechanism; many questions about the same context share one encoding pass, speeding training by about an order of magnitude.
Experiments use five algorithmic tasks over large combinatorial contexts - powering a permutation, evaluating variables in a sequence of assignments, summing a function over a wildcard pattern, shortest paths in a graph, and finding roots in a forest - with a hard-coded decomposition algorithm standing in for the human.

## Results

Iterated Amplification learns all five tasks with at most a modest slowdown relative to supervised learning on ground-truth answers, at about twice the computation per question.
The more striking number is sample complexity of the expert: supervised learning needed tens of millions of ground-truth examples, while amplification needed only tens of thousands of queries to the decomposition oracle, because learning to decompose is much easier than learning to solve.

## Why it endures

It established decomposition as one of the field's main strategies for oversight beyond human ability - the bet that helping a human break a problem down scales better than asking a human to judge the final answer.
The paper's own argument for why decomposition is plausible in the real world is that coordinating copies of one agent is easier than coordinating human teams: the collaboration need not be efficient, only helpful at all; copies can work sequentially; and there are no conflicting incentives to manage.
Its framing of the oversight problem - expanding the set of tasks with any trustworthy training signal - is the same problem [[Concept — Scalable oversight]] and every later paper in [[Cluster — Scalable oversight]] address.

## Limitations

The experiments replace the human with a hand-coded decomposition algorithm on tasks that are algorithmically decomposable and already have an algorithmic training signal, so they say nothing about whether humans can decompose interesting real-world tasks or whether messy decompositions can be learned.
The agent is trained only by supervised imitation; the authors expect real applications would learn a reward function from the amplified team and optimize it.
The question distribution must cover every subquestion the decomposition will ask, which a system designer would have to construct carefully.
The method assumes that a human coordinating copies of the agent outperforms a single copy - the key assumption the authors argue for but do not test outside toy domains.

## Relations

- The companion proposal to [[Paper — AI Safety via Debate (2018)]]: both use a recursive structure where AI systems help a human handle subquestions, but here each subquestion goes to an independent copy of the agent, whereas in debate it goes to an adversary trained to defend a particular answer; the debate paper shows both reach the same PSPACE ceiling under idealized assumptions.
- Its training loop mirrors Expert Iteration and [[Paper — Mastering the Game of Go without Human Knowledge (2017)]]'s self-play, minus the external reward.
- Motivated by the proxy-optimization failures named in [[Paper — Concrete Problems in AI Safety (2016)]] and [[Concept — Reward hacking]], and builds on the learned-reward approach of [[Paper — Deep RL from Human Preferences (2017)]] for tasks where human judgment still works.
- Its human-predictor H′ - a model of the human trained to stand in for expensive human queries - is the same move debate makes with its learned judge.
- Later empirical scalable-oversight work, including [[Paper — Measuring Progress on Scalable Oversight for Large Language Models (2022)]], lists amplification among the techniques its sandwiching paradigm is designed to test.

## Up

- [[Cluster — Scalable oversight]]
