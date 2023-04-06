---
title: "[논문 리뷰] Eye Tracking-based LSTM for Locomotion Prediction in VR"
date: 2023-04-06
categories:
  - Paper review
  - VR
tags:
---

IEEE VR and 3D User Interfaces 2022 [논문](https://ieeexplore.ieee.org/document/9756758) 에 대한 리뷰

<br>

## 주제
머신러닝을 활용한 gaze를 바탕으로 유저의 행동 예측에 관한 연구


## 연구 동기

### Redirected Walking
> 가상 현실에서 걷기를 할 때, 우리가 사용할 수 있는 실제 공간이 매우 작다. 이 문제를 해결하기 위한 하나의 방법으로, 유저로 하여금 물리적 공간을 실제보다 더 크게 인식하게 하여 가상 공간에서 더 자유롭게 움직일 수 있도록 한다.

<img src="\assets\images\post\Paper-Review-VR\rdw.png">

<br>

__Redirected Walking으로 얻는 이점__
* Translation gains : 유저가 실제 공간에서 움직이는 속도보다 가상 공간에서 움직이는 속도를 더 크게 스케일할 수 있다

<img src="\assets\images\post\Paper-Review-VR\translation-gain.png">

* Curvature gains : 가상 공간을 조금씩 회전시켜 실제 공간에서 직선으로 움직이면 가상 공간에서는 곡선으로 움직이게 만드는 효과 (그림 1) $\rightarrow$ 실제로 곡선으로 움직이는데 가상 공간에서 직선으로 움직이는 효과와 동일 (그림 2).

<img src="\assets\images\post\Paper-Review-VR\curvature-0.png" width="60%">
그림 1

<img src="\assets\images\post\Paper-Review-VR\curvature-1.png" width="60%">
그림 2

<img src="\assets\images\post\Paper-Review-VR\curvature-2.png" width="60%">

<br>

### Early locomotion prediction
> Redirected walking 에서 유저의 포지션을 계속 추적하여 장애물이나 다른 유저, 혹은 벽과 같은 경계로 이동하고 있는지 확인한다. 만약 그렇다면 유저를 다른 방향으로 조금씩 미리 회전시켜서 _gains_ 를 얻을 수 있고 유저는 변화를 덜 느끼고 더 편안할 수 있다

두 가지 데이터를 통해 Early locomotion prediction 가능

* 환경 데이터 : 스켈레톤 맵을 통해 모든 가능한 경로를 얻고 이전의 데이터로 경험적인 가정을 세울 수 있다. 하지만 이 방법은 각각의 가상 공간에 적용되기 때문에 다이나믹하게 변화하거나 새롭게 생성되는 환경에는 적용하기 어렵다.
* 유저 데이터 : 유저의 걷는 속도, 방향, 머리의 방향, 시선을 통해 다음에 어디로 이동할 지 예측할 수 있다

<br>

__본문의 저자는 시선 데이터가 일반적인 Early locomotion prediction 에 유용한 지 확인하고자 한다__

<br>

## 연구 과정

* LSTM 모델 사용
* 학습 데이터 (input features)
    - 유저의 속도
    - 머리 회전 방향 (Head Mounted Display, HMD)
    - 신체 회전 방향 (Inertial Measuring Unit, IMU)
    - 시선 데이터 (HMD) 
* Output : 현재 머리의 위치에서 2.5초 후 예측된 머리의 위치로 향하는 방향 벡터

<br>

<img src="\assets\images\post\Paper-Review-VR\input-0.png">

이 모델은 환경에 대한 정보가 없이 사용자의 행동에 기초하기 때문에 환경이 아닌 사용자에게 종속된 좌표계를 사용할 필요가 있었다. 이를 __Head-fixed Coordinate__ 라고 하고 하는데, 다음과 같이 인풋 시퀀스가 있을 때 __reference yaw angle__ 을 통해 head-fixed 좌표계로 가져올 수 있다.

<br>

아래는 7개의 input features.

<img src="\assets\images\post\Paper-Review-VR\input-1.png">
<img src="\assets\images\post\Paper-Review-VR\input-2.png">


<br>

## 연구 결과

<img src="\assets\images\post\Paper-Review-VR\result-0.png">

User study 에서 진행했던 세 가지 task (Search, corridor, obstacle) 데이터셋 별로 성능을 구분한 표이다. 이 값들은 실제 경로와 예측의 평균 오차이다. __이를 통해, 시선 데이터를 학습에 사용했을 때 예측의 정확도가 더 높음을 알 수 있다.__

<br>

<img src="\assets\images\post\Paper-Review-VR\result-1.png">

위의 표는 장애물을 피해서 움직이는 task에서 사용자가 움직이다가 멈출 때, 가속도가 0에 가까워질 때에 모델의 정확도가 가장 높다는 것을 보여준다. 따라서 주로 사용자가 어떤 interaction 을 하기 위해 멈추려고 할 때 시선 데이터가 가장 효과적임을 알 수 있다. 그리고 유저가 어떤 행동을 하고 어떤 작업을 하느냐에 따라 시선 데이터가 모델에 주는 영향이 달라지는 것을 확인했다.

<br>

## Contribution

이 연구의 의의는 시선 데이터가 미래의 행동을 예측하는 데 도움이 될 수 있다는 것을 보여준다. 이전 연구에서는 시선 데이터가 두 개의 선택지에서 선택을 예측할 때 유용하다는 것을 보여주었지만 이 연구는 그 결과를 확장했다. 또한 유저가 환경과 상호작용할 때 특히 유용하다는 것을 보였다.