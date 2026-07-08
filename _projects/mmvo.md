---
title: "MMVO: Visual-Inertial 3D Reconstruction in Challenging Industrial Environments"
subtitle: "Deep Learning for Computer Vision · Stanford CS231N"
description: "Making feed-forward 3D reconstruction survive construction sites: an IMU side network that corrects camera pose, and a diffusion refiner that sharpens depth — without fine-tuning the backbone or using LiDAR at inference."
image: /assets/img/mmvo/vggt-betterdepth-comparison.png
order: 1
year: 2026
affiliation: Stanford University, CS231N (group project)
methods: "Visual-inertial fusion, cross-attention conditioning, 6-DOF pose regression, conditional latent diffusion, point-cloud registration"
tools: "Python, PyTorch, VGGT, BetterDepth, Stable Diffusion 2, COLMAP, ROS, Hilti SLAM Challenge 2023"
links:
  - title: Final paper (PDF)
    url: /assets/papers/mmvo.pdf
  - title: "Milestone 1 — problem & setup"
    url: /assets/papers/mmvo-milestone-1.pdf
  - title: "Milestone 2 — architecture"
    url: /assets/papers/mmvo-milestone-2.pdf
  - title: "Milestone 3 — results"
    url: /assets/papers/mmvo-milestone-3.pdf
---

## Motivation

