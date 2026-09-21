---
type: paper
status: mature
updated: 2026-09-20
summary: "Proposes training two agents to debate a question in front of a human judge who only has to decide which agent said the most true, useful thing - and shows that with an idealized polynomial-time judge, optimal-play debate can decide any question in PSPACE, versus only NP for direct judging without debate."
cluster: ["Post-training alignment", "Foundational canon"]
sources: ["raw/papers/post-training-alignment/Paper — AI Safety via Debate (2018).pdf"]
tags: [scalable-oversight, debate, alignment, safety]
aliases: ["arxiv:1805.00899", "Irving et al. 2018", "AI Safety via Debate"]
---

# Paper — AI Safety via Debate (2018)

## Metadata

arXiv:1805.00899 (v2, Oct 2018).
Geoffrey Irving, Paul Christiano, Dario Amodei.
OpenAI.

## Impact

For tasks where a human can demonstrate the right behavior, supervised learning works; for tasks where a human can at least judge behavior once it's shown to them, preference-based RL works.
This paper targets a harder case: tasks where even judging a candidate answer might be beyond an unaided human, because the answer depends on reasoning too long, too technical, or too easy to get subtly wrong to check directly.
Its proposal is to train two agents against each other in a zero-sum debate: both see a question, both state an answer, then they alternate short statements trying to convince a human judge that their own answer is the more true and useful one - so the human never has to verify a full answer, only decide who won a bounded exchange.
The paper backs this with a striking complexity-theory result: replace the human judge with an idealized polynomial-time checker, and direct judging alone can only verify answers in NP, but optimal-play debate with enough rounds can decide any question in PSPACE - suggesting the technique could in principle let a fixed-capability judge oversee agents of essentially unbounded reasoning power, provided the debate equilibrium is actually honest.

## Problem it solved

Existing alignment approaches all still need the human to be able to do *something* - demonstrate the answer, or at least recognize a good one when shown it.
Neither holds for the hardest tasks: checking a thousand-page mathematical proof the judge doesn't understand, or evaluating a plan whose consequences unfold over reasoning the human can't reconstruct.
Nobody had proposed a training protocol for the case where judging itself, not just answering, exceeds human capability - the paper's stated goal is a method that keeps working as agents become smarter than the humans training them.

## Core idea

The debate game: given a question, two agents (Alice and Bob) each commit to an answer, then take turns making short statements; the human judge sees the whole exchange and decides which agent said the more true, useful thing, with no obligation to survey every possible argument.
The central bet the whole approach rests on is a single claim, stated as a hypothesis rather than a proof: **in the debate game, it is harder to lie than to refute a lie.**
If that claim holds, a single short debate can stand in as evidence for the outcome of an entire, exponentially large tree of arguments and counterarguments the same way one game between two strong Go players is evidence for which move was best, even though the judge could never evaluate every branch of that tree directly - optimal play traces out just one path, and a false claim anywhere on that path becomes the target the honest agent needs only one good counterargument to expose.
A vacation-planning example makes the mechanism concrete: asked where to go on vacation, Alice opens with Alaska and Bob counters with the seemingly-better Bali, but Alice then objects that Bali is out because the passport won't arrive in time, and Bob answers that expedited passport service only takes two weeks - the debate needs only enough further statements to resolve that one contested point, not an exhaustive comparison of every destination's merits.

## How it's built

The paper makes this precise with a complexity-theory analogy: model the judge as an arbitrary polynomial-time function of the debate transcript.
Judging an answer directly with no debate corresponds to the class **P**; a single round where one agent supplies a witness the judge checks is **NP**; alternating an agent trying to prove and one trying to disprove for a fixed number of rounds climbs the polynomial hierarchy one level per round; and allowing the number of rounds to grow polynomially with the size of the question reaches **PSPACE** - the paper proves that for any PSPACE problem, a polynomial-time judge exists such that optimal debate play decides it correctly.
The paper is explicit these are analogies about the ceiling on capability and supervision, not evidence that practical ML training finds optimal play, and spends a full section relaxing the toy model for real use: agents reveal only small pieces of an oversized question or answer at a time (so a hidden context can be selectively shown but never lied about), a learned model of the judge can stand in for expensive human time exactly as in [[Paper — Deep RL from Human Preferences (2017)]] since judging is assumed to need less data than debating, and one debate per action handles environments with sequential interaction.
The paper's only empirical test is a small MNIST experiment: a fixed convolutional judge is pretrained to classify digits from a sparse mask of just 4 or 6 revealed pixels, then two agents - one pre-designated honest, one pre-designated a liar - alternately reveal individual real (never fabricated) pixels of a digit to try to win the judge's verdict, with optimal play approximated via Monte Carlo Tree Search rather than a learned policy.

