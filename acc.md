# YOLO 기반의 ADAS를 적용한 ACC(Advanced Cruise Control) Simulation by MATLAB&Simulink (1)

## 개요

- **CC(Cruise Control)**
    - 운전자가 accelerate 하지 않아도 지정된 속도로 차량을 주행할 수 있는 기능.
- **ACC(Advanced Cruise Control)**
    - 기존 크루즈 기능에, 브레이크도 자동으로 밟아주는 기능이 추가된 시스템.
    - 즉 주변 차량 환경을 인식하고 가속, 브레이크를 cruise로 제어하기 위해서는 자율 주행 기술이 필요.

## ML model

- **YOLO**
    - YOLO의 여러가지 버전이 있지만, 참조 가능한 데이터가 충분하며 의미 있는 결과 도출이 가능한 모델 선택을 목표로 한다.
    - YOLOv4, Darknet의 모델을 우선적으로 시도 후 버전 업 계획 중임.
- VariFocalLoss
    - 객체 검출에서 주로 사용되는 손실 함수(loss function) 중 하나로 YOLOv4에서 처음 소개되었다. 기존의 객체 검출에서 사용되던 Cross-Entropy 손실 함수는 모든 예측 박스에 대해 동일한 가중치를 부여하는 데 비해, VariFocalLoss는 예측 박스의 어려운 예제에 더 높은 가중치를 부여한다. 즉, 예측 오류가 큰 예제에 더 많은 비중을 두는 방식이다.
    - YOLOv6에서도 VariFocalLoss를 사용하며, 자율 주행 또한 예측 오류가 큰 예제가 존재하는 도로 상황이므로 여러 모델로 테스트 할 필요성이 있다.

![[YOLOv6 paper] [https://arxiv.org/pdf/2209.02976](https://arxiv.org/pdf/2209.02976)](image.png)

[YOLOv6 paper] [https://arxiv.org/pdf/2209.02976](https://arxiv.org/pdf/2209.02976)

## 사용 TOOL

- MATLAB&Simulink
    - Deep Learning Toolbox
    - Automated Driving Toolbox
    - RoadRunner / RoadRunner Asset Library / RoadRunner Scene Builder
    - 그 외 Toolbox는 광운대학교 License에 포함, Toolbox 1개 당 USD 15.00 정도 예상하므로 예산 안에서 연구 가능.
    

## 구현 계획

- 학습된 YOLO 모델 준비
    - 테스트 완료한 YOLO 모델을 Deep Learning Toolbox에 로드
    - Object Detection 성능 확인, 모델이 허용 가능한 속도와 효율을 가지는지 평가
- PID 제어 로직 설계 (ACC)
    - YOLO의 bounding box 크기 기반으로 객체 간 거리 추정
    - 계산된 객체 간 속도를 simulink PID controller의 입력으로써 사용하여 피드백 제어 로직 구성
    - 즉 거리 오차를 에러로써 사용

![[PID simulink logic] Run-Time Safety Monitoring of Neural-Network-Enabled Dynamical Systems Weiming Xiang, Senior Member, IEEE](image%201.png)

[PID simulink logic] Run-Time Safety Monitoring of Neural-Network-Enabled Dynamical Systems Weiming Xiang, Senior Member, IEEE

- Simulimk 통합
    - 차량 모델링(간단한 동력학 모델 및 속도 제어 모델)
    - RoadRunner에서 도로 및 장애물, 선행 차량 속도 등의 환경 무작위 생성
    - YOLO-Simulink 연동(YOLO 출력 데이터를 simulink에 입력 및 연동)

- 테스트 및 평가 방법
    - YOLO
        - mAP (mean Average Precision) : 높을 수록 객체 탐지 성능 증가.
        - FPS,  False Positive, False Negative, 추론 시간 등을 종합적으로 평가 하여 YOLO 버전 마다의 데이터 제시와 함께 해당 버전의 YOLO를 사용하는 이유를 밝힐 것.
    - PID logic
        - Overshoot(%), Rise Time, Settling Time, Steady-State Error 파악
        - Root Locus가 left half plane에 존재하는지, Gain&Phase Margin을 그려서 허용 가능한 범위에 있는지 확인함으로써도 평가 가능
    - RoadRunner
        - RoadRunner 시나리오 내에서 ACC 최종 평가
    
    ![[MATLAB RoadRunner]](image%202.png)
    
    [MATLAB RoadRunner]
    

## 팀원

- 나수현
- 조현준
- 양봉윤