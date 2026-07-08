---
title: "GenRecon: Indoor Scene Reconstruction with Generative Priors"
subtitle: "3D Reconstruction · Generative Models"
description: "Reconstructing a complete, relightable PBR mesh of an indoor scene from a sparse set of photographs, by casting reconstruction as conditional 3D generation."
image: /assets/img/thumbs/genrecon.svg
order: 4
year: 2026
affiliation: Independent implementation study
methods: "Conditional 3D generation, structure-from-motion, LoRA fine-tuning, chunk-wise generation and registration"
tools: "Python, PyTorch, Trellis 2, COLMAP, VGGT, CUDA 13"
links:
  - title: "GenRecon paper (Schmid et al., arXiv)"
    url: https://arxiv.org/abs/2605.23888
---

## Overview

Classical multi-view reconstruction pipelines degrade sharply when photographs are sparse: unobserved surfaces produce holes, and photometric inconsistencies corrupt geometry. *GenRecon* (Schmid et al., 2026) addresses this by reframing scene reconstruction as **conditional 3D generation** — a generative prior fills in what the cameras did not see, while observed regions constrain the synthesis. This project is a clean, single-library re-implementation of the GenRecon method built on the Trellis 2 generative prior, capable of producing a complete, relightable, physically-based-rendering (PBR) mesh of an indoor room from a handful of casual photographs.

## Method

The pipeline proceeds in three stages. First, camera poses are recovered with COLMAP structure-from-motion, falling back to the feed-forward VGGT estimator when classical SfM fails to register the sparse views. Second, the scene is partitioned into overlapping chunks, and each chunk is synthesized by the Trellis 2 generative model conditioned on the posed observations; generating chunk-wise keeps each generation within the prior's native output volume while preserving local detail. Third, chunks are placed and merged into a single mesh using axis-aligned bounding-box overlap constraints followed by similarity-ICP registration, and the result is previewed under multiple HDRI environments to validate relightability.

Fine-tuning introduces only a small set of new parameters — a LoRA adapter over the prior, per-block 3D-conditioning injection layers, and an IBRNet-style feature aggregator (approximately 55.6 M new and 4.7 M LoRA parameters) — so the generative backbone itself remains frozen. Training progress is monitored on loss, IoU, and Chamfer-distance metrics.

A byproduct of this work was a substantial porting effort to run Trellis 2 on NVIDIA Blackwell-generation GPUs (sm_120) under CUDA 13, documented alongside the implementation.

## Results

<div class="placeholder-note">Placeholder — rendered results (input photo sets, reconstructed room meshes, and HDRI relighting previews) will be added here.</div>

## Reference

Schmid et al., *GenRecon: Bridging Generative Priors for Multi-View 3D Scene Reconstruction*, [arXiv:2605.23888](https://arxiv.org/abs/2605.23888).
