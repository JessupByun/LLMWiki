---
type: paper
status: in-progress
updated: 2026-08-07
summary: "Origin of RLHF - learn a reward model from human comparisons of short clips, then optimize it, cutting oversight cost by ~3 orders of magnitude."
cluster: ["Post-training alignment", "Foundational canon"]
sources: ["raw/papers/Paper — Deep RL from Human Preferences (2017).pdf"]
tags: [rlhf, alignment, reward-modeling, rl, preferences, scalable-oversight]
aliases: ["arxiv:1706.03741", "Christiano et al. 2017", "RLHF origin paper"]
---

# Paper — Deep RL from Human Preferences (2017)

## Metadata

- **arXiv:** 1706.03741 (v4, 2023-02-17)
- **Venue:** NIPS 2017. Note that no venue is printed anywhere in the PDF itself; confirmed externally via DBLP rather than from the source.
- **Authors:** Paul F Christiano (OpenAI), Jan Leike (DeepMind), Tom B Brown (no affiliation printed), Miljan Martic (DeepMind), Shane Legg (DeepMind), Dario Amodei (OpenAI).
- A cross-lab OpenAI/DeepMind collaboration, which is itself notable for 2017.

## Impact

This is the origin of RLHF, and the single most load-bearing ancestor of modern post-training.
Its contribution is not "use human feedback" but the specific move that made human feedback *affordable*: don't use humans as the reward signal, use them to train a **separate reward model**, then let RL optimize that model instead.
The authors claim this "reduce[s] the interaction complexity by roughly 3 orders of magnitude" (p10), which is what turned preference-based RL from a toy method into something applicable to state-of-the-art systems.
Every SFT → reward model → RL pipeline in current LLM alignment is a descendant of this loop.

## Problem it solved

Deep RL's wins had all come in domains with a well-specified reward function, like Atari scores and board games.
The authors' framing: to train a robot "to clean a table or scramble an egg... It's not clear how to construct a suitable reward function," and a hand-designed approximation "will often result in behavior that optimizes our reward function without actually satisfying our preferences" (p1).
They tie this directly to safety, citing Bostrom, Russell, and Amodei et al. on "misalignment between our values and the objectives of our RL systems" (p1).

Two obvious alternatives were already ruled out.
Imitation learning and IRL don't apply "to behaviors that are difficult for humans to demonstrate (such as controlling a robot with many degrees of freedom but very non-human morphology)" (p1).
And putting a human directly in the reward loop "is prohibitively expensive for RL systems that require hundreds or thousands of hours of experience" (p2).
Hence the real target: keep human intent as the objective, but cut the feedback required "by several orders of magnitude" (p2).

## Core idea: preferences over clips, not scores

The human never writes a reward number and never demonstrates the task.
They watch **two 1-2 second video clips** side by side and pick which one is closer to the goal, with the option to call a tie or say they can't tell (p5).
Incomparable answers get dropped from the dataset entirely.

The trick is treating the reward function as a **latent variable explaining those choices**.
The probability the human prefers clip 1 is modeled as depending exponentially on the summed latent reward over that clip, which is the **Bradley-Terry model** (p5), the same structure as an Elo rating: the difference in predicted reward between two segments estimates the probability a human picks one over the other.
The reward model is then fit by plain cross-entropy against the observed human labels.

Concretely, if the human consistently prefers clips where a simulated robot is upright and moving forward, gradient descent on that cross-entropy loss discovers a reward function that scores uprightness and forward motion highly, without anyone ever writing down "reward = 1.0 * forward_velocity".

Three processes then run **asynchronously** (p4), which is what makes the wall-clock work: the policy collects trajectories, clip pairs get sent for human comparison, and the reward model retrains on all comparisons so far. Updated reward parameters flow back to the policy continuously.

## How it's built

- **RL algorithms:** A2C for Atari, TRPO for the MuJoCo robotics tasks (p4). Policy-gradient methods were chosen deliberately because the learned reward "may be non-stationary, which leads us to prefer methods which are robust to changes in the reward function" (p4).
- **Ensembling:** the reward model is an ensemble (3 predictors by default), each trained on a bootstrap resample, outputs independently normalized then averaged (p5).
- **Query selection:** sample many candidate clip pairs, keep those where ensemble members disagree most (highest prediction variance). The authors are blunt that this is "a crude approximation" and that "in some tasks it actually impairs performance" (p6).
- **Modeling human error:** the likelihood assumes a flat 10% chance the human answers uniformly at random, since "human raters have a constant probability of making an error, which doesn't decay to 0" (p5).
- **Leakage control:** variable-length episodes were removed, and score displays blanked out on all seven Atari games, so that "human feedback provides its only guidance about what it ought to do" (p14, p15). MuJoCo torque penalties were dropped "Because torques are not directly visible to a human supervisor" (p14).