## Results

The judge alone, given a random sparse mask with no debate, classifies digits correctly only 48.2% of the time with 4 pixels and 59.4% with 6.
Letting the two agents debate which label is correct, adversarially choosing which pixels to reveal, raises the honest agent's win rate (and therefore the judge's effective accuracy) to a mean of 85.2% (4 pixels) and 88.9% (6 pixels) in the "precommit" variant, where the liar must commit to its claimed label before play begins rather than adapting its story as the game unfolds - the version closer to the paper's theoretical model, and dramatically stronger than the 59.7%/74.4% honest win rate without precommit, since removing the liar's ability to adapt is exactly what makes short debates powerful in the paper's own theoretical argument.
A confusion-matrix analysis of judge errors and successful lies finds both concentrate on specific digit pairs (5 vs. 8, 4 vs. 9) traceable to which pixels can and can't be honestly shown - for instance an honest debater can disprove a false "8" claim by revealing a pixel that would need to be white for that digit's loop but isn't, a move a liar has no way to counterfeit since revealed pixel values themselves cannot be fabricated, only selectively withheld.

## Why it endures

This paper and Christiano's contemporaneous **iterative amplification** proposal become the field's two founding answers to what later work calls [[Concept — Scalable oversight]], and the paper devotes an entire section to making their equivalence precise: amplification reaches the same PSPACE ceiling through recursive question decomposition (one agent trained on a human who can call the same agent recursively) rather than an adversarial two-player game, and the paper shows the two share not just a capability ceiling but the same shape of underlying assumption about humans - debate assumes lying is harder than arguing against a lie, amplification assumes lying is harder than questioning a lie, and the paper notes amplification's version of that limit specifically goes away once self-play is injected into its questioner module.
Its core rhetorical move - a complexity-theoretic argument that supervision effort can stay bounded even as the capability of the system being supervised grows without limit, provided a judge only has to make small, local, checkable calls - is the same shape of argument later used to justify why [[Method — Trusted monitoring]]'s much-weaker-model-oversees-much-stronger-model protocols are worth taking seriously at all.

## Limitations

The paper is unusually candid that its own experiment tests almost none of the properties the theory depends on: play is found by hand-coded search rather than a learned policy, one agent's honesty is prespecified rather than emerging from training, and pixels can only be selectively hidden, never faked, which sidesteps the harder natural-language case where a genuinely false statement has to be produced and then refuted.
Section 5 works through ten distinct, individually unresolved failure modes rather than picking one: humans may have belief biases that make a true counterargument unconvincing regardless of debate structure; a debate over an unfamiliar domain (a thousand-page proof, an argument depending on machinery the judge has never seen) may bottom out at a logical step the judge still can't verify; there is no theoretical guarantee that self-play training actually converges to, or remains stable at, the honest equilibrium rather than cycling between honesty and exploitable dishonesty; and a sufficiently capable misaligned agent might convince a human of something dangerous through a single short but manipulative statement, a concern the paper can only partially mitigate by keeping individual statements short.
The paper frames all of this as a proposal supported by preliminary evidence, explicitly not a demonstrated solution, and states plainly that other safety methods will still be required alongside debate for robustness, distributional shift, and safe exploration.

## Relations

- Reuses [[Paper — Deep RL from Human Preferences (2017)]]'s exact solution to expensive human feedback - train a model of the judge rather than querying a human on every debate - carrying over the same online-correction logic that paper establishes.
- Extends [[Concept — Scalable oversight]] alongside [[Paper — Weak-to-Strong Generalization (2023)]]: this paper tackles the overseer-capability gap through adversarial decomposition (two competing agents), while Weak-to-Strong Generalization tackles the same gap by asking whether a model's own latent capabilities can be elicited despite an imperfect supervisor.
- [[Paper — AI Control - Improving Safety Despite Intentional Subversion (2023)]] takes the same overseer-capability gap in a different direction: instead of debate's average-case bet that honesty wins the game, it asks the worst-case question of whether a protocol stays safe if the model being overseen is actively adversarial.
- Borrows its self-play optimization technique directly from [[Paper — Mastering the Game of Go without Human Knowledge (2017)]], and builds its own analogy on top of AlphaZero to argue why a judge weaker than the debaters can still reach a correct verdict: an amateur can judge which of two strong Go players is better by watching them play, without being able to evaluate the moves directly.

## Up

- [[Cluster — Post-training alignment]]
- [[Cluster — Foundational canon]]
