# UGRP_SAVTON

Size-aware Virtual Try-On using Diffusion Model

DGIST 2024 UGRP Project

## Overview

This project explores a size-aware virtual try-on method based on a diffusion model.

Most virtual try-on models focus on generating realistic clothing images, but they do not explicitly control clothing size. In this project, we explored how to reflect clothing size changes by adjusting warping ratios and adding segmentation map conditioning to the model input.

## Goal

The goal of this project was to build a virtual try-on model that can reflect clothing size information in the generated result.

We focused on the following questions:

- Can clothing size be controlled through input conditioning?
- Can segmentation maps help the model generate different clothing silhouettes?
- Can warping-based size adjustment produce visually meaningful changes?

## What We Changed

We extended a diffusion-based virtual try-on pipeline with additional conditioning inputs.

Main changes:

- Added segmentation map as an additional condition
- Modified the first U-Net input layer to accept expanded input channels
- Adjusted warped cloth ratio to test size changes
- Compared output changes through qualitative and quantitative evaluation

## My Contribution

I mainly worked on the following parts:

- Warping module experiment for clothing size adjustment
- Segmentation map based input manipulation
- Input structure modification for size-aware conditioning
- Result comparison and qualitative analysis

## Method

### 1. Warping Ratio Control

We changed the warping ratio of the clothing image to test whether sleeve length and overall clothing silhouette could be adjusted.

### 2. Segmentation Map Conditioning

We modified the upper-clothing region in the segmentation map using dilation and erosion.

- Target region: upper clothes
- Kernel size: 30 x 30
- Operations: dilation and erosion

The modified segmentation map was used as an additional condition during inference.

### 3. Input Layer Expansion

To include the segmentation map in the model input, we expanded the first convolution layer of the U-Net. Newly added channel weights were initialized to zero to preserve the original pretrained knowledge as much as possible.

## Experiment Setup

- Dataset: VITON-HD
- Train samples: 11,647
- Test samples: 2,032
- Resolution: 1024 x 768
- GPU: RTX A6000
- Training iterations: 300k
- Batch size: 16

## Results

### Quantitative Results

- LPIPS: 0.129
- SSIM: 0.867
- FID (paired): 10.86
- FID (unpaired): 15.11

### Qualitative Findings

- Changing the warping ratio affected sleeve length and clothing silhouette
- Segmentation map conditioning improved clothing expansion behavior
- Clothing boundaries became clearer in some cases
- Some body-part cutting errors were reduced

## Discussion

This project was not mainly about achieving the best benchmark score.

Its main value was showing that diffusion-based virtual try-on can be extended toward size-aware generation through input conditioning and warping control.

### What I Learned

- How to modify diffusion model inputs for conditional generation
- How segmentation maps can influence image generation results
- How to evaluate both quantitative metrics and qualitative outputs
- How to test model behavior beyond standard benchmark settings

## Award

- DGIST 2024 UGRP 우수상

## Keywords

Diffusion, Virtual Try-On, VTON, Stable Diffusion, Segmentation Map, Warping, Computer Vision