## Results (highlights)

- **Under 1% of interactions need feedback** (p1). MuJoCo: "With 700 labels we are able to nearly match reinforcement learning on all of these tasks" (p7). Note the paper says *labels*, not specifically human labels - the main MuJoCo run used 700 human queries against 350/700/1400 synthetic ones, and real human feedback is separately described as ranging "from being half as efficient as ground truth feedback to being equally efficient" (p7). Atari used 5,500 human queries (p7).
- **Human time is small.** Contractors answered the average query in 3-5 seconds, totalling 30 minutes to 5 hours per experiment (p6).
- **Preference-trained agents sometimes beat the hand-coded reward**, for three different reasons worth keeping distinct:
  - With 1,400 *synthetic* labels, performance slightly exceeded training on the true reward, "perhaps because the learned reward function is slightly better shaped" (p7).
  - On **Ant**, real human feedback beat the synthetic oracle, because asking humans to prefer "standing upright" was better reward shaping than the hand-crafted upright bonus (p7).
  - On **Enduro**, human labelers "tend to reward any progress towards passing cars," shaping past an exploration problem A3C couldn't solve alone (p8).
- **Novel behaviors with no reward function at all.** A Hopper backflip was trained from **900 queries in under an hour** (p8). Also one-legged Half-Cheetah locomotion (800 queries) and staying alongside traffic in Enduro (~1,300 queries).
- **Cost framing** (footnote 6, p11): ~$25 of compute vs ~5 hours of human labour, "at US minimum wage this totals ~$36."

## Why it endures

The architectural pattern - *learn a proxy for human judgment, then optimize the proxy* - is the template the entire LLM post-training stack inherited.
Swap trajectory clips for pairs of model responses and A2C/TRPO for PPO, and this is recognizably the RLHF stage of InstructGPT and everything after it.
The Bradley-Terry preference likelihood introduced here is also the exact object DPO later shows can be optimized without running RL at all.

## Limitations

- Learned rewards are less stable: "Training with learned reward functions tends to be less stable and higher variance" (p7).
- **A named failure:** on Qbert the method never learns to beat the first level with real human feedback, possibly because "short clips in Qbert can be confusing and difficult to evaluate" (p8). Breakout and SpaceInvaders never match RL either.
- **Every real-human curve is a single run**, against 5-run (MuJoCo) and 3-run (Atari) averages for synthetic labels (p7, p8). Two of the MuJoCo tasks were labeled by an author rather than a contractor, and all novel behaviors were trained on author feedback (p7, p9).
- Query selection by ensemble variance sometimes actively hurts (p6).
- **Diminishing returns already visible:** "we are already hitting diminishing returns on further sample-complexity improvements because the cost of compute is already comparable to the cost of non-expert feedback" (p10).
- Human-vs-synthetic gaps are partly a pipeline problem: labeler inconsistency and "uneven rate of labeling by contractors, which can cause labels to be overly concentrated in narrow parts of state space" (p8).

## Reward hacking evidence

Worth isolating, because it is the paper's most durable safety finding.
The ablation where the reward model is trained **offline** (fixed dataset, no fresh feedback) produces exactly the failure the alignment literature now expects: "the predictor captures only part of the true reward, and maximizing this partial reward can lead to bizarre behavior."
The concrete case: on Pong, the agent learns to avoid losing points but not to score them, "result[ing] in extremely long volleys that repeat the same sequence of events ad infinitum" (p9-10).
The authors' conclusion is a design principle rather than a curiosity: "in general human feedback needs to be intertwined with RL learning rather than provided statically" (p10).
See [[Concept — Reward hacking]].

## Relations

- Method hub: [[Method — Reinforcement learning from human feedback (RLHF)]].
- Preference likelihood: [[Concept — Bradley-Terry preference model]], the piece later post-training work reuses most directly.
- Failure mode it documents: [[Concept — Reward hacking]].
- Contemporary with [[Paper — Attention Is All You Need (2017)]]. The two 2017 papers sit on opposite sides of the modern stack: one is the architecture, this one is the alignment procedure eventually applied to it.

## Up

- [[Cluster — Post-training alignment]]
- [[Cluster — Foundational canon]]
