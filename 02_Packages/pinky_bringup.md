---
type: package
title: "pinky_bringup"
source_repo: seljb/pinky_pro
source_commit: "014a09f289e6988894fbffde2624fdb1e257815b"
source_paths:
  - pinky_bringup/package.xml
  - pinky_bringup/setup.py
  - pinky_bringup/setup.cfg
  - pinky_bringup/pinky_bringup/__init__.py
  - pinky_bringup/pinky_bringup/bringup.py
  - pinky_bringup/pinky_bringup/dynamixel_driver.py
  - pinky_bringup/pinky_bringup/battery_publisher.py
  - pinky_bringup/launch/bringup_robot.launch.xml
  - pinky_bringup/launch/bringup_sim.launch.xml
  - pinky_bringup/config/pinky_params.yaml
  - pinky_bringup/resource/pinky_bringup
  - pinky_bringup/test/test_copyright.py
  - pinky_bringup/test/test_flake8.py
  - pinky_bringup/test/test_pep257.py
verification: 코드 확인
---

# pinky_bringup

## 1. 패키지 개요

STEP 8-4-1/8-4-2의 정적 분석을 정리한 Package 기준 문서다.
Repository는 `seljb/pinky_pro`, 패키지 경로는 `pinky_bringup/`,
분석 Commit은 `014a09f289e6988894fbffde2624fdb1e257815b`다.
`verification: 코드 확인`은 파일·구현·정적 참조를 뜻하며 실제 실행 성공이 아니다.

[코드 확인] 실기 로봇 기동, Dynamixel 차동 구동과 encoder 기반 odom/TF/JointState,
외부 Battery 데이터 발행 및 시뮬레이터 선택 Launch를 제공한다.
역할은 TODO인 manifest description에서 추정하지 않고 실제 구현으로 확인했다.
근거: [pinky_bringup/pinky_bringup/bringup.py:36-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L36-L205), [pinky_bringup/pinky_bringup/battery_publisher.py:6-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L6-L36), [pinky_bringup/launch/bringup_robot.launch.xml:3-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L3-L29), [pinky_bringup/launch/bringup_sim.launch.xml:13-48](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L13-L48).

| 항목 | 확인 내용 | 근거 |
|---|---|---|
| 패키지 이름·버전 | `pinky_bringup`, `0.0.0` | [pinky_bringup/package.xml:4-5](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L4-L5) |
| 빌드 유형 | `ament_python` | [pinky_bringup/package.xml:20-22](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L20-L22) |
| description·license | TODO 기재 | [pinky_bringup/package.xml:6-8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L6-L8) |
| 자체 주요 클래스 | ROS Node `Pinky`, `BatteryPublisher`; 일반 Python 래퍼 `DynamixelDriver` | [pinky_bringup/pinky_bringup/bringup.py:36-38](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L36-L38), [pinky_bringup/pinky_bringup/battery_publisher.py:6-8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L6-L8), [pinky_bringup/pinky_bringup/dynamixel_driver.py:4-5](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L4-L5) |

## 2. 패키지 구조

[코드 확인] 기준 Commit에서 추적하는 패키지 내부 파일은 **14개**다.
아래 목록은 전체이며, 빈 파일에는 줄 번호 anchor를 붙이지 않았다.

