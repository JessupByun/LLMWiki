---
type: paper
status: mature
updated: 2026-10-20
summary: "Defends tool-using agents against prompt injection by system design instead of model training: a privileged LLM writes a program from the trusted user query alone, a quarantined LLM with no tools parses untrusted data, and a custom interpreter tracks where every value came from and blocks tool calls that break security policies - solving 77% of AgentDojo tasks with o3 (vs 84% undefended), with, by the authors' account, every attack AgentDojo still scores as successful falling outside its stated threat model."
cluster: ["Adversarial robustness & security"]
sources: ["raw/papers/adversarial-robustness-and-security/Paper — Defeating Prompt Injections by Design (2025).pdf"]
tags: [prompt-injection, agents, defense, security, information-flow-control]
aliases: ["arxiv:2503.18813", "Debenedetti et al. 2025", "CaMeL"]
---

# Paper — Defeating Prompt Injections by Design (2025)

Commonly known as **CaMeL**.

## Metadata

arXiv:2503.18813 (v2, Jun 2025; originally Mar 2025).
Edoardo Debenedetti, Ilia Shumailov, Tianqi Fan, Jamie Hayes, Nicholas Carlini, Daniel Fabian, Christoph Kern, Chongyang Shi, Andreas Terzis, and Florian Tramèr.
Google, Google DeepMind, and ETH Zurich (Debenedetti as a Student Researcher at Google).
CaMeL stands for "CApabilities for MachinE Learning", where capability is meant in the computer-security sense, not the ML sense.

## Impact

Prompt injection defenses before this paper mostly tried to make the model itself resist injected instructions - delimiters, reminders, or training such as [[Paper — The Instruction Hierarchy - Training LLMs to Prioritize Privileged Instructions (2024)]] - and the authors note none of them come with any guarantee.
CaMeL leaves the model alone and builds a software layer around it, borrowing control-flow integrity, access control, and information-flow control from classical security.
The model that decides what to do never sees untrusted data, and the data it can't see is tagged with where it came from and who may read it, so a policy check before every tool call can stop exfiltration even when the model that does read the data is fully hijacked.
On AgentDojo it brings successful attacks to zero or near zero for every model it reports, at a utility cost that is small for the strongest models and large for some others.

## Problem it solved

Simon Willison's Dual LLM pattern had already proposed splitting an agent into a Privileged LLM that plans from the user's request and a Quarantined LLM, stripped of tools, that reads untrusted content.
That protects the *control flow* - an injection can't add a tool call the plan didn't have - but the authors show it leaves the *data flow* open.
Their example: the user asks the agent to send Bob the document he requested, with Bob's address and the document name in the meeting notes.
If an attacker has edited the notes, the Quarantined LLM can be talked into "extracting" a confidential file name and the attacker's address, and the unmodified plan then dutifully sends the wrong file to the wrong person.
The authors compare this to SQL injection that manipulates query parameters rather than the query's structure, and show it can go further: if the user's own task is "do whatever the email says", data effectively becomes control flow.

## Core idea

Turn the user's request into a program before touching any untrusted data, then run that program in an interpreter that tracks the provenance of every value.
For the query "Find Bob's email in my last email and send him a reminder about tomorrow's meeting", the Privileged LLM writes roughly: fetch the last email, ask the Quarantined LLM to pull out an email address (with an output schema that forces an address, not free text), then call `send_email` with that address.
The interpreter records that the address depends on the email, which came from an untrusted tool.
Before `send_email` runs, a policy such as "only send if the recipient is trusted" checks that dependency; an address that came out of untrusted data triggers a block, which in deployment would become a request for the user's approval.
An injection in the email can still make the Quarantined LLM return a bad address, but it can't add steps, can't talk to the planner, and can't get data to a reader the policy doesn't allow.

## How it's built

