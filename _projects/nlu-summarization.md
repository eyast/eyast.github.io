---
title: "Multi-Document Summarization of Academic Literature Reviews"
subtitle: "Natural Language Understanding · Stanford XCS224U"
description: "A study of abstractive multi-document summarization over a corpus of academic literature reviews, building on pyramid-based pre-training."
image: /assets/img/thumbs/nlu.svg
order: 5
year: 2023
affiliation: Stanford XCS224U final project
methods: "Multi-document summarization, corpus construction, PDF information extraction, transformer fine-tuning"
tools: "Python, pdfminer, Hugging Face Transformers, PRIMERA"
---

## Overview

Literature reviews are themselves summaries — each one condenses a set of source papers into a structured narrative. This final project for Stanford's XCS224U (Natural Language Understanding) treats that property as an experimental resource: a corpus of approximately fifty past student submissions, each comprising a literature review, an experiment protocol, and a final paper, is used to study **abstractive multi-document summarization** — the task of generating a single coherent summary from multiple related source documents.

## System

The first contribution is a corpus-construction pipeline. Submission PDFs are parsed with `pdfminer`, author names are anonymized, and document content is stored hierarchically by section heading with stable unique identifiers; the papers reviewed by each submission are then resolved and retrieved, pairing source papers with the reviews that discuss them. The modeling work builds on [PRIMERA](https://github.com/eyast/PRIMER), a pre-trained multi-document summarization model that uses pyramid-based masked-sentence pre-training to select and aggregate salient information across documents.

## Paper

<div class="placeholder-note">Placeholder — the final paper PDF and its abstract will be added here.</div>
