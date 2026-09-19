---
type: paper
status: mature
updated: 2026-09-18
summary: "Prompts a language model to interleave free-form reasoning steps with actions against an external environment (like a Wikipedia search API), letting reasoning guide what to look up next and retrieved observations correct the reasoning."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — ReAct (2022).pdf"]
tags: [prompting, reasoning, agents]
aliases: ["arxiv:2210.03629", "Yao et al. 2022", "ReAct"]
---

# Paper — ReAct (2022)

Full title: **ReAct: Synergizing Reasoning and Acting in Language Models**.

## Metadata

arXiv:2210.03629.
Shunyu Yao, Jeffrey Zhao, Dian Yu, Nan Du, Izhak Shafran, Karthik Narasimhan, Yuan Cao.
Princeton University; Google Research, Brain Team.
ICLR 2023.

## Impact

[[Paper — Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022)]] (CoT) showed a model can reason step by step, but that reasoning is a closed box: it draws only on what the model already has memorized, so it hallucinates facts and can't update itself when it's wrong.
Separately, prior work had prompted models to take actions in an environment, but without any explicit reasoning to decide what to do next or interpret what came back.
This paper's move is to interleave the two: let the model alternate between generating a reasoning "thought" and taking an action (like searching Wikipedia), so each thought can decide what to look up next and each observation can correct or refine the reasoning that follows.
On question answering and fact verification, this cuts hallucination sharply (a 6% false-positive rate on correct-looking answers, versus 14% for pure chain-of-thought); on two interactive decision-making benchmarks (a text-based household game and a web-shopping environment), one- or two-shot ReAct prompting beats imitation- and reinforcement-learning agents trained on tens of thousands of task instances, by 34 and 10 percentage points respectively.

## Core idea

Give the model's action space a second kind of move alongside its normal actions: a "thought," which is just free-form text that changes nothing in the environment but gets added to the context for the model to reason over on the next step.
A thought can decompose a goal into subgoals, note what was just learned from an observation, decide the next thing to search for, or realize a plan has gone wrong and needs adjusting - the same kind of narration chain-of-thought produces, but now it's woven directly into a loop of real actions and real observations rather than running in isolation.
The concrete contrast the paper draws: shown a HotpotQA question about a device related to the Apple Remote, plain chain-of-thought confidently reasons to a wrong answer from memory alone, while ReAct reasons "I need to search Apple Remote," reads back what it actually says, revises its plan, searches again, and arrives at the correct answer - each step grounded in something just retrieved rather than recalled.

## Why it endures

ReAct's specific contribution, that reasoning and acting compound each other rather than needing to be studied separately, became the default shape for the "LLM agent" pattern: a loop of think-act-observe against tools, APIs, or environments, which is the architecture nearly every subsequent agentic system builds some variant of.
Because a ReAct trajectory is just plain text with readable thoughts interleaved with actions, a human can inspect why the model did something and even edit a thought mid-trajectory to redirect its behavior, a form of interpretability and control that a black-box policy doesn't offer.

## Limitations

The paper's own ablation names a real cost: the structural discipline of alternating reasoning and acting reduces the model's flexibility relative to pure chain-of-thought, leading to more reasoning errors in some cases (most notably the model getting stuck repeating the same thought-action loop rather than recognizing it isn't making progress).
Retrieval quality also directly bounds performance: a search that returns nothing useful derails the whole trajectory and the model has a hard time recovering, accounting for close to a quarter of its failures on the paper's own error analysis.

## Relations

- Directly extends [[Paper — Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022)]] (CoT) by interleaving its reasoning traces with real actions against an environment.
- Uses a Wikipedia search API as its external tool, functionally similar in spirit to the retrieval step in [[Paper — Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks (2020)]] (RAG), though ReAct's retrieval is explicitly reasoned over rather than automatically marginalized.
- See [[Method — ReAct]] for the reason-then-act loop as reused across later agentic LLM systems.

## Up

[[Cluster — Foundational canon]]
