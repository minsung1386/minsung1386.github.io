<!-- # [Paper review] Addressing Delayed Feedback for Continuous Training with Nerual Networks in CTR predictions
 -->
 ---
title: "[논문 리뷰] Addressing Delayed Feedback for Continuous Training with Nerual Networks in CTR predictions"
date: 2023-04-06
categories:
  - ML
  - Paper review
  - RecSys
tags:
---


twitter 에서 발표한 ACM Recsys 2019 [논문](https://arxiv.org/abs/1907.06558) 에 대한 리뷰

<br>

## 목표
__CTR 예측을 위한 Continuous learning 에서의 Delayed feedback 의 처리 -최적의 손실 함수와 모델 조합 확인__

<br>

__Delayed feedback__ 
> 사용자가 아이템에 대한 피드백을 제공하는 데 시간이 걸리는 경우를 의미. 예를 들어, 사용자가 영화를 본 후에 영화에 대한 평가를 제공하는 것이 일반적. 이러한 피드백이 딜레이되면 추천 시스템이 새로운 정보를 고려하지 못하고 지난 정보에만 의존하여 추천을 수행할 수 있음

<br>

<img src="\assets\images\20230406-figure1.png" width="500px" height="300px">

<br>

__Solution__
* __Fake negative__ 로 초기 레이블링 : click 발생 시 같은 이벤트에 대해 positive label 데이터를 추가 $\rightarrow$ CTR이 낮을 때만 효과적
* 휴리스틱 방식은 잘못된 레이블링 문제와 최적의 window size 에 대한 trade-off 존재

<br>

## 연구 과정
__State-Of-The-Art (SOTA) Delayed feedback model__ : [논문](https://dl.acm.org/doi/abs/10.1145/2623330.2623634)에 나오는 모델을 아래와 같이 정리해봤다.

<img src="\assets\images\20230406-figure2.png" width="800px" height="400px">

<br>

__Importance Sampling__ : 확률 분포 p(x) 를 알고 있지만 p에서 sample을 생성하기 어려울 때, 비교적 sample 을 생성하기 쉬운 q(x) 에서 생성하여 p 의 기댓값을 계산하는 것.

Importance sampling 은 fake negative 가 포함된 distribution 으로부터 actual data distribution 을 뽑기 위해 활용된다. 이 때, 활용할 loss funciton 을 제안하고 두 모델의 조합에 따른 성능을 비교한다.

* model
    * Logistic regression
    * Wide-and-deep model
* loss funciton
    * Delayed feedback loss _[Chapelle, 2014]_
    * Positive-unlabeled loss (PU loss) _[du Plessis et al., 2015]_
    * __Fake negative weighted (FN weighted)__
    * __Fake negative calibration (FN calibration)__

<br>

## 연구 결과

1. Logistic regression
<div>
    <img src="\assets\images\20230406-figure3.png" width="45%" height="100%">
    <img src="\assets\images\20230406-figure4.png" width="45%" height="100%">
</div>
<br>
2. Wide-and-deep model 
<div>
    <img src="\assets\images\20230406-figure5.png" width="45%" height="100%">
    <img src="\assets\images\20230406-figure6.png" width="45%" height="100%">
</div>

<br>

본 논문에서 제안된 두 가지 손실 함수 FN weighted 과 FN calibration 은 Wide-and-deep 모델의 조합이 가장 우수한 오프라인 성능을 보였다.

하지만 제안된 손실 함수는 FN 데이터에 의해 먼저 update 되고 positive 데이터는 나중에 들어오므로 gradient 의 시간적 의존성이 존재함을 고려해야 한다.