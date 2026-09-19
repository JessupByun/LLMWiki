---
type: paper
status: mature
updated: 2026-09-18
summary: "Trains a reward model on human labels for the correctness of each individual reasoning step, rather than just the final answer, and shows this process-supervised reward model finds correct solutions far more reliably than one trained on outcomes alone."
cluster: ["Post-training alignment", "Foundational canon"]
sources: ["raw/papers/post-training-alignment/Paper — Let's Verify Step by Step (2023).pdf"]
tags: [alignment, reward-modeling, scalable-oversight, reasoning, process-supervision]
aliases: ["arxiv:2305.20050", "Lightman et al. 2023", "PRM800K"]
---

# Paper — Let's Verify Step by Step (2023)

## Metadata

arXiv:2305.20050.
Hunter Lightman, Vineet Kosaraju, Yura Burda, Harri Edwards, Bowen Baker, Teddy Lee, Jan Leike, John Schulman, Ilya Sutskever, Karl Cobbe.
OpenAI.
Submitted May 2023.

## Impact

A reward model trained only on whether a solution's final answer is correct faces a hard credit-assignment problem: it has to infer, with no direct signal, which of many reasoning steps actually caused the failure, and on genuinely hard problems almost every sampled solution contains an error somewhere, so a bare correct/incorrect label carries very little information.
This paper trains a reward model directly on step-level correctness instead: human labelers mark each individual step in a solution as positive, negative, or neutral, and the resulting process-supervised reward model (PRM) is used to rank many sampled solutions and pick the best one.
On the MATH benchmark, the PRM solves 78.2% of a representative test subset using best-of-1860 search, versus 72.4% for an otherwise-identical outcome-supervised reward model (ORM) and 69.6% for majority voting - and the gap widens as more candidate solutions are searched over, not just at one fixed budget.
The paper also releases PRM800K, 800,000 step-level human correctness labels across 75,000 solutions, as a public resource for further work on process supervision.

## Problem it solved

Outcome-supervised reward models (ORMs), the standard approach used in prior work including [[Paper — Training LMs to Follow Instructions (2022)]] (InstructGPT)'s RLHF pipeline, only ever see a solution's final label: correct or incorrect.
This creates two distinct problems.
First, a purely outcome-based signal makes credit assignment genuinely hard: the reward model must generalize from "this whole multi-step solution was wrong" to "which specific step was the mistake," with no direct supervision on the latter.
Second, automatic outcome grading is fooled by false positives: solutions that reach the correct final answer through invalid reasoning still get graded correct, quietly teaching the reward model to reward bad reasoning that happens to land on the right number.
A prior closely related study (Uesato et al. 2022, not yet ingested) had already compared outcome and process supervision on grade-school math and found the two performed similarly - this paper revisits that comparison with a more capable base model (GPT-4), a harder benchmark (MATH instead of GSM8K), and roughly two orders of magnitude more human feedback, and finds the earlier near-tie does not hold at this scale.

## Core idea

Instead of asking a labeler "is this whole solution right or wrong," ask them to walk through it one step at a time and mark each step positive (correct and helpful), negative (incorrect or unreasonable), or neutral (technically valid but questionable), stopping as soon as the first negative step is reached.
The reward model is then trained as an ordinary next-token classifier: given a solution prefix ending right after some step, predict the probability that step is correct.
To score an entire candidate solution, multiply together the per-step correctness probabilities - one bad step anywhere in the chain sharply lowers the whole solution's score, which is exactly the precise, localized signal an outcome-only label can't provide.
At inference time, the reward model is used purely for search: sample many candidate solutions from a fixed generator model (never fine-tuned by the reward signal itself, deliberately kept out of scope for this paper), and pick whichever one the PRM scores highest.
A concrete illustration from the paper: two solutions to the same problem, one correct and one that goes wrong partway through, both light up green under the PRM for their early, valid steps, then the incorrect one turns red at the exact step where it introduces a difference-of-squares factorization that doesn't actually apply.

## How it's built

