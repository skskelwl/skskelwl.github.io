---
title: "OpenArm 시작하기: 프로젝트 소개와 하드웨어 구성"
date: 2026-09-13 00:00:00 +0900
categories: [로보틱스, OpenArm]
tags: [OpenArm, 로봇팔, Physical AI, ROS2, CAN-FD]
description: OpenArm 2.0의 특징과 하드웨어·소프트웨어 구성을 살펴보고 앞으로 진행할 작업을 정리한다.
image:
  path: /assets/img/posts/openarm/openarm-overview.webp
  alt: OpenArm 2.0 양팔 로봇의 전체 구성과 주요 사양
pin: true
---

OpenArm 관련 작업을 기록하기 위한 첫 글이다. 먼저 OpenArm이 어떤 프로젝트인지 살펴보고, 실제 구동을 시작하기 전에 알아야 할 하드웨어와 소프트웨어 구성을 한 번에 정리해 보려고 한다.

이 글은 **Enactic의 OpenArm 2.0 공식 문서**를 기준으로 작성했다. 아직 직접 확인하지 않은 값은 공식 사양으로 구분했으며, 이후 글에서는 실제 조립 상태와 구동 결과를 계속 추가할 예정이다.

## OpenArm이란?

[OpenArm](https://openarm.dev)은 Physical AI 연구를 위해 공개된 사람 크기의 오픈소스 로봇팔 플랫폼이다. 팔 하나에 7개의 자유도(7-DOF)를 가지며, 한 팔 또는 양팔 구성으로 사용할 수 있다.

일반적인 산업용 로봇팔이 정해진 위치를 빠르고 정확하게 반복하는 데 초점을 둔다면, OpenArm은 사람이 사용하는 환경에서 물체를 잡고 접촉하며 데이터를 수집하는 연구에 초점을 둔다. 하드웨어 CAD와 BOM뿐 아니라 URDF/Xacro, CAN 통신 라이브러리, ROS 2 패키지, 시뮬레이션 환경도 공개되어 있어 구조를 직접 이해하고 수정하기 좋다.

OpenArm으로 시도할 수 있는 대표적인 작업은 다음과 같다.

- 사람의 동작을 따라 하는 원격 조작(teleoperation)
- 모방학습을 위한 로봇 데이터 수집
- 카메라 영상을 이용한 물체 인식과 조작
- MuJoCo·Isaac Lab 기반 시뮬레이션
- ROS 2와 MoveIt 2를 이용한 모션 플래닝
- 힘과 접촉이 중요한 양팔 협업 작업

{% include embed/youtube.html id='QzJSBbF7zIE' %}
_OpenArm 공식 프로젝트 소개 영상_

![OpenArm 2.0 양팔 로봇의 주요 사양](/assets/img/posts/openarm/openarm-overview.webp){: width="1600" height="1112" }
_OpenArm 2.0 전체 구성. 이미지 출처: [OpenArm 공식 저장소](https://github.com/enactic/openarm)_

## 주요 사양

공식 문서에 공개된 OpenArm 2.0의 핵심 사양을 정리하면 다음과 같다.

| 항목 | 사양 |
| --- | --- |
| 자유도 | 팔 하나당 7-DOF |
| 구성 | Single-arm 또는 Bimanual |
| 도달 거리 | 약 606 mm |
| 팔 무게 | 팔 하나당 약 5.5 kg |
| 정격 페이로드 | 4.1 kg |
| 최대 페이로드 | 6.0 kg |
| 통신 | CAN-FD |
| 구조 | 알루미늄·스테인리스 구조물, MISUMI 알루미늄 프레임 베이스 |
| 엔드 이펙터 | 2지 평행 그리퍼와 내장 RGB 카메라 |

여기서 페이로드에는 엔드 이펙터 무게도 포함된다. 별도의 무거운 그리퍼나 센서를 장착한다면 실제로 들 수 있는 물체의 무게는 그만큼 줄어든다. 정격 4.1 kg은 팔을 최대한 뻗은 불리한 자세에서 1분간 유지하는 조건이며, 최대 6.0 kg은 같은 조건에서 짧게 이동하고 유지하는 기준이다.

> 사양표의 수치는 설계와 버전에 따라 바뀔 수 있으므로 부품 구매나 제어기 설정 전에는 반드시 [OpenArm 공식 문서](https://docs.openarm.dev)를 다시 확인해야 한다.
{: .prompt-info }

## 하드웨어 구성

OpenArm을 단순히 모터가 연결된 로봇팔로 보기보다는, 다음 다섯 부분으로 나누면 전체 구성을 이해하기 쉽다.

### 1. 관절과 구동기

팔 하나에는 7개의 관절이 있다. 사람의 어깨부터 손목까지와 비슷한 움직임을 만들기 위해 여러 회전축을 조합한 구조다. 각 관절에는 요구 토크와 크기에 맞춰 서로 다른 DAMIAO 모터가 사용된다.

![OpenArm 2.0 관절별 모터 배치](/assets/img/posts/openarm/openarm-motors.webp){: width="700" height="400" }
_관절 위치에 따른 모터 구성. 이미지 출처: [OpenArm 공식 하드웨어 문서](https://docs.openarm.dev/hardware/openarm-2.0/motor)_

- **DM-J8009P 계열**: 큰 토크가 필요한 몸체와 어깨 쪽에 배치
- **DM4340 계열**: 토크와 크기의 균형이 필요한 중간 관절에 사용
- **DM-J4310 계열**: 비교적 작고 가벼워야 하는 손목 쪽에 사용

OpenArm이 강조하는 특징 중 하나는 역구동성(backdrivability)이다. 외력이 가해졌을 때 관절이 지나치게 딱딱하게 버티기보다 힘에 반응할 수 있어 원격 조작과 접촉 작업에 유리하다. 다만 역구동성이 안전을 자동으로 보장한다는 뜻은 아니며, 제어 파라미터와 기구적 간섭, 비상 정지 동작을 별도로 검증해야 한다.

### 2. 링크와 베이스

관절 사이 링크와 주요 구조물에는 알루미늄과 스테인리스 부품이 사용된다. 중앙 지지대는 MISUMI 알루미늄 프레임으로 구성되어 높이를 조절하거나 카메라·센서 같은 장치를 추가하기 쉽다. 베이스 플레이트에는 일정 간격의 M6 탭이 있어 테이블이나 별도 프레임에 고정할 수 있다.

각 관절에는 기계적 동작 한계가 있다. 소프트웨어 리밋과 별개로 비정상적인 자세와 배선 손상을 막는 최후의 제한이므로, 조립 후에는 간섭과 스토퍼 손상 여부를 먼저 확인해야 한다.

### 3. 그리퍼와 손목 카메라

OpenArm 2.0의 기본 엔드 이펙터는 두 손가락으로 물체를 집는 평행 그리퍼다. 그리퍼 하우징 안에는 RGB 카메라가 들어 있어 물체에 가까운 위치에서 영상을 얻을 수 있다.

![OpenArm 2.0 그리퍼와 내장 카메라](/assets/img/posts/openarm/openarm-gripper.webp){: width="1600" height="722" }
_2지 그리퍼와 내장 RGB 카메라. 이미지 출처: [OpenArm 공식 그리퍼 문서](https://docs.openarm.dev/hardware/openarm-2.0/gripper)_

손가락 형상은 교체할 수 있도록 설계되어 있어 물체 형태에 맞는 핑거를 새로 제작할 수 있다. 기본 그리퍼로 성능을 확인한 뒤, 작업 대상에 따라 접촉면 재질과 길이, 카메라 시야 가림 여부를 조정하는 방식이 적절해 보인다.

### 4. 전원과 통신

실제 하드웨어를 구동하려면 로봇 기구부 외에도 전원·통신 계통이 필요하다.

- 충분한 전류 용량을 가진 **24 V 전원 공급 장치**
- 모터와 전원을 분배하는 허브 보드 및 케이블 하네스
- 제어 PC와 모터 버스를 연결하는 **USB-to-CAN-FD 인터페이스**
- 좌·우 팔을 각각 연결하기 위한 CAN 인터페이스
- 이상 상황에서 전원을 차단할 수 있는 비상 정지 장치

공식 설정 예제에서는 오른팔을 `can0`, 왼팔을 `can1`에 연결한다. CAN 통신을 시작하기 전에는 종단 저항, 배선 극성, 모터 ID 중복, 정격 전압을 먼저 확인해야 한다. 전원이 인가된 상태에서 커넥터를 임의로 분리하거나 모터 영점을 잘못 설정하면 갑작스러운 움직임이 발생할 수 있다.

### 5. 제어용 컴퓨터와 소프트웨어

공식 튜토리얼은 Ubuntu 22.04 이상을 전제로 한다. 전체 소프트웨어는 기능에 따라 여러 저장소로 나뉜다.

| 구성요소 | 역할 |
| --- | --- |
| `openarm_hardware` | CAD, STL·STEP 파일과 제작 정보 |
| `openarm_description` | URDF/Xacro 모델과 링크·관절 정의 |
| `openarm_can` | CAN-FD 기반 모터 통신과 설정 도구 |
| `openarm_ros2` | ROS 2 Control 및 MoveIt 2 연동 |
| `openarm_mujoco` / `openarm_isaac_lab` | 시뮬레이션과 학습 환경 |
| `openarm_teleop` | 리더–팔로워 및 원격 조작 |
| `openarm_dataset` | 학습 데이터 기록과 관리 |

![RViz에서 확인한 OpenArm 양팔 모델](/assets/img/posts/openarm/openarm-rviz.webp){: width="1600" height="882" }
_URDF/Xacro로 생성한 양팔 모델의 RViz 시각화. 이미지 출처: [OpenArm 공식 저장소](https://github.com/enactic/openarm)_

실물에 바로 명령을 보내기 전에 `openarm_description`의 URDF를 RViz에서 확인하고, MuJoCo나 Isaac Lab에서 관절 방향과 제한을 검증하는 순서가 안전하다. 이후 CAN 통신과 ROS 2 Control을 연결하면 시뮬레이션에서 확인한 구성을 실제 하드웨어로 확장할 수 있다.

## 앞으로 진행할 순서

OpenArm 관련 글은 다음 순서로 정리할 예정이다.

1. 보유 부품 확인과 전체 BOM 정리
2. 기구부 조립 상태 및 관절별 배선 확인
3. 24 V 전원과 비상 정지 회로 점검
4. 모터 ID, CAN-FD 인터페이스와 종단 저항 설정
5. 각 관절의 영점과 동작 범위 확인
6. URDF 모델을 RViz에서 시각화
7. ROS 2 Control과 MoveIt 2 연결
8. 그리퍼와 손목 카메라 테스트
9. 원격 조작 및 데이터 수집
10. 실험 중 발생한 문제와 해결 과정 기록

첫 구동에서는 속도와 토크 제한을 낮게 설정하고 한 관절씩 확인할 생각이다. 양팔 전체를 한 번에 움직이기보다 전원, 통신, 영점, 방향을 단계별로 검증해야 문제 발생 지점을 빠르게 찾을 수 있다.

> 로봇을 동작시키기 전에는 베이스를 단단히 고정하고, 작업 반경에서 사람과 장애물을 치우고, 즉시 누를 수 있는 위치에 비상 정지 장치를 준비해야 한다. 전원을 차단하면 역구동 가능한 팔과 들고 있던 물체가 떨어질 수 있다는 점도 고려해야 한다.
{: .prompt-warning }

## 참고 자료

- [OpenArm 공식 웹사이트](https://openarm.dev)
- [OpenArm 공식 문서](https://docs.openarm.dev)
- [Enactic OpenArm GitHub](https://github.com/enactic/openarm)
- [OpenArm 하드웨어 CAD·BOM](https://github.com/enactic/openarm_hardware)
- [OpenArm ROS 2 패키지](https://github.com/enactic/openarm_ros2)
- [OpenArm CAN 통신 라이브러리](https://github.com/enactic/openarm_can)

다음 글에서는 실제로 가지고 있는 부품을 기준으로 BOM과 배선 구성을 확인하고, 구동 전에 필요한 준비물을 정리해 볼 예정이다.
