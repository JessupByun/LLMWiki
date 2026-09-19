---
type: paper
status: in-progress
updated: 2026-09-18
summary: "LSTM - memory cells with multiplicative input/output gates and a constant error carrousel let recurrent networks bridge time lags of 1000+ steps, solving the vanishing/exploding gradient problem that made plain backpropagation through time unable to learn long-range dependencies."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Long Short-Term Memory (1997).pdf"]
tags: [architecture, recurrent-networks, sequence-modeling, foundational]
aliases: ["Hochreiter & Schmidhuber 1997", "LSTM"]
---

# Paper — Long Short-Term Memory (1997)

## Metadata

- **Venue:** Neural Computation 9(8):1735-1780, 1997.
- **Authors:** Sepp Hochreiter (Technische Universitat Munchen), Jurgen Schmidhuber (IDSIA).

## Impact

Solved a problem that had made recurrent networks impractical for anything but short sequences: [[Paper — Learning representations by back-propagating errors (1986)]]'s backpropagation, run backward through time in a recurrent network (BPTT), causes the error signal to either explode or vanish exponentially with the number of time steps, so a network cannot learn a dependency between events more than about 10 steps apart.
LSTM solves this directly with a new unit design, and could bridge time lags in excess of 1000 discrete time steps on tasks that Real-Time Recurrent Learning (RTRL), BPTT, and every other recurrent net algorithm the authors compared against failed to solve at all.
It became the dominant recurrent architecture for two decades of sequence modeling - speech recognition, machine translation, and the [[Paper — Sequence to Sequence Learning with Neural Networks (2014)]] / [[Paper — Neural Machine Translation by Jointly Learning to Align and Translate (2014)]] line of work this wiki's Transformer lineage builds directly on - until the Transformer displaced it in 2017.

## Core idea

The paper's own analysis (building on Hochreiter's 1991 thesis) pins down exactly why plain recurrent backpropagation fails over long time lags: at every time step the error signal gets multiplied by a weight and a derivative term, so after enough steps the accumulated product either shrinks toward zero (the gradient vanishes, so nothing distant is learned) or grows without bound (the gradient explodes, causing unstable weight updates).
LSTM's fix is a new kind of hidden unit called a **memory cell**, built around a self-connected linear unit with a fixed weight of exactly 1.0 on its own recurrent connection - the paper calls this the **constant error carrousel (CEC)**.
Because that self-connection's weight is fixed at 1.0 rather than learned, an error signal circulating inside the CEC is neither shrunk nor amplified no matter how many time steps it travels back through, which is precisely what breaks the vanishing/exploding pattern.
Left on its own, though, a cell that always remembers everything and never forgets is nearly as useless as one that remembers nothing - so each memory cell gets two learned **multiplicative gates**: an input gate that decides when new information is allowed to overwrite the cell's stored value, and an output gate that decides when the cell's stored value is allowed to affect the rest of the network.
The concrete task the paper builds to demonstrate this: a sequence where the very first symbol determines the correct answer, but that answer is only checked 1000 steps later, with hundreds of random distractor symbols in between - a task where any short-time-lag training signal is completely absent, so there is no way to learn it by generalizing from shorter examples.
LSTM solves it reliably; BPTT and RTRL cannot solve versions of this task with time lags past about 10 steps.

## Why it endures

The gate mechanism - multiplicative units that learn when to let information in or out, rather than fixed connections - reappears across nearly every architecture designed to control information flow over time or depth, from the simplified GRU cell to, in a different form, the residual "skip connection" gating intuition behind [[Paper — Deep Residual Learning for Image Recognition (2015)]].
For two decades LSTM was the default choice for any sequence task with long-range structure, until the Transformer's attention mechanism (2017) offered a way to look directly at any earlier position in a sequence without needing to carry information forward step by step at all.

## Limitations

The authors note their own truncated-backprop version of LSTM (used for computational efficiency) cannot easily solve tasks that are non-decomposable into an easier subgoal, such as computing the XOR of two widely separated inputs, since storing only one of the two inputs provides no partial credit toward the answer.
They also note LSTM has no special ability to count precise numbers of discrete time steps - it can distinguish "recent" from "distant" but not, without extra mechanism, the difference between an event 99 versus 100 steps ago.

## Relations

- Directly addresses the vanishing/exploding gradient problem inherent to running [[Paper — Learning representations by back-propagating errors (1986)]]'s backpropagation through time in a recurrent network.
- The base recurrent cell used by [[Paper — Sequence to Sequence Learning with Neural Networks (2014)]]'s encoder-decoder architecture and by [[Paper — Neural Machine Translation by Jointly Learning to Align and Translate (2014)]]'s attention mechanism, both published nearly two decades later.
- Superseded as the dominant sequence architecture by [[Paper — Attention Is All You Need (2017)]]'s Transformer, which replaces step-by-step recurrent state entirely with direct attention over all positions at once.

## Up

- [[Cluster — Foundational canon]]
