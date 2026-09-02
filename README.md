# LLMWiki

A personal, agent-maintained research wiki on AI security and safety.

The primary lens is alignment, mechanistic interpretability, post-training, RL, adversarial robustness, and evaluation / red-teaming of frontier systems.
That lens sits on top of a foundational canon: landmark papers spanning all of ML that earn a place as load-bearing background, whether or not they touch security directly.

This is a reading memory and a thesis-building tool, not a survey or a textbook.
Pages are written for understanding and impact rather than completeness - each one leads with why the work mattered, in plain English.

## What this repo is

A published snapshot of the prose layer, so the work is readable by anyone.
It is not the working wiki, and it is not meant to be cloned or reproduced.

The live vault has three things this repo deliberately leaves out:

- **`raw/`** - the source PDFs each page was written from.
  They stay local rather than being redistributed here.
- **`.obsidian/`** - vault configuration, which is per-machine.
- **The agent schema and ingest log** - the instructions the maintaining agent runs on, and the append-only timeline of what was read when.

So a clone will not reconstruct the wiki.
The `sources:` frontmatter on each page points at local paths that exist only on my machine, and `[[wikilinks]]` render as plain text on GitHub rather than as navigable links.
What travels is the writing itself, which is the part meant to be read.

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

Paper pages come in two tiers.
Papers whose primary cluster is topical get the full spine: impact, the problem it solved, the core idea, how it is built, results, why it endures, limitations, and relations.
Canon-only papers get a lighter treatment, since they are background rather than an active subject of study.

Every page ends with an `## Up` link to its cluster, so nothing is orphaned.

## Current state

Three clusters: foundational canon, post-training alignment, and adversarial robustness & security.
Five papers, seven concepts, and two methods.
Mechanistic interpretability and evaluation / red-teaming are named pillars with no papers in them yet.

`wiki/index.md` is the source of truth; this section will drift.

## Sources

Every paper page carries its arXiv id or DOI in `aliases:` and in its Metadata section, so the primary source is one lookup away even though the PDFs are not here.
Every empirical or definitional claim is meant to trace to one of those sources - if something reads as unsourced, that is a bug.
