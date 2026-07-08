---
title: "Automatic Text Summarization: A Literature Review"
subtitle: "Natural Language Understanding · Stanford XCS224U"
description: "A survey of nine papers in automatic text summarization, arguing that explicit information representation — not longer context alone — is the lever for further progress."
image: /assets/img/thumbs/nlu.svg
order: 7
year: 2023
affiliation: "Stanford Online, XCS224U — Natural Language Understanding (AI Professional Program)"
methods: "Literature review, comparative analysis of extractive, abstractive, and hybrid summarization"
tools: "PEGASUS, PRIMERA, Longformer, BART, knowledge graphs, ROUGE"
links:
  - title: Literature review (PDF)
    url: /assets/papers/xcs224u_lit_review.pdf
  - title: "PRIMERA (Xiao et al., 2022)"
    url: https://arxiv.org/abs/2110.08499
---

## Overview

Automatic Text Summarization (ATS) condenses a long document into a shorter one that preserves what matters. Deceptively simple to state, the task fragments on contact into a dozen subproblems: collecting source material, honouring user constraints on style and length, identifying relationships between spans ranging from words to whole documents, scoring or extracting the important portions, explicitly representing the assertions a source makes, generating the summary, checking it, and providing an audit trail back to the source.

This literature review was written for **XCS224U (Natural Language Understanding)** in Stanford Online's AI Professional Program, jointly with Alex Ezazi and Hamilton Link. It surveys nine papers organized around three lines of attack — handling more content, capitalizing on structure, and decomposing the workflow — and concludes with a thesis:

> We see an opportunity to develop more explicit information representations to augment summary generation as part of a modular pipeline.

## Handling more content

The first cluster confronts the transformer's context limit. **Liu et al. (2018)** treat Wikipedia article generation as multi-document summarization with a two-stage extractive-then-abstractive framework, and introduce a decoder-only *transformer with memory-compressed attention* (T-DMCA) that reduces the number of keys and values with strided convolutions, processing sequences three times longer. **Dong et al. (2021)** score sentences by position and sentence-to-section similarity (HipoRank), exploiting the reliable sectional structure of scientific articles while preserving original wording — which matters in legal and medical domains where paraphrase is unacceptable. **PEGASUS** (Zhang et al., 2020) pre-trains with *gap sentence generation*: rather than masking random words as BERT does, it masks whole salient sentences and learns to reconstruct them, transferring to unseen summarization datasets with as few as a thousand fine-tuning examples.

## Capitalizing on structure

The second cluster asks whether explicit structure can cure abstractive summarization's twin pathologies — summaries that are barely more than extractive, and summaries that fabricate. **Huang et al. (2020)** pair a sequential document encoder with a knowledge-graph encoder built from Stanford OpenIE (subject, predicate, object) triples, trained with a "multiple-choice cloze reward" to capture entity interactions. **Chen et al. (2023)** extend this, embedding the knowledge graph with a Graph Attention Network and linking those embeddings to text embeddings before a BART decoder. Notably, neither clearly beats a fine-tuned BART baseline — and both degrade sharply on the sparse Wikipedia corpus relative to dense news articles, raising the question of whether summarization is fundamentally a different task in different domains.

**PRIMERA** (Xiao et al., 2022) is the cluster's most interesting case. It also uses gap sentence generation, but selects those gaps with an *Entity Pyramid* — named-entity recognition standing in as a more reliable proxy for recurring assertions than today's brittle information-extraction algorithms. The result is an unsupervised foundational model for multi-document summarization requiring no reference summaries at all.

## Flexible workflows

The third cluster decomposes the task. **Miller (2019)** summarizes lecture transcripts extractively with no training whatsoever, embedding sentences and selecting those nearest k-means centroids — and finds, contrary to convention, that averaging the word-embedding matrix beats the `[CLS]` token as a sentence representation. **Pilault et al. (2020)** feed a reordered extractive summary into an abstractive transformer, demonstrating how sensitive the abstractor is to the quality of its extractive input. **Slobodkin et al. (2022)** isolate a genuinely new subtask, *Controlled Text Reduction*: given a document with user-highlighted spans, generate a summary containing all and only the highlighted facts. Their Longformer Encoder-Decoder, with global attention tied to the highlight tags, shows that different highlights measurably produce different summaries — and that access to the unhighlighted context remains necessary for coherence.

## Synthesis

Reading across the nine, a pattern emerges. Papers that exploit document structure — Pilault, Dong, and, reaching back, Edmundson (1969) — suggest that "in feature-enhanced models some form of extractive summary and structural information will remain valuable." Both PEGASUS and PRIMERA use gap sentence generation, but the former relies on simple sentence-overlap scores while the latter extracts more specific structured information; that contrast is precisely why we expect information extraction to eventually improve summarization demonstrably. Meanwhile, the repeated reliance on human evaluation via MTurk across these papers signals ROUGE's limitations, and by extension the difficulty of measuring exactly the content structure the field keeps reaching for.

The review closes by proposing four directions: architectures that ingest much longer sequences; techniques to capture document-level context; capturing entity relationships across long distances; and — the one that motivates the whole argument — providing **visibility and an "audit trail"** from the summary back to the source, so a reader can verify a claim or drill into detail.

## Paper

The full review is available [as a PDF](/assets/papers/xcs224u_lit_review.pdf).
