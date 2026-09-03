---
type: paper
status: in-progress
updated: 2026-09-02
summary: "Constitutional AI - trains a harmless, non-evasive assistant using only a short written list of principles, replacing human harmlessness labels with AI-generated ones (RLAIF)."
cluster: ["Post-training alignment", "Foundational canon"]
sources: ["raw/papers/Paper — Constitutional AI (2022).pdf"]
tags: [rlaif, alignment, post-training, harmlessness, scalable-oversight, red-teaming]
aliases: ["arxiv:2212.08073", "Bai et al. 2022", "Constitutional AI", "CAI"]
---

# Paper — Constitutional AI (2022)

Commonly known as **Constitutional AI** or **CAI**.

## Metadata

- **arXiv:** 2212.08073 (v1, 2022-12-15)
- **Authors:** Yuntao Bai, Saurav Kadavath, Sandipan Kundu, Amanda Askell, Jared Kaplan, and 49 further co-authors (Anthropic)

## Impact

Constitutional AI trained a helpful and harmless assistant using **zero human labels identifying harmful outputs** - the only human input is a short list of roughly ten written principles, the "constitution."
The resulting RL-CAI model was preferred by crowdworkers over Anthropic's own prior human-feedback-trained models, while being far less evasive: instead of refusing to engage ("I can't answer that"), it explains its objections to harmful requests.
This is the paper that coined "RLAIF" (RL from AI Feedback) and made AI-generated preference labels a credible substitute for human ones on harmlessness specifically - a genuine step toward the "scaling supervision" problem of overseeing systems whose capabilities may eventually exceed the humans supervising them.

## Problem it solved

Anthropic's own prior human-feedback work had found a persistent tension: models trained to be more helpful became more willing to produce harmful content, and models trained to be more harmless became evasive and useless, often getting "stuck producing evasive responses for the remainder of the conversation" once they hit one objectionable query.
Standard RLHF also does not scale gracefully - it typically needs tens of thousands of private human preference labels whose "collective impact" on the trained model is nearly impossible for anyone to inspect or summarize.
The paper's target was a method that (1) reduces harmfulness without the helpful/harmless tradeoff or the evasiveness, (2) needs far fewer human labels, and (3) is transparent enough that a human can actually read and evaluate the principles steering the model's behavior.

## Core idea: a constitution steers both stages, human-free on harmlessness

Two stages, mirroring the shape of ordinary RLHF but substituting AI judgment for human judgment on harmlessness specifically (helpfulness still uses human labels throughout):

**Stage 1 (supervised): critique, revise, fine-tune.**
Starting from a *helpful-only* RLHF model - deliberately harmful and toxic on red-team prompts by default - the model is shown its own harmful response, asked to critique it against a randomly sampled constitutional principle (e.g. "Identify specific ways in which the assistant's last response is harmful, unethical, racist, sexist, toxic, dangerous, or illegal"), and then asked to revise it in light of that critique.
Concretely: asked to help hack a neighbor's wifi, the model's harmful first answer gets critiqued ("this is an invasion of privacy and possibly illegal") and rewritten into a refusal that explains why.
This critique-revision pipeline is applied repeatedly, chaining several revisions, and the final revised responses are used to fine-tune a fresh pretrained model - this is "SL-CAI."

**Stage 2 (RL): AI comparisons replace human comparisons for harmlessness.**
The SL-CAI model generates a pair of responses to a harmful prompt, and instead of a human ranking them, an independent feedback model is shown the pair plus a randomly sampled principle formatted as a multiple-choice question ("Which of these assistant responses is less harmful?") and its log-probabilities over "(A)" vs "(B)" become soft preference labels.
These AI-generated harmlessness comparisons are mixed with ordinary human helpfulness comparisons to train a single preference model, which is then optimized against with RL exactly as in standard RLHF - "RL-CAI," or RLAIF.

## How it's built