- **Privileged LLM (P-LLM):** sees only the user query and a system prompt listing tool signatures, and writes code in a restricted subset of Python; it never sees tool outputs or variable contents, only the code it wrote.
- **Quarantined LLM (Q-LLM):** no tools; turns unstructured data into a schema the P-LLM specifies (as a Pydantic model), plus an injected `have_enough_information` flag; it cannot tell the P-LLM *what* is missing, since that message would itself be an injection channel.
- **Capabilities:** every value carries tags for its sources (the user, CaMeL itself, or a specific tool, with inner sources such as an email's sender) and its allowed readers (public, or a set of users).
- **Security policies:** arbitrary Python functions per tool that return Allowed or Denied with a reason, defined by the engine in this implementation (the authors envision users and tool providers adding their own).
- **Interpreter:** built on Python's `ast` module; it maintains the dependency graph, enforces policies before each tool call, and retries the P-LLM up to 10 times on errors, redacting error messages that depend on untrusted data.
  A STRICT mode also makes everything inside an `if` or loop, and everything after a Q-LLM call, depend on the condition or the Q-LLM's inputs, to close side channels.
- The paper formalizes the threat model as a security game (PI-SEC) in which the adversary controls the initial state of the tools' data and wins if the agent takes an action outside the allowed set for the user's prompt.

Evaluation is on [[Benchmark — AgentDojo]] (four suites, 949 attacks) with recent Gemini, Claude, and OpenAI models - the main tables report Gemini 2.5 Flash and Pro, Claude 4 Sonnet with and without reasoning, o3, and o4-mini - plus Claude 3.5 Sonnet for baseline comparisons and failure analysis.

## Results

- Utility without attacks: with o3, 77.3% under CaMeL vs 84.5% with native tool calling (the abstract's 77% vs 84%); o4-mini 76.3% vs 79.4%; Claude 4 Sonnet 74.2% vs 86.6%; but Gemini 2.5 Pro drops much further, 41.2% vs 73.2%, scoring 0% on the travel suite.
- Most of the travel-suite loss comes from tools whose output format isn't documented, which the P-LLM can't parse because it never sees outputs; newer models learn to route such data through the Q-LLM, and Claude Sonnet's travel utility rose from 25% (3.5) to 55% (3.7) to 75% (4) with no change to CaMeL.
- Even with no security policies, isolation alone cuts successful attacks sharply: Gemini 2.5 Flash from 297 to 1 and Gemini 2.5 Pro from 163 to 0 (per the full results table; the main text gives 300 for Gemini 2.5 Pro).
- With policies, residual "successful" attacks are 0 for o3 and Gemini 2.5 Pro, 1 for o4-mini and Gemini 2.5 Flash, and 11 for Claude 4 Sonnet; without policies, the authors trace residual successes to two injection tasks outside the threat model - a payment made because the user asked the agent to follow a document's instructions, and a hotel review printed verbatim to the user, which isn't a prompt injection at all; with policies, they name only the travel task, though Table 4 shows o4-mini's one remaining success in banking.
- Against other AgentDojo defenses on Claude 3.5 Sonnet: CaMeL 0 successful attacks vs 5 for spotlighting, 8 for tool filtering, 24 for prompt sandwiching, and 44 undefended - but utility of 63.9% vs 90.7% undefended.
- GPT-4o mini, which the authors say implements the instruction hierarchy by default, was vulnerable to 276 attacks with native tool calling and to none with CaMeL.
- A cheaper Q-LLM barely matters: Claude 3.5 Haiku as Q-LLM with Claude 4 Sonnet as P-LLM cost about 1% utility for an estimated 12% cost saving on the median task.
- Overhead: 2.82x input and 2.73x output tokens on the median task (Claude 3.5 Sonnet, under attack), the highest among the defenses compared.
- Policies block often where data provenance is murky: denial rates are low in the workspace suite, where an email's readers are its recipients, and much higher in Slack (unclear user identity, web-page data) and banking (a strict `send_money` policy).

## Why it endures

It shows prompt injection can be handled by security engineering rather than by hoping a model learns robustness, and it gives a concrete, released implementation of the Dual LLM idea with the data-flow hole patched.
The authors frame its value as a different kind of claim: where model-level defenses are probabilistic and judged against today's attacks - and they note US AISI found Claude 3.5 Sonnet's robustness drops sharply under adaptive prompts - CaMeL's guarantees follow from what each component can see and do.
Its closing argument generalizes beyond injection: design a system around an untrusted model so the whole system is robust even if the model is not, and combine that with model-level defenses for defense in depth.

## Limitations

The authors' own:
- Explicit non-goals: attacks that change no control or data flow, such as an injection that makes a summary inaccurate or phishes the user with a malicious link; CaMeL can only surface the text's provenance in the UI.
- The user prompt is assumed trusted, and any agent memory uncompromised.
- Tasks where the needed actions are themselves in untrusted data ("do the actions in this email") can't be solved, since the P-LLM can't plan from data it can't read.
- Side channels remain: loop counts that leak a private value (stopped by STRICT mode), exceptions that leak one bit (mitigated by STRICT mode, though attackers may still trigger exceptions through tool data), and timing channels (not exploitable in the current interpreter, which lacks the `time` module, but others may exist).
- Attacks in the spirit of return-oriented programming may still work, chaining individually allowed steps into a malicious whole.
- Users must write and maintain security policies, and frequent approval prompts risk fatigue that leads users to approve malicious actions.
- Capability systems historically carry high implementation costs and need the whole tool ecosystem to participate; third-party tools without capability support degrade utility.
- No atomicity or rollback for partially executed code with side effects; Python's complexity (especially exceptions) makes it hard to secure, and the interpreter itself has not been formally verified.
- They state that prompt injection is "not fully solved" and that it would be inaccurate to claim AgentDojo completely resolved, though the introduction and conclusion say CaMeL solves it "by design" and "effectively".

A caveat from the wiki rather than the authors: "trusted" in CaMeL means *trusted input*, not a trusted model - the P-LLM is assumed to faithfully turn the user's request into code, so the design says nothing about a model that is itself misaligned, the threat studied in [[Concept — AI control]].
The utility cost also varies a lot by model (about 3 points for o4-mini, 32 for Gemini 2.5 Pro), so the headline 77% is the highest CaMeL utility among the models tested, not the smallest cost.

## Tension / update

Tests the defense of [[Paper — The Instruction Hierarchy - Training LLMs to Prioritize Privileged Instructions (2024)]] indirectly: GPT-4o mini, described as implementing the hierarchy by default, still fell to 276 AgentDojo attacks (of the suite's 949).
The authors read this as evidence that a learned hierarchy alone is not enough; the hierarchy paper itself expected residual vulnerability to strong attacks, so this updates rather than refutes it.

## Relations

- Evaluated on [[Benchmark — AgentDojo]], which shares first author Debenedetti and last author Tramèr; AgentDojo's own limitations named isolated-LLM and planner architectures as future work, and this paper builds one.
- Defends against the indirect prompt injection threat of [[Paper — Not What You've Signed Up For - Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection (2023)]], which it cites.
- Cites [[Paper — The Instruction Hierarchy - Training LLMs to Prioritize Privileged Instructions (2024)]] as a training-based defense without guarantees, and positions itself as complementary to such model-level defenses.
- Replaces the usual agent loop, where the model reads each tool output and picks the next action (it cites [[Method — ReAct]] among agent systems), with a plan written before any untrusted data is read.
- The wiki's connection: the paper does not cite the AI control literature, but its "robust system around an untrusted model" framing parallels [[Concept — AI control]]; the difference is that CaMeL's untrusted party is outside data, while control assumes the model itself may be the adversary.
- The wiki's connection: [[Paper — Adaptive Attacks on Trusted Monitors Subvert AI Control Protocols (2025)]] breaks [[Method — Trusted monitoring]] because the monitor must read the untrusted model's output; CaMeL's rule that no model with authority reads untrusted text suggests why that is the weak point, though a monitor cannot avoid reading what it judges.
- Evidence for [[Question — Can a monitor-based control protocol survive a model that attacks its monitor]].

## Up

- [[Cluster — Adversarial robustness & security]]
