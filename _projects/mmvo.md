---
title: "MMVO: Visual-Inertial 3D Reconstruction in Challenging Industrial Environments"
subtitle: "Deep Learning for Computer Vision · Stanford CS231N"
description: "Making VGGT-based 3D reconstruction survive construction sites: an IMU side network for camera pose, and a diffusion refiner for depth."
image: /assets/img/mmvo/vggt-betterdepth-comparison.png
featured: true
order: 3
year: 2026
affiliation: Stanford University, CS231N (group project)
methods: "Visual-inertial fusion, cross-attention conditioning, 6-DOF pose regression, conditional latent diffusion for depth refinement"
tools: "Python, PyTorch, VGGT, BetterDepth, ROS, Hilti SLAM Challenge 2023 dataset"
---

## Overview

Digital twins of active construction sites require an accurate reconstruction of the site's geometry, but construction sites are hostile terrain for modern feed-forward reconstruction models: grayscale, low-texture, high-contrast imagery prevents [VGGT](https://arxiv.org/abs/2503.11651) from registering correspondences across views, degrading both its camera poses and its depth maps. This CS231N group project studies the factors driving that degradation and designs interventions that reduce reconstruction error **without fine-tuning on new images and without supplying LiDAR as input**, by decoupling the reconstruction problem into a camera-pose subproblem and a depth subproblem.

{% include figure.html path="/assets/img/mmvo/hilti-fisheye-frame.png" caption="A raw Alphasense fisheye frame from the Hilti SLAM Challenge 2023 dataset — grayscale, low-texture, high-contrast: everything a reconstruction model dislikes." %}

## Method

**Pose.** A side network — a fusion adapter composed of an IMU embedding mechanism and a stack of transformer encoders/decoders — conditions VGGT's camera tokens on inertial measurements through cross-attention and regresses 6-DOF poses as quaternion–translation pairs. This removes VGGT's catastrophic failure on the peripheral cameras and drives relative translation error to 3 cm, well below the 13.2 cm of an IMU-only baseline.

**Depth.** A frozen VGGT produces coarse depth maps, which a [BetterDepth](https://arxiv.org/abs/2407.17952)-style refiner enhances using conditional latent diffusion. The refinement lowers per-frame Chamfer L2 from 0.98 m² to 0.75 m² on held-out construction-site sequences.

{% include figure.html path="/assets/img/mmvo/vggt-betterdepth-comparison.png" caption="Depth refinement on Hilti frames: input image, coarse VGGT depth, diffusion-refined VGGT + BetterDepth, and the dense LiDAR-derived ground truth." %}

## Data

Experiments use the Hilti SLAM Challenge 2023 platform: five synchronized Alphasense fisheye cameras with near-omnidirectional coverage, a co-mounted IMU, and multi-channel LiDAR, recorded on active construction sites. A data-engineering pipeline extracts the ROS bag recordings into per-topic, timestamp-aligned frames, and the static laser scans are converted into a dense volumetric ground truth against which pose and depth predictions are evaluated.

## Status

<div class="placeholder-note">Placeholder — the final report PDF will be added here.</div>