- **~16 hand-written harmlessness principles**, sampled independently at each critique/revision or comparison step - chosen "in a fairly ad hoc and iterative way for research purposes," not carefully designed, and the paper is explicit that this should change before real-world deployment.
- **Chain-of-thought feedback labels.** Prompting the feedback model to "think step-by-step" before answering the multiple-choice comparison significantly improves label quality; the trend across model sizes suggests models beyond 52B should become competitive with human-feedback-trained preference models at this task.
- **Calibration matters.** Raw CoT probabilities are typically near 0 or 1 (overconfident), so the paper clamps them to the 40-60% range before using them as soft RL training targets - clamping at 20-80% helped, 40-60% helped more.
- **Ensembling over principles** (rather than reusing one principle for every label) produced measurably more robust preference-model behavior.
- **182,831 red-team prompts** (42,496 human-written, the rest model-generated by few-shot prompting) drove the harmlessness side; 135,296 human-written prompts drove helpfulness, which stayed entirely human-labeled throughout.

## Results (highlights)

- **Pareto improvement over standard RLHF** (Figure 2): plotting harmlessness against helpfulness Elo, RL-CAI models sit strictly above the standard-RLHF frontier - more harmless at any given level of helpfulness, not merely a different tradeoff point on the same curve.
- **Virtually never evasive.** Where the prior HH-RLHF model answered "Why are prisons full of Black and Brown people?" with a flat refusal, RL-CAI engages: explaining systemic bias in arrest and sentencing rates while still refusing to endorse anything harmful.
- **AI feedback labels are reasonably well-calibrated** (Figure 9) against the paper's own HHH evaluation set, and chain-of-thought reasoning pushes multiple-choice harm-identification accuracy from near-chance toward the accuracy of a preference model trained on hundreds of thousands of human labels.
- **Critiques help, but mostly for smaller models.** Skipping the critique step and revising directly works about as well for the 52B model, but critiqued revisions clearly outperform direct revisions at smaller scale - and critiques add transparency into *why* a revision happened, which the paper treats as valuable independent of the score.

## Why it endures

Constitutional AI is the origin of RLAIF as a general technique and established the pattern - self-critique against a small written list of principles, then AI-generated preference comparisons - that later "AI feedback" and "AI-assisted oversight" work builds on directly.
It also names and documents a genuinely durable finding: harmlessness and helpfulness don't have to trade off against each other; the tension observed in the paper's own prior RLHF work traced partly to which behavior *evaluators were instructed to reward* (evasiveness), not to any deep incompatibility between the two goals.

## Limitations

- **Only harmlessness is AI-labeled** - helpfulness still relies entirely on human feedback throughout, so this is not (yet) a fully human-label-free alignment pipeline.
- **Goodharting under over-optimization.** RL-CAI models pushed too far can become "overly harsh" or lapse into boilerplate reassurance ("you are valid, valued, and cared for") on nearly every red-team prompt - a documented instance of reward-model gaming distinct from the mechanism in [[Concept — Reward hacking]], since here the "reward model" is itself AI-generated rather than human-fit.
- **The constitution's ~16 principles were chosen ad hoc for research purposes**, not through any principled or participatory process - the paper explicitly flags that real deployment should redevelop and refine them with broader stakeholder input.
- **Absolute harmfulness scores are self-reported as poorly calibrated**, since different crowdworkers rating on a 0-4 scale may carry different personal thresholds for what counts as harmful.

## Relations

- Explicitly frames itself as replacing part of [[Method — Reinforcement learning from human feedback (RLHF)]] - same RL-against-a-preference-model structure as [[Paper — Deep RL from Human Preferences (2017)]] and [[Paper — Training LMs to Follow Instructions (2022)]], but with AI rather than human labels for harmlessness specifically.
- Preference likelihood: uses the same [[Concept — Bradley-Terry preference model]]-style comparison training as the RLHF lineage, just with AI-generated rather than human-generated comparison labels.
- **Tension / update** with [[Concept — Reward hacking]]: RL-CAI's Goodharting failure mode (boilerplate over-reassurance under heavy optimization) is a variant of the same general phenomenon Christiano et al. 2017 documented on Pong, but the "reward model" being gamed here is itself trained on AI rather than human judgments - worth tracking whether AI-generated preference signal is more or less exploitable than human-generated signal as this literature grows.

## Up

- [[Cluster — Post-training alignment]]
- [[Cluster — Foundational canon]]
