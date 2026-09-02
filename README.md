# LLMWiki

A personal, agent-maintained research wiki on AI security and safety.

The primary lens is alignment, post-training, RL, adversarial robustness, mechanistic interpretability, and evaluation / red-teaming of frontier systems, built on a foundational canon of landmark ML papers that earn their place whether or not they touch security directly.
Occasional papers outside both - kept purely because they're interesting - get a page too.

This is a reading memory and a thesis-building tool, not a survey or a textbook.
Pages are written for understanding and impact rather than completeness - each one leads with why the work mattered, in plain English.

## Repository scope

This repo publishes the prose layer only, so the work is readable by anyone.
It is not the working wiki, and it is not meant to be cloned or reproduced.

Left out on purpose:

- **`raw/`** - the source PDFs, kept local rather than redistributed here.
- **`.obsidian/`** - vault configuration, which is per-machine.
- **The agent schema and ingest log** - the instructions the maintaining agent runs on, and the append-only history of what was read when.

A clone won't reconstruct the wiki: `sources:` frontmatter points at local paths, and `[[wikilinks]]` render as plain text on GitHub instead of navigable links.
What travels is the writing itself.

## Reading it

Start at [`wiki/index.md`](wiki/index.md), which catalogs every page with a one-line summary.

The vault is authored in [Obsidian](https://obsidian.md), where the graph view renders the links between papers, concepts, and clusters.
On GitHub, read it as ordinary Markdown and follow the index.

## Layout

```
wiki/
  index.md        # Catalog of every page
  papers/         # One page per paper
  clusters/       # Thematic hubs
  concepts/       # Definitions and relations
  methods/        # Algorithms and techniques
  benchmarks/     # Eval suites and datasets
  repos/          # Code and checkpoints
  questions/      # Open research questions, with evidence attached
```

## Page types

| Type | Role |
|------|------|
| **Paper** | One canonical page per paper, keyed on arXiv id where one exists |
| **Cluster** | Thematic hub linking the papers, methods, and questions under a theme |
| **Concept** | Something you can define and point at - superposition, grokking, the Transformer architecture |
| **Method** | A procedure you can run - RLHF, DPO, GCG, activation patching |
| **Benchmark** | Eval suites (what they measure, and their limits) and datasets (what they contain, and how they were collected) |
| **Repo** | Code or checkpoints, pinned to a commit |
| **Question** | An open thread that accumulates evidence across papers - the one page type that is mine rather than the literature's |

Paper pages come in two tiers: papers whose primary cluster is topical get the full spine (impact, problem solved, core idea, how it's built, results, why it endures, limitations, relations); canon-only papers get a lighter treatment, since they're background rather than an active subject of study.
Every page ends with an `## Up` link to its cluster, so nothing is orphaned.

## Current state

Work in progress, permanently. `wiki/index.md` is always the current, authoritative count and contents.

## Sources

Every paper page carries its arXiv id or DOI in `aliases:` and its Metadata section, so the primary source is one lookup away even though the PDFs aren't here.
Every empirical or definitional claim is meant to trace to one of those sources - if something reads as unsourced, that's a bug.
