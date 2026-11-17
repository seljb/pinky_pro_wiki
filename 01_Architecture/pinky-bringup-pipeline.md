---
type: architecture
title: "pinky_bringup 실기 및 시뮬레이터 파이프라인"
source_repo: seljb/pinky_pro
source_commit: "014a09f289e6988894fbffde2624fdb1e257815b"
source_paths:
  - pinky_bringup/launch/bringup_robot.launch.xml
  - pinky_bringup/launch/bringup_sim.launch.xml
  - pinky_bringup/pinky_bringup/bringup.py
  - pinky_bringup/pinky_bringup/dynamixel_driver.py
  - pinky_bringup/pinky_bringup/battery_publisher.py
  - pinky_description/launch/upload_robot.launch.py
  - pinky_mujoco/launch/launch_sim.launch.py
  - pinky_gz_sim/launch/launch_sim.launch.xml
  - pinky_bringup/package.xml
verification: 코드 확인
---

# pinky_bringup 실기 및 시뮬레이터 파이프라인

## 1. 목적

STEP 8-4-1과 STEP 8-4-2의 분석 결과를 바탕으로 실기 기동과 시뮬레이터 선택,
모터 명령·상태·배터리의 정적 데이터 흐름을 정리한다.
모든 소스 경로·줄 번호와 원본 링크는
`014a09f289e6988894fbffde2624fdb1e257815b` 기준이다.

[코드 확인]은 파일에 선언된 구성·호출·문자열을 뜻한다.
[추론]은 그 근거에 따른 영향 해석이며, 실제 실행·초기화·통신 성공과
최종 런타임 파라미터는 [미검증]이다. ROS2·모터·센서·시뮬레이터를 실행하지 않았다.
기준 Commit에 없는 미등록 파일 3개는 분석에서 제외했다.
작업 범위는 [소스코드 분석 기준](../00_Overview/source-baseline.md)을 따른다.

파일 구성·통신 목록은 [패키지 문서](../02_Packages/pinky_bringup.md),
원래 수식·오류 처리는 두 Code Analysis 문서,
설정값·변경 영향은 [파라미터 문서](../03_Parameters/pinky_bringup-parameters.md)를 기준으로 한다.

## 2. 전체 데이터 흐름

### 2.1. 실기 구성

[코드 확인] 아래 선은 launch 포함 관계 또는 소스에 명시된 함수·이름·타입의 대응이다.
프로세스가 실제 준비된 순서와 통신 연결을 실행으로 검증한 그림은 아니다.

```text
bringup_robot.launch.xml
  ├─ pinky_description/upload_robot.launch.py
  │    ├─ robot_state_publisher
  │    └─ joint_state_publisher
  ├─ sllidar_ros2/sllidar_c1_launch.py
  │    └─ 외부 child의 내부 Node/Topic/메시지 타입은 미확인
  ├─ bringup 실행 파일 → Pinky / Node 이름 pinky_bringup
  │    ├─ cmd_vel(Twist)
  │    │    → twist_callback()
  │    │    → DynamixelDriver.set_double_rpm()
  │    │    → SDK GroupSyncWrite
  │    ├─ 명목 30 Hz 상태 타이머
  │    │    → update_and_publish()
  │    │         → get_feedback() / encoder·RPM
  │    │         → encoder 기반 상태 계산
  │    │         ├─ odom → base_footprint TF
  │    │         ├─ odom(Odometry)
  │    │         └─ joint_states(JointState)
  │    └─ battery/voltage(Float32)
  │         → battery_voltage_callback() → 전압 경고
  └─ battery_publisher 실행 파일 → BatteryPublisher
       └─ Node 이름 battery_publihser
            └─ 외부 pinkylib.Battery
                 ├─ 별도 5초 타이머 → battery/percent(Float32)
                 └─ 별도 5초 타이머 → battery/voltage(Float32)
```