The generator is fine-tuned from GPT-4 to output solutions in a consistent newline-delimited step format, purely so individual steps can be parsed and labeled, not to teach it new reasoning ability.
Human labelers see model-generated solutions and label each step through an interface built for the task; data collection is made more efficient by actively surfacing "convincing wrong-answer" solutions, ones the current best PRM already rates highly despite being wrong, since these carry the most information about where the reward model is still mistaken.
This active-learning selection strategy is measured to be about 2.6x more data-efficient than uniformly sampling which solutions to label.
Because collecting human process-supervision labels at scale is prohibitively expensive to ablate freely, the paper also runs a second, complementary set of experiments where a large, already-trained PRM stands in as a synthetic labeler to supervise smaller reward models - letting the authors isolate exactly how much of the ORM-vs-PRM gap comes from the supervision type itself, independent of dataset size or labeling bias.

## Results

Best-of-1860 search on the MATH test subset: 78.2% for the PRM versus 72.4% for the ORM and 69.6% for majority voting, with the PRM's advantage over the ORM holding and widening across every search budget tested, not just at the largest one.
On a held-out set of 224 recent AP Calculus, AP Chemistry, AP Physics, and AMC exam problems, chosen specifically because they postdate the model's training data, the PRM still outperforms the ORM (72.9% vs 63.8% aggregate best-of-100), showing the advantage isn't an artifact of memorizing MATH-specific patterns.
The small-scale synthetic-supervision experiments isolate the effect cleanly: even holding dataset size and labeling source fixed, process supervision from the same synthetic labeler beats outcome supervision from that identical labeler, confirming the gain comes from the supervision granularity itself and not just from having more or cleaner data.

## Why it endures

The paper's central empirical claim, that supervising the reasoning process rather than only the final answer produces a measurably more reliable reward model, became a foundational result for the training methodology behind later reasoning-focused models that use process- or step-level reward signals during reinforcement learning, rather than a single reward at the end of a rollout.
Its release of PRM800K as an open dataset made process-supervision research reproducible outside of organizations with the resources to collect step-level human labels at scale.

## Alignment implications

The paper argues process supervision is not just more accurate but more alignment-relevant on its own terms: it directly rewards a reasoning process a human can inspect and endorse, rather than using a final outcome as an imperfect, gameable proxy for good reasoning - the same "reward the intent, not just an easily-measured stand-in for it" concern documented in [[Concept — Reward hacking]].
The authors frame their central finding, that safer, more interpretable supervision here *improves* rather than costs performance, as evidence against the usual assumption that alignment methods carry a performance tax; they call this a negative alignment tax and flag it as a reason process supervision might see wider adoption specifically because it stops being a tradeoff.

## Limitations

Collecting step-level human labels is far more expensive per solution than checking a final answer automatically, which is precisely why the paper needed its active-learning selection strategy and its synthetic-labeler experiments to make large-scale comparison feasible at all.
The comparison is conducted entirely within mathematical reasoning, where correctness is relatively well-defined and MATH problems mostly have automatically checkable final answers; the paper explicitly does not know how the process-versus-outcome gap generalizes to domains without that structure.
The paper also flags an unresolved test-set contamination risk from MATH problems potentially appearing in pretraining data, though it argues any such contamination should affect all methods compared roughly equally.

## Relations

- Directly revisits the outcome-vs-process comparison from Uesato et al. 2022 (not yet ingested) at greater scale, on a harder benchmark, with substantially more human feedback, and finds the earlier near-tie does not hold.
- Builds on the reward-model-plus-search methodology of Cobbe et al. 2021's outcome-supervised verifiers (not yet ingested), the direct predecessor of this paper's ORM baseline.
- Uses a generator fine-tuned to produce [[Method — Chain-of-Thought Prompting|chain-of-thought]]-style step-by-step solutions, from [[Paper — Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022)]].
- Its reward-model-plus-RL lineage traces back to [[Paper — Deep RL from Human Preferences (2017)]] and runs through [[Paper — Training LMs to Follow Instructions (2022)]] (InstructGPT), though this paper deliberately keeps the generator itself out of scope, focusing only on reward model training.
- Directly relevant to [[Concept — Reward hacking]]: process supervision is framed as a structural mitigation for reward models being gamed via an easily-measured but imperfect proxy (the final answer alone).
- See [[Method — Process Reward Model (PRM)]] for the mechanism as a candidate building block for later reinforcement-learning-on-reasoning systems.

## Up

[[Cluster — Post-training alignment]]
[[Cluster — Foundational canon]]
