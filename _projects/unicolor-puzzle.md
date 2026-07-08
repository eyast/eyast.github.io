---
title: "Shape-Only Assembly of a Unicolor 2,000-Piece Jigsaw Puzzle"
subtitle: "Computer Vision · Combinatorial Search"
description: "A study in constraint satisfaction under minimal, noisy information: solving a single-color jigsaw puzzle using only the geometry of its edges."
image: /assets/img/puzzle_final/thumbnail.jpg
order: 3
year: 2022
affiliation: Independent study
methods: "Contour detection, feature engineering, conditional probability, k-means clustering"
tools: "Python, OpenCV, NumPy, SymPy, scikit-learn, multiprocessing, cProfile"
links:
  - title: Dataset on Kaggle
    url: https://www.kaggle.com/datasets/etaifour/jigsawpuzzle
  - title: Code on GitHub
    url: https://github.com/eyast/puzzle-solver
---

## Abstract

This project investigates a class of large constraint-satisfaction problems in which the available information is minimal and noisy — a setting of practical relevance to operations research and scheduling. As a physical testbed, I consider the assembly of a custom-manufactured 2,000-piece jigsaw puzzle printed in a single, uniform color. With no pictorial information whatsoever, assembly must rely exclusively on the geometry of each piece's edges. The complete corpus of scanned pieces has been released as an open dataset on [Kaggle](https://www.kaggle.com/datasets/etaifour/jigsawpuzzle) (CC BY-SA 3.0), and the solver implementation is available on [GitHub](https://github.com/eyast/puzzle-solver).

## Data Acquisition

A rigorous digitization protocol was required before any computation could begin. Each piece was numbered sequentially with a 0.2 mm pen and annotated with a horizontal reference stroke to disambiguate orientation (distinguishing, for instance, a "9" from an inverted "6"). Pieces were archived in a twenty-drawer cabinet in batches of one hundred, then scanned face-down on a flatbed scanner in a darkened room — ambient light was eliminated so that only the scanner's own source illuminated the piece surfaces, suppressing depth artifacts. The result is a set of single-channel scans covering all 2,000 pieces.

<div class="figure-row">
{% include figure.html path="/assets/img/puzzle_final/01_left_box_drawers.jpg" caption="Physical indexing: a twenty-drawer cabinet, one hundred pieces per drawer." %}
{% include figure.html path="/assets/img/puzzle_final/02_scanning.jpg" caption="Batch scanning on a flatbed scanner in a darkened room." %}
{% include figure.html path="/assets/img/puzzle_final/03_results.jpg" caption="A representative scan of one batch." %}
</div>

## Segmentation and Edge Extraction

Piece candidates were segmented with `cv2.connectedComponents`; because handwritten indices and scanner dust produce many spurious components, the large piece-scale components were separated from noise by clustering component areas with k-means (k = 2), avoiding hard-coded thresholds. For every extracted piece, corner hypotheses were initialized as the contour points nearest the bounding-box corners and subsequently refined through a purpose-built OpenCV annotation interface. Each piece was then decomposed into four directed edges (top, right, bottom, left), which were rotated to a canonical horizontal frame using symbolic geometry (SymPy) so that all edges became directly comparable.

{% include figure.html path="/assets/img/puzzle_final/04_scanned_results.jpg" caption="Extracted contours after segmentation." %}

## Feature Design and Matching

Several edge descriptors were evaluated. Counterintuitively, the least expressive descriptor proved the most reliable: a three-dimensional vector comprising edge width, height, and the abscissa of the edge's highest point. Pairwise distances between all edges were computed in a vectorized loop distributed across cores with Python's `multiprocessing`, and profiled with `cProfile` to locate bottlenecks.

## Probabilistic Assembly

Assembly proceeded as a belief-updating process grounded in conditional probability. Border and corner pieces — which satisfy the fewest constraints and impose the strongest ones on their neighbors — were placed first; thereafter, candidate pieces were ranked by their posterior compatibility with all adjacent, already-placed edges, with beliefs revised after each placement. As the frontier of placed pieces grew, the constraint network sharpened and recommendation error declined markedly.

<div class="figure-row">
{% include figure.html path="/assets/img/puzzle_final/07.jpg" caption="Border-first assembly: edge pieces impose the strongest constraints." %}
{% include figure.html path="/assets/img/puzzle_final/08.jpg" caption="Interior placement guided by conditional probability." %}
</div>

{% include figure.html path="/assets/img/puzzle_final/final.jpg" caption="The assembled puzzle — 1,999 of 2,000 pieces (one piece was lost in handling)." %}

## Artifacts

- **Dataset** — raw flatbed scans of all numbered pieces: [kaggle.com/datasets/etaifour/jigsawpuzzle](https://www.kaggle.com/datasets/etaifour/jigsawpuzzle)
- **Code** — segmentation, feature extraction, and solver notebooks: [github.com/eyast/puzzle-solver](https://github.com/eyast/puzzle-solver)