실기 포함 관계는 [pinky_bringup/launch/bringup_robot.launch.xml:3-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L3-L29),
description Node 구성은 [pinky_description/launch/upload_robot.launch.py:22-59](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/upload_robot.launch.py#L22-L59)에서 확인했다.
`Pinky`의 구독·발행·타이머와 상태 발행 순서는
[pinky_bringup/pinky_bringup/bringup.py:84-101](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L84-L101), [pinky_bringup/pinky_bringup/bringup.py:127-172](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L127-L172)에 있다.
배터리 데이터 취득·발행은 [pinky_bringup/pinky_bringup/battery_publisher.py:6-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L6-L36),
전압 소비는 [pinky_bringup/pinky_bringup/bringup.py:197-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L197-L205)에 있다.

### 2.2. 시뮬레이터 분기

```text
bringup_sim.launch.xml
  ├─ sim == "mujoco" [기본 선택]
  │    → pinky_mujoco/launch_sim.launch.py
  │         → URDF 확장·world 결정
  │         → pinky_mujoco bridge + robot_state_publisher
  └─ sim in ("gz", "gazebo")
       → pinky_gz_sim/launch_sim.launch.xml
            → Gazebo 서버/GUI + description
            → 로봇 생성 + ROS/Gazebo bridge
```

[코드 확인] 선택 조건과 include는 [pinky_bringup/launch/bringup_sim.launch.xml:14-47](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L14-L47),
MuJoCo Node 구성은 [pinky_mujoco/launch/launch_sim.launch.py:41-79](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/launch/launch_sim.launch.py#L41-L79),
Gazebo 구성은 [pinky_gz_sim/launch/launch_sim.launch.xml:13-35](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/launch/launch_sim.launch.xml#L13-L35)에 있다.
이 경로는 실기 `Pinky`, `DynamixelDriver`, `BatteryPublisher`를 기동하지 않는다.
실기 YAML의 바퀴 파라미터를 child로 전달하는 항목도 없다.

## 3. ROS2 구성 요소

[코드 확인] 전체 자체 인터페이스는 [패키지 문서](../02_Packages/pinky_bringup.md)에 정리하고,
아래는 파이프라인의 연결점만 표시한다. 일반 Topic 문자열은 모두 상대 이름이다.

| Node | Topic / Service / Action | 역할 | 코드 근거 |
|---|---|---|---|
| `pinky_bringup` | 구독 `cmd_vel` / `geometry_msgs/msg/Twist` | `Pinky.twist_callback()`에서 속도 명령 처리 | [pinky_bringup/pinky_bringup/bringup.py:17](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L17); [pinky_bringup/pinky_bringup/bringup.py:86](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L86); [pinky_bringup/pinky_bringup/bringup.py:104-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L125) |
| `pinky_bringup` | 발행 `odom` / `nav_msgs/msg/Odometry` | encoder 차분 기반 pose·속도 | [pinky_bringup/pinky_bringup/bringup.py:84](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L84); [pinky_bringup/pinky_bringup/bringup.py:127-183](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L127-L183) |
| `pinky_bringup` | TF `odom → base_footprint` | `TransformStamped`를 `sendTransform()`에 전달 | [pinky_bringup/pinky_bringup/bringup.py:20-21](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L20-L21); [pinky_bringup/pinky_bringup/bringup.py:163-172](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L163-L172) |
| `pinky_bringup` | 발행 `joint_states` / `sensor_msgs/msg/JointState` | raw encoder와 feedback RPM의 변환값 | [pinky_bringup/pinky_bringup/bringup.py:85](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L85); [pinky_bringup/pinky_bringup/bringup.py:185-195](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L185-L195) |
| `battery_publihser` | 발행 `battery/percent`, `battery/voltage` / `std_msgs/msg/Float32` | 외부 `Battery` 값의 별도 주기 발행 | [pinky_bringup/pinky_bringup/battery_publisher.py:8-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L8-L36) |
| `pinky_bringup` | 구독 `battery/voltage` / `std_msgs/msg/Float32` | `battery_voltage_callback()`의 경고 판단 | [pinky_bringup/pinky_bringup/bringup.py:90-95](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L90-L95); [pinky_bringup/pinky_bringup/bringup.py:197-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L197-L205) |
| description child | `robot_state_publisher`, `joint_state_publisher` 구성 | 모델·joint state 관련 외부 Node 설정 | [pinky_description/launch/upload_robot.launch.py:22-59](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/upload_robot.launch.py#L22-L59) |
| LiDAR child | 내부 Node/Topic/타입 확인되지 않음 | 외부 launch 경로와 인수만 확인 | [pinky_bringup/launch/bringup_robot.launch.xml:12-18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L12-L18) |

TF transport Topic 이름·QoS는 자체 소스에서 직접 지정하지 않고 외부
`TransformBroadcaster`에 위임한다. 이를 별도로 확인한 런타임 Topic으로 서술하지 않는다.
자체 세 Python 구현 파일에는 Service/Action 서버·클라이언트 생성 코드가 없다.
외부 Node의 자동 기본 서비스나 child 내부 인터페이스를 자체 구현으로 열거하지 않는다.

## 4. 실행 및 연결 관계

### 4.1. 실기 launch

[코드 확인] `bringup_robot.launch.xml`의 구성은 다음과 같다.

| 구성 | 전달 내용 | 소스 근거 |
|---|---|---|
| launch 인수 | `use_sim_time=False`, `wheel_radius`, `wheel_separation` 선언. 바퀴 기본값은 파라미터 문서 참조 | [pinky_bringup/launch/bringup_robot.launch.xml:4-6](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L4-L6) |
| description include | `pinky_description/launch/upload_robot.launch.py`에 `use_sim_time` 인수 전달 | [pinky_bringup/launch/bringup_robot.launch.xml:8-10](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L8-L10) |
| LiDAR include | `serial_port=/dev/ttyAMA0`, `frame_id=rplidar_link`, `inverted=false`, `angle_compensate=true`, `scan_mode=DenseBoost` | [pinky_bringup/launch/bringup_robot.launch.xml:12-18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L12-L18) |
| own bringup Node | pkg=`pinky_bringup`, exec=`bringup`; YAML과 개별 바퀴 파라미터 전달 | [pinky_bringup/launch/bringup_robot.launch.xml:20-24](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L20-L24) |
| own battery Node | pkg=`pinky_bringup`, exec=`battery_publisher`; 별도 파라미터 전달 없음 | [pinky_bringup/launch/bringup_robot.launch.xml:26-27](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L26-L27) |

두 실행 파일은 각각 `main()`으로 연결되며 Node 이름은 `pinky_bringup`과
코드에 적힌 철자 그대로 `battery_publihser`다.
진입점 근거: [pinky_bringup/setup.py:24-28](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/setup.py#L24-L28),
[pinky_bringup/pinky_bringup/bringup.py:208-219](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L208-L219), [pinky_bringup/pinky_bringup/battery_publisher.py:38-49](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L38-L49).

XML에는 own Node의 name override·namespace·remapping이나 준비 완료 대기가 없다.
따라서 기재 순서를 프로세스의 초기화 완료 순서로 단정하지 않는다.
IMU/ADC/lamp/LED/emotion을 직접 기동하는 항목도 이 launch에는 없다
([pinky_bringup/launch/bringup_robot.launch.xml:3-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L3-L29)).

### 4.2. 시뮬레이터 인수와 child 연결

[코드 확인] 이 표는 `bringup_sim.launch.xml`에 적힌 기본값과 전달 경로다.
실제 override 이후 적용값은 [미검증]이다.

| 인수 | 기본값 | 전달·사용 대상 | 소스 근거 |
|---|---|---|---|
| `sim` | `mujoco` | 정확한 문자열 비교로 분기 | [pinky_bringup/launch/bringup_sim.launch.xml:14](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L14); [pinky_bringup/launch/bringup_sim.launch.xml:29-30](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L29-L30) |
| `world_name` | `pinky_factory.world` | MuJoCo·Gazebo child | [pinky_bringup/launch/bringup_sim.launch.xml:17](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L17); [pinky_bringup/launch/bringup_sim.launch.xml:38](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L38); [pinky_bringup/launch/bringup_sim.launch.xml:44](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L44) |
| `cam_tilt_deg` | `8` | MuJoCo·Gazebo child | [pinky_bringup/launch/bringup_sim.launch.xml:18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L18); [pinky_bringup/launch/bringup_sim.launch.xml:40](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L40); [pinky_bringup/launch/bringup_sim.launch.xml:45](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L45) |
| `use_gpu` | `false` | MuJoCo child만 | [pinky_bringup/launch/bringup_sim.launch.xml:21](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L21); [pinky_bringup/launch/bringup_sim.launch.xml:35](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L35) |
| `viewer` | `true` | MuJoCo child만 | [pinky_bringup/launch/bringup_sim.launch.xml:22](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L22); [pinky_bringup/launch/bringup_sim.launch.xml:36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L36) |
| `camera` | `true` | MuJoCo child만 | [pinky_bringup/launch/bringup_sim.launch.xml:23](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L23); [pinky_bringup/launch/bringup_sim.launch.xml:37](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L37) |
| `world` | 빈 문자열 | MuJoCo child만 | [pinky_bringup/launch/bringup_sim.launch.xml:24](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L24); [pinky_bringup/launch/bringup_sim.launch.xml:39](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L39) |
| `namespace` | 빈 문자열 | Gazebo child만 | [pinky_bringup/launch/bringup_sim.launch.xml:27](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L27); [pinky_bringup/launch/bringup_sim.launch.xml:46](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L46) |

`sim == "mujoco"`와 `sim in ("gz", "gazebo")`가 실제 조건이다
([pinky_bringup/launch/bringup_sim.launch.xml:29-30](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L29-L30)). 별도 유효성 검사는 없으며,
[추론] 다른 문자열은 어느 include 조건에도 해당하지 않는다.
주석의 simulator 간 Topic 호환 설명을 실제 실행 검증으로 취급하지 않는다
([pinky_bringup/launch/bringup_sim.launch.xml:10-11](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L10-L11)).

| child | 확인한 정적 실행 경로 | 소스 근거 |
|---|---|---|
| MuJoCo | `generate_launch_description()` → `OpaqueFunction(setup)` → URDF 확장 → world 결정 → bridge와 RSP Node 구성 | [pinky_mujoco/launch/launch_sim.launch.py:41-79](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/launch/launch_sim.launch.py#L41-L79); [pinky_mujoco/launch/launch_sim.launch.py:82-94](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/launch/launch_sim.launch.py#L82-L94) |
| MuJoCo world | 명시 `world`가 있으면 해당 경로 사용. 아니면 `.yaml`은 `pinky_mujoco/worlds`, 그 외는 `pinky_gz_sim/worlds` | [pinky_mujoco/launch/launch_sim.launch.py:31-38](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/launch/launch_sim.launch.py#L31-L38) |
| Gazebo | 외부 `ros_gz_sim` 서버/GUI launch 포함, description 포함, create·parameter_bridge·image_bridge 구성 | [pinky_gz_sim/launch/launch_sim.launch.xml:13-35](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/launch/launch_sim.launch.xml#L13-L35) |

MuJoCo child의 bridge와 RSP에는 `use_sim_time=True`가 전달된다
([pinky_mujoco/launch/launch_sim.launch.py:73-78](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/launch/launch_sim.launch.py#L73-L78)). 그 child에는 namespace/remapping이 명시되지 않는다.
Gazebo는 description include에 namespace를 전달하지만 create/bridge Node 자체에
namespace 속성을 지정하지 않는다([pinky_gz_sim/launch/launch_sim.launch.xml:22-35](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/launch/launch_sim.launch.xml#L22-L35)).
상위 `world`는 Gazebo child에 전달되지 않는다.

### 4.3. Node 내부 초기화와 종료

[코드 확인] 아래는 각 Python 실행 경로의 함수 호출 순서다.
서로 다른 프로세스 사이의 준비 완료 동기화를 뜻하지 않는다.

```text
bringup main()
  → rclpy.init()
  → Pinky.__init__()
       → Node("pinky_bringup"), is_initialized=False
       → wheel 파라미터 선언·읽기, circumference 계산
       → DynamixelDriver 생성
       → begin() / 포트·baud 설정
       → initialize_motors()
       → sleep(1.0)
       → set_double_rpm(0, 0)
       → get_feedback() / 초기 encoder 저장
       → publisher/subscriber/TF/timer 생성
       → x=y=theta=0, last_time 설정, is_initialized=True
  → rclpy.spin(node)
  → finally: driver.terminate() → destroy_node() → shutdown()

battery_publisher main()
  → rclpy.init()
  → BatteryPublisher.__init__()
       → Node("battery_publihser")
       → 외부 Battery() 생성
       → Float32 publisher 2개
       → 별도 5초 timer 2개
  → rclpy.spin(publisher)
  → finally: destroy_node() → shutdown()
```

근거는 [pinky_bringup/pinky_bringup/bringup.py:36-101](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L36-L101), [pinky_bringup/pinky_bringup/bringup.py:208-219](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L208-L219),
[pinky_bringup/pinky_bringup/dynamixel_driver.py:25-57](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L25-L57), [pinky_bringup/pinky_bringup/battery_publisher.py:6-49](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L6-L49)다.
모터의 초기화 register·원시 명령·오류 분기·종료 처리는
[모터 제어 분석](../05_Code_Analysis/pinky_bringup--motor-control.md)을 따른다.
실제 함수 실행과 하드웨어 초기화는 수행하지 않았다.

### 4.4. namespace·sim-time·외부 의존성의 경계

[코드 확인] description child의 선언 인수는 `namespace`, `is_sim`,
`cam_tilt_deg`다. 별도의 `use_sim_time` launch 인수를 선언하거나 읽지 않는다.
RSP/JSP의 `use_sim_time`은 `LaunchConfiguration('is_sim')`을 사용한다.
근거: [pinky_description/launch/upload_robot.launch.py:14-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/upload_robot.launch.py#L14-L16), [pinky_description/launch/upload_robot.launch.py:22-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/upload_robot.launch.py#L22-L29), [pinky_description/launch/upload_robot.launch.py:45-57](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/upload_robot.launch.py#L45-L57).

실기 parent가 `use_sim_time`을 전달했다는 사실만으로 own Node나 description의
최종 clock 적용을 확정하지 않는다. own bringup/battery 항목에는 이 파라미터 전달이 없다
([pinky_bringup/launch/bringup_robot.launch.xml:4-27](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L4-L27)). Gazebo child는 `is_sim=True`도 전달한다
([pinky_gz_sim/launch/launch_sim.launch.xml:22-27](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/launch/launch_sim.launch.xml#L22-L27)).

description은 namespace에서 xacro 입력과 `frame_prefix`를 구성하며
JSP의 `source_list=['joint_states']`, `rate=20.0` 및
`('/robot_descrption', 'robot_descrpition')` remapping을 설정한다.
이는 child의 정적 설정이며 외부 JSP의 처리·루프 여부는 확인하지 않았다.
근거: [pinky_description/launch/upload_robot.launch.py:18-20](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/upload_robot.launch.py#L18-L20), [pinky_description/launch/upload_robot.launch.py:30-57](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/upload_robot.launch.py#L30-L57).
own BR의 frame 문자열은 `odom`, `base_footprint`로 고정되어 있다
([pinky_bringup/pinky_bringup/bringup.py:20-21](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L20-L21)).

| 연결 대상 | 선언과 실제 참조의 구분 | 소스 근거 |
|---|---|---|
| `pinky_mujoco` | own manifest의 exec_depend이면서 조건부 include 대상 | [pinky_bringup/package.xml:18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L18); [pinky_bringup/launch/bringup_sim.launch.xml:34-41](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L34-L41) |
| `pinky_description`, `pinky_gz_sim`, `sllidar_ros2` | include는 확인되나 own manifest 직접 의존 선언은 없음 | [pinky_bringup/package.xml:10-18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L10-L18); [pinky_bringup/launch/bringup_robot.launch.xml:8-18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L8-L18); [pinky_bringup/launch/bringup_sim.launch.xml:43-47](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L43-L47) |
| `pinkylib.Battery` | 배터리 Node가 직접 사용. 내부 취득·보정 구현은 외부 영역 | [pinky_bringup/pinky_bringup/battery_publisher.py:1-10](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L1-L10); [pinky_bringup/pinky_bringup/battery_publisher.py:28-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L28-L36) |
| `dynamixel_sdk` | driver가 포트·패킷·group 객체를 사용 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:1-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L1-L29) |

전이 의존성·사전 설치와 설치 성공 여부는 [미검증]이다.
전체 manifest와 import 대조는 [패키지 문서](../02_Packages/pinky_bringup.md)에 정리한다.

## 5. 주요 파라미터

바퀴 파라미터 두 개의 YAML/Python/Launch 기본값, 사용 수식, 단위 확인 여부,
동적 재적용 및 변경 영향은
[파라미터 문서](../03_Parameters/pinky_bringup-parameters.md)를 기준으로 한다.

[코드 확인] 실기 launch는 YAML 뒤에 같은 키의 개별 param도 전달하고,
`Pinky.__init__()`은 값을 한 번 읽어 멤버에 저장한다.
근거: [pinky_bringup/launch/bringup_robot.launch.xml:20-23](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L20-L23), [pinky_bringup/pinky_bringup/bringup.py:43-52](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L43-L52).
실제 override 이후 적용값과 ROS 저장값·계산 멤버값의 일치는 [미검증]이다.
시뮬레이터 선택 인수는 4.2에 별도로 정리했으며, 실기 YAML의 ROS 파라미터와 혼동하지 않는다.

## 6. 관련 문서

- [패키지 개요·파일·통신·의존성](../02_Packages/pinky_bringup.md)
- [모터 제어 함수·수식·드라이버·종료](../05_Code_Analysis/pinky_bringup--motor-control.md)
- [Encoder·Odometry·TF·JointState·배터리 발행](../05_Code_Analysis/pinky_bringup--state-publishing.md)
- [바퀴 파라미터와 하드코딩 설정 구분](../03_Parameters/pinky_bringup-parameters.md)
- [전체 패키지 구조](../00_Overview/package-inventory.md)
- [소스코드 분석 기준](../00_Overview/source-baseline.md)
- [문서 색인](../index.md)

## 7. 검증 및 미확인 사항

| 구분 | 확인 내용 또는 남은 검증 | 근거 |
|---|---|---|
| [코드 확인] | 실기/시뮬레이터 분기와 own Node 초기화·발행 호출 순서 | 2~4절의 기준 SHA 소스 링크 |
| [코드 확인] | 명령 timeout/watchdog와 저전압 정지는 own Python 구현에 없음. 장치 전체의 보호 부재로 확대하지 않음 | [pinky_bringup/pinky_bringup/bringup.py:104-161](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L161); [pinky_bringup/pinky_bringup/bringup.py:197-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L197-L205) |
| [코드 확인] | 초기화 실패 시 생성자 return 경로가 있으나 main은 is_initialized를 검사하지 않고 spin | [pinky_bringup/pinky_bringup/bringup.py:55-80](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L55-L80); [pinky_bringup/pinky_bringup/bringup.py:208-219](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L208-L219) |
| [추론] | 미완성 Node의 spin 및 cleanup 예외 가능성. 실제 재현은 하지 않음 | [pinky_bringup/pinky_bringup/bringup.py:210-219](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L210-L219) |
| [코드 확인] | JointState 이름과 Xacro 이름의 정적 불일치, 오른쪽 odom/joint 부호 차이 | [상태 발행 분석](../05_Code_Analysis/pinky_bringup--state-publishing.md)에서 실제 경로·줄·수식 비교 |
| [미검증] | 실제 모터·센서 초기화, Topic 연결·QoS·TF tree·최종 namespace/remapping·발행주기 | 정적 구성과 분리; 실행 결과 없음 |
| [미검증] | clock·최종 파라미터·override, 외부 LiDAR/JSP·SDK·Battery 동작 | 4~5절의 소스 근거와 별도 확인 필요 |
| [미검증] | 빌드·설치·simulator 호환성·실제 모델 표시·안전 동작 | 실제 실행·빌드·테스트 없음 |

기존 [전체 패키지 구조](../00_Overview/package-inventory.md)의 실기/시뮬레이터·
배터리·선언 의존 관계와 모순되는 내용은 확인하지 않았다.
이번 문서는 기존 정적 조사 내용을 상세 호출 관계로 분리한 것이며,
새로운 런타임 검증 결과를 추가한 문서가 아니다.
