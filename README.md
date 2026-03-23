# UGRP_SAVTON

Size-aware Virtual Try-On using Diffusion Model  
DGIST 2024 UGRP Project

## Overview

온라인 의류 구매에서는 실제 착용감과 사이즈를 확인하기 어렵다는 문제가 있습니다.  
기존 Virtual Try-On(VTON) 연구들은 의류를 자연스럽게 입혀주는 데 집중했지만, 사용자의 체형이나 옷의 크기 차이를 명시적으로 반영하는 데에는 한계가 있었습니다.

이 프로젝트는 **Diffusion 기반 Virtual Try-On 모델에 사이즈 정보를 반영할 수 있는 방법**을 탐구한 연구입니다.  
의류 warping 비율과 segmentation map conditioning을 활용해, 생성 결과에서 의상의 크기와 실루엣이 실제로 변화하는지를 실험했습니다. :contentReference[oaicite:5]{index=5} :contentReference[oaicite:6]{index=6}

---

## Goal

이 연구의 목표는 다음과 같습니다.

- 사이즈를 조건으로 반영할 수 있는 Virtual Try-On 모델 설계
- 입력 변형에 따라 생성 결과가 어떻게 달라지는지 검증
- segmentation map을 추가 입력으로 사용해 의상 크기 조절 가능성 탐색

보고서에서도 연구 목표를 **사이즈를 통해 조건화가 가능한 가상 피팅 모델을 만드는 것**으로 명시하고 있으며,  
비율 기반 크기 조절과 segmentation map 추가 학습을 핵심 실험으로 다루고 있습니다. :contentReference[oaicite:7]{index=7}

---

## What We Changed

기존 Stable Diffusion 기반 inpainting 파이프라인을 바탕으로,  
사람 이미지와 마스크, pose map, warped cloth뿐 아니라 **segmentation map을 추가 입력으로 사용하도록 확장**했습니다.

주요 변경 사항:

- Stable Diffusion inpainting pipeline 기반 VTON 구성
- pose map, segmentation map, warped cloth를 함께 조건으로 사용
- 입력 채널 확장을 위해 **U-Net 첫 번째 convolution layer를 수정**
- 기존 네트워크 지식을 유지하기 위해 새 채널에 해당하는 가중치는 0으로 초기화하여 확장

보고서에서는 최종 spatial input이 pose map, segmentation map, encoded warped cloth를 포함하도록 확장되었고,  
이를 위해 첫 번째 convolution 계층의 입력 채널을 늘렸다고 설명합니다. :contentReference[oaicite:8]{index=8}

---

## My Contribution

제가 맡아 집중한 부분은 다음과 같습니다.

- **Warping module 실험 및 크기 비율 조정**
- **Segmentation map 기반 입력 조작 실험**
- diffusion 입력 구조 변경 아이디어 구현
- 결과 비교 및 qualitative analysis 정리

특히 segmentation map의 상의 영역에 대해 **dilation / erosion**을 적용해  
모델이 입력 조건 변화에 따라 의상의 크기를 실제로 다르게 생성하는지 검증했습니다.  
이 과정에서 OpenCV를 사용해 이진 마스크를 만들고, 주황색 상의 영역(인덱스 5)을 조작해 새로운 segmentation map을 생성했습니다. :contentReference[oaicite:9]{index=9}

---

## Method

### 1. Warping Ratio Control
의류를 신체에 맞추는 warping 과정에서 비율을 조절하여  
소매 길이와 전체 실루엣이 어떻게 달라지는지 실험했습니다.

### 2. Segmentation Map Conditioning
사람 의상 segmentation map에서 상의 영역만 선택한 뒤,  
팽창(dilation)과 침식(erosion)을 적용하여 의상 영역 크기를 바꿨습니다.

- kernel size: 30 × 30
- dilation / erosion 적용
- 변경된 segmentation map을 추론 입력으로 사용

이 과정을 통해 모델이 단순히 이미지를 자연스럽게 생성하는 수준을 넘어서,  
**입력 조건에 따라 의류 크기 자체를 조절할 수 있는지**를 검증했습니다. :contentReference[oaicite:10]{index=10}

---

## Experiment Setup

- Dataset: **VITON-HD**
- Train samples: **11,647**
- Test samples: **2,032**
- Resolution: **1024 × 768**
- GPU: **RTX A6000**
- Training iterations: **300k**
- Batch size: **16** :contentReference[oaicite:11]{index=11}

---

## Results

정량 평가에서는 LPIPS, SSIM, FID를 사용했습니다.  
보고서 기준으로 우리 모델의 결과는 다음과 같습니다.

- LPIPS: **0.129**
- SSIM: **0.867**
- FID (paired): **10.86**
- FID (unpaired): **15.11** :contentReference[oaicite:12]{index=12}

정량 지표만 보면 기존 SOTA 대비 압도적으로 우수하다고 말하기는 어렵습니다.  
하지만 질적 실험에서는 다음을 확인했습니다.

- warping ratio 변화에 따라 소매 길이와 의상 실루엣이 비율적으로 변화함
- segmentation map을 추가했을 때 의상 확대 효과가 더 잘 반영됨
- 의상의 경계가 더 명확해지고, 신체 일부가 잘리는 오류가 감소함 :contentReference[oaicite:13]{index=13}

즉, 이 연구의 의미는 “최고 성능 달성”보다는  
**사이즈 조절을 위한 conditioning 가능성을 diffusion 기반 VTON에 실험적으로 보여준 것**에 있습니다.

---

## Discussion

이 연구는 기존 VTON의 한계였던 **사이즈 반영 문제**를  
데이터를 새로 대규모 수집하지 않고도 입력 조건 설계를 통해 해결하려는 시도였습니다.

성과:
- warping ratio 변화에 따른 일관된 의상 변화 확인
- segmentation conditioning의 효과 확인
- 입력 구조 확장을 통한 size-aware VTON 가능성 탐색

한계:
- 정량 지표 기준으로는 기존 대표 모델 대비 우세하지 않음
- 다양한 체형과 자세에서의 일반화 성능은 추가 검증 필요
- 실제 상용화를 위해서는 더 정교한 conditioning 및 데이터 확장이 필요

이 프로젝트는 연구적으로는 **conditioning 설계**, 구현 측면에서는 **diffusion 입력 구조 수정과 실험 파이프라인 구성**을 경험한 프로젝트였습니다.

---

## Award

- **DGIST 2024 UGRP 우수상** :contentReference[oaicite:14]{index=14}

---

## Keywords

`Diffusion` `Virtual Try-On` `VTON` `Stable Diffusion` `Segmentation Map Conditioning` `Warping Module` `Computer Vision`
