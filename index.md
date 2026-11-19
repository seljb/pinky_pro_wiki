# Pinky Pro learning index — 2026-11-19

Start with the roadmap. Each new learning page uses English prose, diagrams and tables; source references are optional audit trails. Existing Korean analysis remains preserved below this current index.

| Page | Learning purpose |
|---|---|
| [Learning roadmap](00_Overview/learning-roadmap.md) | Complete first study unit, six sessions, worked examples and bringup reference connections. |
| [System overview](01_Architecture/system-overview.md) | Eleven packages, launch boundaries and command/feedback loops. |
| [Motion and feedback](04_Features/motion-and-feedback.md) | English bringup lesson: RPM, encoders, odometry, TF and battery. |
| [Robot description and TF](01_Architecture/robot-description-and-tf.md) | Links, joints, frame ownership and the joint-name mismatch. |
| [Simulation pipeline](01_Architecture/simulation-pipeline.md) | MuJoCo/Gazebo launch selection, bridges, clock and sensor differences. |
| [Navigation and SLAM](01_Architecture/navigation-pipeline.md) | Saved maps, AMCL, mapping, planning, control and lifecycle boundaries. |
| [Sensors and expression](01_Architecture/sensors-and-expression.md) | Independent peripheral nodes, messages and service paths. |
| [ROS 2 interface atlas](04_Features/ros2-interface-atlas.md) | Topics, services, actions, TF and message-contract distinctions. |
| [pinky_description](02_Packages/pinky_description.md) | Shared Xacro, robot frames and description publishers. |
| [pinky_gz_sim](02_Packages/pinky_gz_sim.md) | Gazebo plugins, worlds and ROS/Gazebo bridges. |
| [pinky_mujoco](02_Packages/pinky_mujoco.md) | Description conversion, physics, simulated sensors and ROS adapter. |
| [pinky_navigation](02_Packages/pinky_navigation.md) | Navigation entrypoints, external dependencies and browser bridge. |
| [pinky_imu_bno055](02_Packages/pinky_imu_bno055.md) | BNO055 acquisition, message fields and unit questions. |
| [pinky_sensor_adc](02_Packages/pinky_sensor_adc.md) | ADC channels, range, IR and separate battery state. |
| [pinky_interfaces](02_Packages/pinky_interfaces.md) | Four shared service definitions and implementation links. |
| [pinky_lamp_control](02_Packages/pinky_lamp_control.md) | Lamp modes, timing and hardware service implementation. |
| [pinky_led](02_Packages/pinky_led.md) | Pixel/color/brightness services through pinkylib. |
| [pinky_emotion](02_Packages/pinky_emotion.md) | GIF/LCD expression servers and legacy entrypoint caveat. |
| [Navigation parameters](03_Parameters/navigation-parameters.md) | Source values, launch routing and expected parameter effects. |
| [Navigation web walkthrough](05_Code_Analysis/navigation-web-control.md) | Browser coordinates, cached state and asynchronous request outcomes. |
| [Latest local review and validation](06_Change_History/2026-11-19-learning-wiki-review.md) | This session's actual file changes, preserved baseline and validation scope. |
| [Previous evidence and validation](06_Change_History/2026-11-18-learning-wiki-evidence.md) | Preserved earlier writing and validation record. |

[Home](README.md) · [Operation log](log.md) · [Agent instructions](AGENTS.md) · [Document templates](templates/README.md)

## Preserved reference analysis

- [Source baseline, 2026-11-17](00_Overview/source-baseline.md)
- [Complete package inventory](00_Overview/package-inventory.md)
- [Bringup package](02_Packages/pinky_bringup.md)
- [Bringup pipeline](01_Architecture/pinky-bringup-pipeline.md)
- [Motor implementation](05_Code_Analysis/pinky_bringup--motor-control.md)
- [State publishing implementation](05_Code_Analysis/pinky_bringup--state-publishing.md)
- [Bringup parameter catalog](03_Parameters/pinky_bringup-parameters.md)

---

## Historical index — preserved from 2026-11-17

The original index below is retained verbatim. Its statement that ten package analyses are pending describes that earlier date; use the current index above for the expanded English route.

# Pinky Pro LLM Wiki Index

## 프로젝트 안내

- [README](README.md) — 목적과 구축 현황
- [LLM 작업 규칙](AGENTS.md)
- [작업 기록](log.md)

## Wiki 문서 분류

| 분류 | 내용 |
|---|---|
| 00_Overview | 전체 시스템 개요 |
| 01_Architecture | ROS2 파이프라인과 데이터 흐름 |
| 02_Packages | 패키지, Node 및 역할 |
| 03_Parameters | 파라미터 및 영향 관계 |
| 04_Features | 요구사항과 관련 코드의 연결 |
| 05_Code_Analysis | 소스코드 상세 분석 |
| 06_Change_History | 코드 변경 이력 |
| 07_Experiments | 실험 및 검증 기록 |
| 08_Troubleshooting | 문제와 해결 과정 |

## 문서 색인

- [소스코드 분석 기준](00_Overview/source-baseline.md) — 분석 기준 Git 버전, 미등록 파일 및 읽기 전용 작업 범위
- [전체 ROS2 패키지 구조](00_Overview/package-inventory.md) — 패키지 11개, 선언 의존성과 정적 참조, 상세 분석 권장 순서

- [pinky_bringup 패키지](02_Packages/pinky_bringup.md) — 전체 파일, 실행 진입점, 자체 Node·인터페이스와 선언·실제 의존성
- [pinky_bringup 파이프라인](01_Architecture/pinky-bringup-pipeline.md) — 실기·시뮬레이터 Launch 분기, 초기화 및 모터·상태·배터리 흐름
- [pinky_bringup 모터 제어](05_Code_Analysis/pinky_bringup--motor-control.md) — cmd_vel 수식, 100 RPM 동일비율 제한, Dynamixel 전송·초기화·종료
- [pinky_bringup 상태 발행](05_Code_Analysis/pinky_bringup--state-publishing.md) — encoder 기반 Odometry·TF·JointState, Xacro 이름 비교와 배터리 발행
- [pinky_bringup 파라미터](03_Parameters/pinky_bringup-parameters.md) — YAML 두 키의 기본값·전달·사용 수식·변경 영향과 하드코딩 값 구분

pinky_bringup 상세 분석 문서 5개를 등록했습니다. 나머지 10개 패키지 상세 분석 및 기능 분석 문서는 아직 생성되지 않았습니다.

LLM은 새 문서를 생성할 때 해당 문서의
링크와 한 줄 설명을 여기에 추가해야 합니다.
