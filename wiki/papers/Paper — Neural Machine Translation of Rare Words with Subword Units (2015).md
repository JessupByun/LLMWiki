---
type: paper
status: in-progress
updated: 2026-09-18
summary: "BPE - adapts the byte pair encoding compression algorithm to word segmentation, representing an open vocabulary as a compact, fixed set of merged subword units, letting neural translation models handle rare and unseen words without a back-off dictionary."
cluster: ["Foundational canon"]
sources: ["raw/papers/foundational-canon/Paper — Neural Machine Translation of Rare Words with Subword Units (2015).pdf"]
tags: [tokenization, nlp, foundational]
aliases: ["arxiv:1508.07909", "Sennrich et al. 2015", "BPE", "Byte Pair Encoding"]
---

# Paper — Neural Machine Translation of Rare Words with Subword Units (2015)

Commonly known as **BPE**.

## Metadata

- **arXiv:** 1508.07909 (v5, 2016-06-10)
- **Venue:** ACL 2016.
- **Authors:** Rico Sennrich, Barry Haddow, Alexandra Birch (University of Edinburgh).

## Impact

Neural machine translation models operate with a fixed vocabulary (typically 30,000-50,000 words), but translation is inherently an open-vocabulary problem - names, compounds, and morphologically complex words appear at test time that were never seen during training.
The prior fix was to fall back to a bilingual dictionary lookup for any unknown word, which breaks down whenever there isn't a clean 1-to-1 word correspondence between languages.
This paper's fix is to segment words into subword units small enough to be individually translatable - via character copying for names, compositional translation for compounds, and phonological transformation for cognates and loanwords - eliminating the need for a back-off dictionary entirely.
On the WMT15 English-German and English-Russian translation tasks, subword segmentation improved BLEU by up to 1.1 and 1.3 points respectively over a back-off dictionary baseline, with the largest gains concentrated exactly where expected: rare and previously unseen words.
The specific segmentation scheme introduced here, byte pair encoding (BPE), went on to become the near-universal tokenization method for large language models, including [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] (GPT-2) and its successors.

## Core idea

Byte Pair Encoding is originally a data-compression algorithm (Gage 1994) that iteratively replaces the most frequent pair of adjacent bytes in a sequence with a single new symbol.
This paper adapts it to word segmentation: start with a symbol vocabulary equal to the character alphabet (plus a special end-of-word marker), then repeatedly find the most frequent adjacent pair of symbols across the whole training corpus (weighted by word frequency) and merge it into a new symbol, for a fixed number of merge operations - the only hyperparameter of the algorithm.
The paper's own toy example, learned from the dictionary `{low, lowest, newer, wider}`, shows the first few merges building up recognizable pieces: `r · -> r·`, `l o -> lo`, `lo w -> low`, `e r· -> er·`.
Because frequent whole words and frequent morphemes both end up merged into single symbols while rare material stays split into smaller, still-meaningful pieces, the learned set of merge operations can be applied to *any* word at test time - including ones never seen during training - producing an open-vocabulary system from a fixed, compact symbol vocabulary with zero unknown tokens.
The paper motivates this with the German compound `Abwasserbehandlungsanlage` ("sewage water treatment plant"): a segmented, variable-length representation of its parts is intuitively more appealing than forcing the whole compound into one fixed-length word vector, and BPE lets a translation model learn to handle such compounds compositionally rather than treating each one as an opaque, likely-unseen symbol.

## Why it endures

BPE (and its close descendants, such as WordPiece and SentencePiece's segmentation modes) became the default tokenization scheme for essentially every large language model built since, because it requires no language-specific segmentation rules, guarantees a fixed and compact vocabulary with no unknown-token fallback, and lets a model productively compose and recognize words it never saw during training from familiar subword pieces.

## Limitations

The authors note BPE's segmentation is sometimes linguistically implausible - splitting `Forschungsinstituten` as `Forsch|ungsinstitu|ten` rather than the more sensible `Forschungs|instituten` - yet the translation model still learns to handle it correctly in most cases, showing the network is robust to imperfect segmentation rather than requiring linguistically clean splits.
When BPE is learned independently on the source and target languages rather than jointly, segmentation inconsistencies between the two vocabularies can produce transliteration errors (an example in the paper: `rakfisk` mistransliterated with a spurious inserted character), a problem the paper's "joint BPE" variant reduces but does not fully eliminate.

## Relations

- Method hub: [[Method — Byte Pair Encoding (BPE)]].
- Adapts the byte pair encoding data-compression algorithm (Gage 1994, not ingested) to the task of word segmentation.
- Runs on top of the attention-based encoder-decoder NMT architecture from [[Paper — Neural Machine Translation by Jointly Learning to Align and Translate (2014)]] (Bahdanau attention).
- The tokenization scheme adopted at scale by [[Paper — Language Models are Unsupervised Multitask Learners (2019)]] (GPT-2), which uses a 50,257-token byte-level BPE vocabulary, and by essentially every large language model since.

## Up

- [[Cluster — Foundational canon]]
