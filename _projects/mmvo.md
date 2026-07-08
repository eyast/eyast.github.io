---
title: "MMVO: Multi-Modal Visual-Odometry Diffusion Transformers for 3D Occupancy Prediction"
subtitle: "Deep Learning for Computer Vision · Stanford CS231N"
description: "Predicting streaming 3D occupancy maps from multi-camera fisheye imagery and inertial measurements with diffusion transformers."
image: /assets/img/thumbs/mmvo.svg
featured: true
order: 3
year: 2026
affiliation: Stanford University, CS231N (group project)
methods: "Diffusion transformers, visual odometry, multi-sensor fusion, 3D occupancy prediction"
tools: "Python, PyTorch, ROS, Hilti SLAM Challenge 2023 dataset"
---

## Overview

Robots operating in unstructured indoor environments require a persistent, metrically accurate estimate of which regions of space are occupied. MMVO investigates whether **diffusion transformers** — which have proven remarkably effective as generative priors over images and 3D structure — can be conditioned on multi-modal odometry signals to predict *streaming 3D occupancy maps*. The project was developed as a group project for Stanford's CS231N (Deep Learning for Computer Vision).

## Approach

The system consumes the sensor suite of the Hilti SLAM Challenge 2023 platform: five synchronized Alphasense fisheye cameras providing near-omnidirectional coverage, together with a co-mounted inertial measurement unit. A data-engineering pipeline extracts ROS bag recordings into per-topic, timestamp-aligned frames suitable for large-scale training. Conditioned on a short window of posed fisheye views and inertial history, a diffusion transformer is trained to denoise a voxelized occupancy volume — effectively fusing visual odometry and geometry completion into a single generative model. The formulation inherits a key advantage of generative approaches: occluded or never-observed regions receive plausible completions rather than being left unknown.

The accompanying literature review surveys three adjacent bodies of work — multi-view 3D reconstruction, fisheye-specific multi-view geometry, and the fusion of sequential imagery with IMU measurements for streaming occupancy mapping.

## Status

<div class="placeholder-note">Placeholder — the final report PDF, quantitative results, and qualitative occupancy visualizations will be added here.</div>