Digital twins of active construction sites depend on an accurate reconstruction of site geometry, yet construction sites are hostile terrain for modern feed-forward reconstruction models. [VGGT](https://arxiv.org/abs/2503.11651) — a state-of-the-art transformer that jointly infers camera parameters, depth maps, point maps, and 2D tracks in a single forward pass — was trained on scenes that look nothing like an underground level: faint illumination, repetitive surfaces, grayscale imagery, and an absence of clear visual landmarks. Under these conditions the model cannot register correspondences across views, and both its camera poses and its depth maps degrade badly.

Our hypothesis was that **inertial measurements could serve as a prior where image features are uninformative on their own**. Crucially, we constrained ourselves to interventions that require *no fine-tuning of the backbone on new images* and *no LiDAR at inference time*. We therefore decoupled reconstruction into two subproblems — camera pose and depth — and designed a separate intervention for each.

{% include figure.html path="/assets/img/mmvo/hilti-fisheye-frame.png" caption="A raw Alphasense fisheye frame from the Hilti SLAM Challenge 2023 dataset: grayscale, low-texture, high-contrast — everything a reconstruction model dislikes." %}

## Data and ground truth

Experiments use the [Hilti SLAM Challenge 2023](https://hilti-challenge.com/) platform: five synchronized fisheye cameras (720×540, 10 Hz) giving near-omnidirectional coverage, a co-mounted 6-DOF IMU at 200 Hz, and a 32-channel LiDAR at 10 Hz, recorded on real construction sites across floors, underground levels, and staircases.

Because the dataset provides no absolute camera poses for most frames, ground truth had to be constructed. We recover relative rotation and translation between LiDAR sweeps via point-cloud registration (RANSAC/ICP and KISS-ICP), treat the first frame as the world origin, and interpolate the resulting sparse SE(3) poses to the images' timestamps — translation linearly, rotation by SLERP. This step turned out to matter: residuals against four known absolute positions revealed roughly 4 m of cumulative drift, and the sparsity of the LiDAR sweeps makes the interpolated trajectory conspicuously linear. Much of what we later observed in the error analysis traces back to the quality of this surrogate rather than to the networks themselves.

{% include figure.html path="/assets/img/mmvo/ground-truth-registration.png" caption="Ground-truth construction: sweep-to-sweep registration yields sparse SE(3) poses, interpolated to image timestamps. From Milestone 2." %}

## Pose: conditioning camera tokens on inertial measurements

Rather than re-train VGGT, we attach a side network and keep the backbone frozen. IMU readings between consecutive image frames are collapsed into a single relative-motion factor by pre-integration, encoded by a 1-D CNN, projected into camera-token dimensions by an MLP, and passed through two to three transformer encoder blocks. A decoder then takes VGGT's camera tokens as **queries** and cross-attends them to the IMU representation as **keys and values**, producing corrected camera tokens. An MLP head projects these to a quaternion and a translation vector, which we convert to SE(3) for evaluation.

The two objectives — geodesic rotation loss and L2 translation loss — are combined by Kendall's homoscedastic multi-task weighting, so the network learns how much to trust each task rather than having the balance fixed by hand. No gradient flows back into VGGT.

{% include figure.html path="/assets/img/mmvo/pose-architecture.png" caption="The pose intervention: pre-integrated IMU tokens (K,V) cross-attended by VGGT's camera tokens (Q), with the backbone frozen. From Milestone 2." %}

{% include figure.html path="/assets/img/mmvo/pose-training-loss.png" caption="Forward and backward pass. The gradient is stopped at the VGGT backbone; only the fusion adapter and pose head are trained. From Milestone 2." %}

## Depth: a diffusion refiner over coarse predictions

For depth, the frozen VGGT produces a coarse depth map which a [BetterDepth](https://arxiv.org/abs/2407.17952)-style refiner sharpens through conditional latent diffusion. The Hilti image and the coarse depth map are each encoded by a frozen Stable Diffusion 2 VAE; their latents are concatenated with a noisy depth latent (4 + 4 + 4 = 12 channels) and denoised by a [Marigold](https://arxiv.org/abs/2312.02145)-initialized UNet trained under a v-prediction objective. Everything except the UNet stays frozen, and a frozen VAE decoder maps the denoised latent back to a refined depth map.

{% include figure.html path="/assets/img/mmvo/betterdepth-architecture.png" caption="The depth intervention: a trainable UNet refines VGGT's coarse depth by conditional latent diffusion, supervised against a synthesized dense depth surrogate." %}

## Results

**Pose.** Conditioning on inertial data removes VGGT's catastrophic failure on the peripheral cameras. VGGT's rotation error on cameras 2, 3, and 4 clusters around 107° — an artifact of the fisheye field of view colliding with the pinhole model VGGT assumes, compounded by the near-total absence of features shared between those views. Our network brings these to roughly 9–14°, while relative translation error falls to about 3 cm, well below the 13.2 cm of an IMU-only (VINS-Mono-style) baseline. The improvement in translation is uniform across cameras, unlike VGGT's, which varies sharply.

The honest caveat is that the [VINS-Mono](https://arxiv.org/abs/1708.03852) baseline still attains a *lower* mean relative rotation error — 1.7° against our 7.6°. We attribute this to the injection point of our intervention rather than to the inertial signal itself: the network treats each camera's rotation independently and never sees the rig's physical constraint that the five cameras are rigidly co-mounted. Cameras 0 and 1, whose fields of view overlap, converge to about 1.1°; the peripheral cameras, which share little, do not.

{% include figure.html path="/assets/img/mmvo/rotation-error-per-camera.png" caption="Per-camera rotation error. The forward-facing cameras 0 and 1 converge to ~1.1°; the peripheral cameras retain 10–15° error with high variance — the rig's geometry is never given to the network as a constraint. From Milestone 3." %}

{% include figure.html path="/assets/img/mmvo/baseline-comparison.png" caption="Validation comparison against VGGT across the five cameras: rotation error, translation error, and the resulting improvement ratios. From Milestone 3." %}

**Depth.** The diffusion refiner lowers per-frame Chamfer L2 from VGGT's 0.98 m² to 0.75 m². We report this as promising rather than conclusive: the dense ground truth is itself synthesized from sparse LiDAR, and its residual artifacts bound how far the refiner can be trusted.

{% include figure.html path="/assets/img/mmvo/vggt-betterdepth-comparison.png" caption="Depth refinement on held-out Hilti frames: input image, coarse VGGT depth, diffusion-refined VGGT + BetterDepth, and the dense ground-truth surrogate." %}

## Documents

The project was developed across three milestones, each of which is available in full:

- [**Milestone 1**](/assets/papers/mmvo-milestone-1.pdf) — problem motivation, hypothesis, the Hilti dataset and evaluation protocol, and a review of related work (VGGT, VINS-Mono, LiDAR-VGGT).
- [**Milestone 2**](/assets/papers/mmvo-milestone-2.pdf) — ground-truth construction by point-cloud registration, the fusion architecture, the multi-task loss, and the forward/backward pass.
- [**Milestone 3**](/assets/papers/mmvo-milestone-3.pdf) — training runs, the per-camera error decomposition, baseline comparisons, and qualitative trajectory analysis.

The final paper, *Visual-Inertial 3D Reconstruction in Challenging Industrial Environments*, is available [as a PDF](/assets/papers/mmvo.pdf).
