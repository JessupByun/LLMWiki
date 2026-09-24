---
type: method
status: in-progress
updated: 2026-09-24
summary: "Adds two steps to ordinary mini-batch SGD to make the training process differentially private: clip each individual training example's gradient to a fixed L2 norm before averaging, then add Gaussian noise calibrated to that clipping bound - paired with the moments accountant, a tighter method for tracking accumulated privacy loss across many training steps than generic composition theorems provide."
cluster: ["ML security & privacy"]
sources: []
tags: [privacy, differential-privacy, dp-sgd, ml-security]
aliases: ["DP-SGD"]
---

# Method — Differentially Private SGD (DP-SGD)

## Definition

A differentially private variant of mini-batch stochastic gradient descent, introduced by [[Paper — Deep Learning with Differential Privacy (2016)]].
At each training step: **clip** every individual example's gradient (not just the batch average) to a fixed L2 norm bound C, which makes the *sensitivity* of the summed gradient to any single training example provably bounded regardless of how large that example's true gradient happened to be; then **add noise**, a Gaussian with standard deviation proportional to C, to the summed clipped gradients before averaging and taking the descent step - the actual source of the formal [[Concept — Differential privacy]] guarantee, following the standard recipe of calibrating additive noise to a function's worst-case sensitivity.

## Why it works

Clipping alone would make the mechanism deterministic and thus not private at all; noise alone, without clipping, would need to be calibrated to an unbounded worst-case sensitivity and would overwhelm the signal.
Together, clipping bounds *how much* noise needs to be added, and the noise is what actually makes any single training step differentially private.
The harder problem the paper solves is tracking accumulated privacy loss across the thousands of steps deep learning needs: naively composing each step's (epsilon, delta) guarantee via the standard strong composition theorem produces an overall epsilon far too large to be a meaningful guarantee.
The **moments accountant** instead tracks the log-moment-generating function of the privacy-loss random variable at each step - a finer-grained object than the (epsilon, delta) pair - and composes those moments by simple addition, converting the final accumulated bound back into a single (epsilon, delta) guarantee only once, at the end, which yields a substantially tighter final privacy cost for the same training run.

## Relations

- Introduced in [[Paper — Deep Learning with Differential Privacy (2016)]], operationalizing [[Concept — Differential privacy]] for deep, non-convex neural network training for the first time at a usable privacy budget.
- Provides the principled defense against [[Concept — Membership inference]] and [[Method — Shadow model attack]]: a model trained under DP-SGD is secure against these attacks by construction.

## Up

- [[Cluster — ML security & privacy]]