| 원본 저장소 내부 경로·줄 | 역할 |
|---|---|
| [pinky_bringup/package.xml:4-22](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L4-L22) | 이름·버전·의존성과 ament_python 빌드 유형 |
| [pinky_bringup/setup.py:9-28](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/setup.py#L9-L28) | 패키지 탐색·설치 데이터·console entrypoint |
| [pinky_bringup/setup.cfg:1-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/setup.cfg#L1-L4) | develop/install 실행 스크립트 경로 |
| [pinky_bringup/pinky_bringup/__init__.py](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/__init__.py) | 빈 Python 패키지 초기화 파일 |
| [pinky_bringup/pinky_bringup/bringup.py:36-222](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L36-L222) | Pinky Node·실기 구동·상태 발행·배터리 경고·main |
| [pinky_bringup/pinky_bringup/dynamixel_driver.py:4-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L4-L100) | SDK 포트·모터 초기화·명령·feedback·종료 래퍼 |
| [pinky_bringup/pinky_bringup/battery_publisher.py:6-52](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L6-L52) | 외부 Battery 취득·Float32 발행·main |
| [pinky_bringup/launch/bringup_robot.launch.xml:3-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L3-L29) | description/LiDAR include와 own 두 Node |
| [pinky_bringup/launch/bringup_sim.launch.xml:13-48](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L13-L48) | MuJoCo/Gazebo 조건부 선택 |
| [pinky_bringup/config/pinky_params.yaml:1-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/config/pinky_params.yaml#L1-L4) | wheel_radius와 wheel_separation 설정 |
| [pinky_bringup/resource/pinky_bringup](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/resource/pinky_bringup) | 빈 ament package index marker; setup.py:11-12에서 설치 |
| [pinky_bringup/test/test_copyright.py:15-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/test/test_copyright.py#L15-L25) | copyright linter; :20에 skip decorator. 실행 안 함 |
| [pinky_bringup/test/test_flake8.py:15-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/test/test_flake8.py#L15-L25) | ament_flake8 linter 호출. 실행 안 함 |
| [pinky_bringup/test/test_pep257.py:15-23](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/test/test_pep257.py#L15-L23) | ament_pep257 linter 호출. 실행 안 함 |

[코드 확인] 별도 URDF/Xacro 및 msg/srv/action 정의 파일은 없다.
테스트 파일은 linter용이며 존재나 skip 선언을 기능·하드웨어 테스트 성공으로 취급하지 않는다.

### 2.1. Python 설치 구성

`setup.py`는 `find_packages(exclude=['test'])`를 사용한다.
resource marker, manifest, `launch/*launch.*`, `config/*.yaml`을 각각 ament index와
share 디렉터리에 설치하도록 선언한다. `install_requires=['setuptools']`,
`tests_require=['pytest']`다. [pinky_bringup/setup.py:9-23](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/setup.py#L9-L23).

`setup.cfg`의 develop `script_dir`와 install `install_scripts`는 모두
`$base/lib/pinky_bringup`이다. [pinky_bringup/setup.cfg:1-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/setup.cfg#L1-L4).
이 설정을 실제 설치 결과로 검증한 것은 아니다.

## 3. 실행 Node

[코드 확인] 자체 구현 Node는 두 개다. 실행 파일 이름과 Node 생성 이름을 구분한다.

| Node 생성 이름 | 실행 파일 | 클래스·역할 | 코드 근거 |
|---|---|---|---|
| `pinky_bringup` | `bringup` | `Pinky`: 모터 초기화·명령 구독·encoder 기반 상태·배터리 경고 | [pinky_bringup/pinky_bringup/bringup.py:36-101](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L36-L101) |
| `battery_publihser` | `battery_publisher` | `BatteryPublisher`: 외부 Battery 취득과 백분율/전압 발행. 이름은 코드의 오타 그대로 | [pinky_bringup/pinky_bringup/battery_publisher.py:6-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L6-L36) |

`DynamixelDriver`는 Node가 아니라 SDK 래퍼다.
외부 robot/joint state publisher와 LiDAR는 자체 Node 목록에 섞지 않고 5절에서 구분한다.

### 3.1. setup.py 진입점

| 실행 파일 | console entrypoint | 실제 main 연결 | 근거 |
|---|---|---|---|
| bringup | `pinky_bringup.bringup:main` | `rclpy.init → Pinky() → spin`; finally는 driver 종료·Node 제거·shutdown | [pinky_bringup/setup.py:26](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/setup.py#L26); [pinky_bringup/pinky_bringup/bringup.py:208-219](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L208-L219) |
| battery_publisher | `pinky_bringup.battery_publisher:main` | `rclpy.init → BatteryPublisher() → spin`; finally는 Node 제거·shutdown | [pinky_bringup/setup.py:27](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/setup.py#L27); [pinky_bringup/pinky_bringup/battery_publisher.py:38-49](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L38-L49) |

Node 초기화·실기/sim 포함 관계는 [파이프라인](../01_Architecture/pinky-bringup-pipeline.md)에 둔다.

## 4. 통신 인터페이스

[코드 확인] 일반 Topic은 선행 `/`가 없는 코드 문자열이다.
아래 연결은 구현 확인이며 실제 ROS 그래프·수신·QoS 협상은 [미검증]이다.

| Node | 방향 | Topic 또는 TF 관계 | 타입 | 함수·콜백 | 근거 |
|---|---|---|---|---|---|
| pinky_bringup | Publisher | `odom` | `nav_msgs/msg/Odometry` | `_publish_odometry()` | [pinky_bringup/pinky_bringup/bringup.py:18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L18), [pinky_bringup/pinky_bringup/bringup.py:84](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L84), [pinky_bringup/pinky_bringup/bringup.py:174-183](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L174-L183) |
| pinky_bringup | Publisher | `joint_states` | `sensor_msgs/msg/JointState` | `_publish_joint_states()` | [pinky_bringup/pinky_bringup/bringup.py:19](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L19), [pinky_bringup/pinky_bringup/bringup.py:85](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L85), [pinky_bringup/pinky_bringup/bringup.py:185-195](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L185-L195) |
| pinky_bringup | Subscriber | `cmd_vel` | `geometry_msgs/msg/Twist` | `twist_callback()` | [pinky_bringup/pinky_bringup/bringup.py:17](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L17), [pinky_bringup/pinky_bringup/bringup.py:86](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L86), [pinky_bringup/pinky_bringup/bringup.py:104-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L125) |
| pinky_bringup | Subscriber | `battery/voltage` | `std_msgs/msg/Float32` | `battery_voltage_callback()` | [pinky_bringup/pinky_bringup/bringup.py:33-34](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L33-L34), [pinky_bringup/pinky_bringup/bringup.py:90-95](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L90-L95), [pinky_bringup/pinky_bringup/bringup.py:197-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L197-L205) |
| pinky_bringup | TF broadcaster | `odom → base_footprint` | `sendTransform()` 인수는 `geometry_msgs/msg/TransformStamped` | `_publish_tf()` | [pinky_bringup/pinky_bringup/bringup.py:20-21](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L20-L21), [pinky_bringup/pinky_bringup/bringup.py:87](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L87), [pinky_bringup/pinky_bringup/bringup.py:163-172](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L163-L172) |
| battery_publihser | Publisher | `battery/percent` | `std_msgs/msg/Float32` | `percentage_callback()` | [pinky_bringup/pinky_bringup/battery_publisher.py:12-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L12-L16), [pinky_bringup/pinky_bringup/battery_publisher.py:28-31](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L28-L31) |
| battery_publihser | Publisher | `battery/voltage` | `std_msgs/msg/Float32` | `voltage_callback()` | [pinky_bringup/pinky_bringup/battery_publisher.py:18-22](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L18-L22), [pinky_bringup/pinky_bringup/battery_publisher.py:33-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L33-L36) |

일반 pub/sub의 QoS 인수는 10이며 reliability/durability를 개별 구성하는 코드는 없다.

TF transport Topic·QoS는 이 패키지가 직접 지정하지 않고 외부 `TransformBroadcaster`에 위임한다.
자체 생성자와 실기 Launch의 own Node 항목에는 namespace·name override·remapping이 없다.
최종 fully qualified 이름은 외부 실행 옵션까지 확인하지 않아 [미검증]이다.
근거: [pinky_bringup/pinky_bringup/bringup.py:38](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L38), [pinky_bringup/pinky_bringup/bringup.py:84-95](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L84-L95), [pinky_bringup/pinky_bringup/battery_publisher.py:8-22](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L8-L22), [pinky_bringup/launch/bringup_robot.launch.xml:20-27](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L20-L27).

| 구분 | 코드 확인 범위 | 근거 |
|---|---|---|
| 자체 Service server/client | 세 Python 구현 파일에 생성 코드 없음 | [pinky_bringup/pinky_bringup/bringup.py](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py), [pinky_bringup/pinky_bringup/dynamixel_driver.py](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py), [pinky_bringup/pinky_bringup/battery_publisher.py](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py) |
| 자체 Action server/client | 해당 패키지 구현 없음 | 같은 세 소스 파일 및 14개 전체 파일 목록 |
| 자체 인터페이스 정의 | msg/srv/action 파일 없음 | 2절 전체 목록 |
| scan 발행·IMU 구독·센서융합 | 자체 Python 구현 없음 | [pinky_bringup/pinky_bringup/bringup.py](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py), [pinky_bringup/pinky_bringup/battery_publisher.py](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py) |
| 외부 LiDAR 내부 인터페이스 | child 소스가 이 저장소에 없어 확인하지 않음 | include 근거만 [pinky_bringup/launch/bringup_robot.launch.xml:12-18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L12-L18) |
| rclpy 자동 기본 서비스 | 외부 구현 영역이며 자체 서비스로 열거하지 않음 | 이 조사 범위에서 외부 구현 미검증 |

## 5. 의존 관계

### 5.1. package.xml 선언

[코드 확인] 선언 태그와 소스의 실제 사용을 구분한다.
빌드 유형이 ament_python인 것과 `buildtool_depend` 선언 여부는 별개다.

| 태그·항목 | 선언값 | 근거 |
|---|---|---|
| depend | `tf_transformations` | [pinky_bringup/package.xml:15](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L15) |
| exec_depend | `pinky_mujoco` | [pinky_bringup/package.xml:18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L18) |
| test_depend | `ament_copyright`, `ament_flake8`, `ament_pep257`, `python3-pytest` | [pinky_bringup/package.xml:10-13](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L10-L13) |
| buildtool_depend | 선언 없음 | [pinky_bringup/package.xml:1-23](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L1-L23) |
| export/build_type | `ament_python` | [pinky_bringup/package.xml:20-22](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L20-L22) |

Gazebo optional 설명은 주석이며 의존 태그가 아니다. [pinky_bringup/package.xml:16-18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L16-L18).
`setuptools`는 setup.py의 설치 요구사항이며 manifest의 ROS 실행 의존성 선언이 아니다.
[pinky_bringup/setup.py:17](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/setup.py#L17).

### 5.2. 코드·Launch의 실제 참조

| 대상 | 정적 사용 | manifest 직접 선언 | 소스 근거 |
|---|---|---|---|
| pinky_description | 실기 upload launch include | 없음 | [pinky_bringup/launch/bringup_robot.launch.xml:8-10](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L8-L10) |
| pinky_mujoco | 기본 sim 경로 child include | exec_depend 있음 | [pinky_bringup/launch/bringup_sim.launch.xml:34-41](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L34-L41); [pinky_bringup/package.xml:18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L18) |
| pinky_gz_sim | gz/gazebo 조건 child include | 없음 | [pinky_bringup/launch/bringup_sim.launch.xml:43-47](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L43-L47) |
| sllidar_ros2 | 외부 LiDAR launch include | 없음 | [pinky_bringup/launch/bringup_robot.launch.xml:12-18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L12-L18) |
| tf_transformations | `quaternion_from_euler` | depend 있음 | [pinky_bringup/pinky_bringup/bringup.py:12](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L12), [pinky_bringup/pinky_bringup/bringup.py:170](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L170), [pinky_bringup/package.xml:15](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L15) |
| rclpy | Node·init·spin·shutdown | 없음 | [pinky_bringup/pinky_bringup/bringup.py:3-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L3-L4), [pinky_bringup/pinky_bringup/bringup.py:208-219](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L208-L219), [pinky_bringup/pinky_bringup/battery_publisher.py:1-2](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L1-L2) |
| geometry_msgs / nav_msgs / sensor_msgs / std_msgs | Twist·TransformStamped·Odometry·JointState·Float32 | 직접 선언 없음 | [pinky_bringup/pinky_bringup/bringup.py:8-13](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L8-L13); [pinky_bringup/pinky_bringup/battery_publisher.py:3](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L3) |
| tf2_ros | TransformBroadcaster | 없음 | [pinky_bringup/pinky_bringup/bringup.py:11](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L11), [pinky_bringup/pinky_bringup/bringup.py:87](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L87) |
| dynamixel_sdk | port/packet/group I/O·매크로·통신결과 | 없음 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:2](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L2), [pinky_bringup/pinky_bringup/dynamixel_driver.py:25-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L25-L29), [pinky_bringup/pinky_bringup/dynamixel_driver.py:59-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L59-L100) |
| pinkylib | Battery와 취득 함수 | 없음 | [pinky_bringup/pinky_bringup/battery_publisher.py:4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L4), [pinky_bringup/pinky_bringup/battery_publisher.py:10](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L10), [pinky_bringup/pinky_bringup/battery_publisher.py:28-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L28-L36) |

`math/time`, `os/glob`는 Python 표준 라이브러리다.
child Python launch의 `launch/launch_ros/ament_index_python` import를 bringup.py의
직접 import로 취급하지 않는다. 외부 child 연결의 근거는 [파이프라인](../01_Architecture/pinky-bringup-pipeline.md)에 둔다.

[미검증] 전이 의존성·기존 설치·버전이 위 사용을 제공하는지, 실제 설치·import·실행이 성공하는지.
직접 선언 차이만으로 실패를 확정하지 않는다.

## 6. 관련 파이프라인

- [실기·시뮬레이터 실행 및 데이터 흐름](../01_Architecture/pinky-bringup-pipeline.md)
- [전체 ROS2 패키지 구조](../00_Overview/package-inventory.md)
- [소스코드 분석 기준](../00_Overview/source-baseline.md)

실기와 sim은 다른 구현 경로이며 simulator Topic 호환 주석은 실행 검증이 아니다.
정적 기동 관계의 근거: [pinky_bringup/launch/bringup_robot.launch.xml:3-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L3-L29), [pinky_bringup/launch/bringup_sim.launch.xml:13-48](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L13-L48).

## 7. 관련 코드 및 파라미터

- [모터 제어 함수·수식·SDK·초기화/종료](../05_Code_Analysis/pinky_bringup--motor-control.md)
- [encoder·Odometry·TF·JointState·배터리 상태 발행](../05_Code_Analysis/pinky_bringup--state-publishing.md)
- [YAML 설정값·사용 위치·변경 영향](../03_Parameters/pinky_bringup-parameters.md)

Package 문서는 파일·Node·인터페이스·의존 관계의 기준으로 삼고, 함수 수식과 설정값의 상세 내용은 위 문서에 나눈다.

## 8. 검증 및 미확인 사항

- [코드 확인] 패키지 내부 추적 파일 14개, 두 entrypoint·두 자체 Node,
  pub/sub/TF API 호출과 선언·실제 참조를 확인했다.
- [코드 확인] JointState 바퀴 이름과 공통 Xacro의 정적 이름 불일치,
  오른쪽 odom/JointState 부호 표현 차이는 [상태 발행 문서](../05_Code_Analysis/pinky_bringup--state-publishing.md)에 근거와 함께 기록했다.
- [추론] 이름·부호·초기화 실패/cleanup·수치 검증 차이의 영향은 각 코드 분석 문서에서 구분했다.
- [미검증] 실제 parameter 적용값·단위·기구 보정, 모터/센서 동작, ROS 그래프·TF·QoS,
  외부 SDK/Battery/LiDAR 구현과 설치 상태, 빌드·테스트 성공.
- timeout 구현 부재를 장치 전체에 안전 기능이 없다는 주장으로 확대하지 않는다.
- 다음 미등록 파일은 기준 HEAD에 없고 내용·용도·수식의 근거로 사용하지 않았다:
  `pinky_navigation/launch/localization_only_launch.xml`,
  `pinky_navigation/nav2_send_goal_basic.py`,
  `pinky_navigation/params/amcl_params.yaml`.
- 기존 [전체 구조 문서](../00_Overview/package-inventory.md)의 코드 사실과 모순되는 내용은 확인하지 않았다.
  기존 문서를 변경하지 않고 상세 근거·추가 정적 비교를 이 문서 묶음으로 분리했다.
- 원본 코드는 읽기 전용으로 확인했다. ROS2·모터·센서·simulator·빌드·테스트·설치는 실행하지 않았다.
