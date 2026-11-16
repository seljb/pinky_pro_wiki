---
type: overview
title: "Pinky Pro 전체 ROS2 패키지 구조"
source_repo: seljb/pinky_pro
source_commit: "014a09f289e6988894fbffde2624fdb1e257815b"
source_paths:
  - pinky_bringup/package.xml
  - pinky_description/package.xml
  - pinky_emotion/package.xml
  - pinky_gz_sim/package.xml
  - pinky_imu_bno055/package.xml
  - pinky_interfaces/package.xml
  - pinky_lamp_control/package.xml
  - pinky_led/package.xml
  - pinky_mujoco/package.xml
  - pinky_navigation/package.xml
  - pinky_sensor_adc/package.xml
verification: 코드 확인
---

# Pinky Pro 전체 ROS2 패키지 구조

STEP 8-1의 A~G 조사 결과를 검토하고 기준 Commit의 저장소 구조·manifest·주요 참조를
재확인한 STEP 8-3 문서다. 개별 패키지 상세 분석의 출발점으로 사용한다.

Overview 전용 템플릿이 없으므로 [코드 분석 템플릿](../templates/code-analysis.md)의
공통 메타데이터·코드 근거·검증 상태와 [아키텍처 템플릿](../templates/architecture.md)의
구성·연결 서술 형식을 적용했다. 분석 기준의 운영 원칙은
[소스코드 분석 기준](source-baseline.md)을 따른다.

## A. 저장소 및 분석 기준

| 항목 | 확인 결과 |
|---|---|
| Repository | `seljb/pinky_pro` |
| 원본 GitHub 주소 | <https://github.com/seljb/pinky_pro> |
| 원본 로컬 경로 | `/home/ljb/dev_ws/pinky/src/pinky_pro` |
| Wiki 로컬 경로 | `/home/ljb/dev_ws/pinky_pro_wiki` |
| 확인일 | 2026-11-17 |
| 확인한 브랜치 | `feature/nav2-study` |
| 분석 기준 및 확인한 HEAD | `014a09f289e6988894fbffde2624fdb1e257815b` |
| 추적 파일 변경 | staged / unstaged 모두 없음 |
| 미등록 파일 | 기존 3개. G-1에서 별도 표시하고 HEAD 분석에서 제외 |
| 분석 범위 | 위 Commit이 추적하는 파일 157개 |

브랜치와 HEAD·작업 트리 상태는 로컬 Git 조회로 확인했다. 파일 목록은
`git ls-tree -r --name-only <기준 SHA>`, 내용과 줄 번호는
`git show <기준 SHA>:<경로>` 및 `git grep -n <기준 SHA>`를 기준으로 했다.
원격 저장소 상태나 외부 ROS 패키지의 설치 상태는 조회하지 않았다.

| 상태 | 이 문서에서의 의미 |
|---|---|
| [코드 확인] | 기준 Commit의 파일·구현·설정·정적 참조에서 확인한 사실. 메타데이터의 verification도 이 범위 |
| [추론] | 코드 근거를 바탕으로 한 분석 순서 제안이나 영향 가능성 |
| [미검증] | 빌드·실행 성공, 실제 ROS 그래프, 런타임 적용값, 하드웨어 동작 등 |

B~E의 구조·선언·정적 사용 관계는 [코드 확인]이며, 실제 동작은 [미검증]이다.
ROS2·시뮬레이터·로봇·테스트를 실행한 결과를 [실행 확인]으로 기록하지 않는다.
아래 소스 경로는 원본 저장소 내부 상대경로이며 모든 소스 링크는 기준 SHA로 고정했다.

## B. 전체 저장소 디렉터리 구조

[코드 확인] HEAD 추적 파일에서 구성한 트리다. 괄호 안 숫자는 하위 추적 파일 수다.

```text
pinky_pro/
├── .gitignore
├── LICENSE
├── README.md
├── doc/                         (3) 일반 문서·이미지
│   ├── arm64_guide.md
│   ├── architecture.png
│   └── pinky_pro_image.png
├── pinky_bringup/               (14)
│   └── config/, launch/, pinky_bringup/, resource/, test/
├── pinky_description/           (28)
│   └── launch/, meshes/, rviz/, urdf/
├── pinky_emotion/               (19)
│   └── emotion/, pinky_emotion/, resource/, test/
├── pinky_gz_sim/                (20)
│   └── launch/, models/, params/, plugins/, worlds/
├── pinky_imu_bno055/              (3)
│   └── src/
├── pinky_interfaces/              (6)
│   └── srv/
├── pinky_lamp_control/            (4)
│   ├── README.md
│   └── src/
├── pinky_led/                     (9)
│   └── pinky_led/, resource/, test/
├── pinky_mujoco/                 (18)
│   └── config/, launch/, pinky_mujoco/, resource/, test/, tools/, worlds/
├── pinky_navigation/             (27)
│   └── launch/, map/, params/, rviz/, scripts/
└── pinky_sensor_adc/              (3)
    └── src/
```

전체 HEAD 트리의 `package.xml`을 재귀 확인한 결과 최상위 패키지의 11개만 존재하며 중첩 manifest는 없다.

일반 폴더 `doc/`에는 `package.xml`이 없으며 ROS2 패키지로 분류하지 않는다.
`meshes/`, `models/`, `worlds/`, `resource/`, `test/` 등도 독립 패키지가 아니다.
Git 관리 메타데이터 `.git/`은 HEAD 추적 디렉터리 구조에서 제외했다.

| 구분 | 수량 |
|---|---|
| 최상위 디렉터리 | 12개: ROS2 패키지 11개 + 일반 폴더 doc/ |
| Launch 파일 | 20개 |
| YAML 파일 | 8개. 지도와 MuJoCo world YAML 포함 |
| URDF/Xacro 파일 | 4개. 모두 pinky_description 내부 |

## C. ROS2 패키지 11개 전체 목록

[코드 확인] 이름은 각 manifest의 `name`, 빌드 유형은 `export/build_type`에서 확인했다.
역할은 D의 실제 소스·launch 근거로 정리했다.
`pinky_mujoco` 외 10개 manifest의 description은 TODO이므로 이름이나 설명만으로 역할을 추정하지 않았다.

| 패키지 이름 | 실제 저장소 내부 경로 | 목적·역할 | 빌드 유형 | manifest 근거 |
|---|---|---|---|---|
| `pinky_bringup` | `pinky_bringup/` | 실기 기동, Dynamixel 차동 구동·odom/TF·joint state 처리, 배터리 정보 및 simulator 선택 | `ament_python` | [pinky_bringup/package.xml](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml) |
| `pinky_description` | `pinky_description/` | 공통 로봇 형상·관성·조인트·프레임, Gazebo 모델 확장 및 상태 표시 launch 제공 | `ament_cmake` | [pinky_description/package.xml](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/package.xml) |
| `pinky_emotion` | `pinky_emotion/` | GIF 표정의 LCD 표시와 Emotion 서비스 구현 | `ament_python` | [pinky_emotion/package.xml](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/package.xml) |
| `pinky_gz_sim` | `pinky_gz_sim/` | Gazebo world/model 자산, ROS bridge 설정 및 lamp plugin 제공 | `ament_cmake` | [pinky_gz_sim/package.xml](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/package.xml) |
| `pinky_imu_bno055` | `pinky_imu_bno055/` | BNO055의 I2C 읽기 및 IMU 메시지 발행 | `ament_cmake` | [pinky_imu_bno055/package.xml](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_imu_bno055/package.xml) |
| `pinky_interfaces` | `pinky_interfaces/` | 사용자 정의 서비스 인터페이스 4종 생성 | `ament_cmake` | [pinky_interfaces/package.xml](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_interfaces/package.xml) |
| `pinky_lamp_control` | `pinky_lamp_control/` | WS2811 램프의 색·모드·시간 제어 서비스 구현 | `ament_cmake` | [pinky_lamp_control/package.xml](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_lamp_control/package.xml) |
| `pinky_led` | `pinky_led/` | pinkylib.LED를 이용한 픽셀·전체 색·밝기 제어 서비스 구현 | `ament_python` | [pinky_led/package.xml](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/package.xml) |
| `pinky_mujoco` | `pinky_mujoco/` | 공통 URDF의 MuJoCo 모델 변환 및 ROS bridge 제공 | `ament_python` | [pinky_mujoco/package.xml](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/package.xml) |
| `pinky_navigation` | `pinky_navigation/` | 외부 Nav2 구성, SLAM Toolbox 연결, 지도·RViz·웹 인터페이스 제공 | `ament_cmake` | [pinky_navigation/package.xml](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/package.xml) |
| `pinky_sensor_adc` | `pinky_sensor_adc/` | I2C ADC 기반 초음파 거리·IR 원시 배열·배터리 상태 메시지 발행 | `ament_cmake` | [pinky_sensor_adc/package.xml](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_sensor_adc/package.xml) |

합계는 `ament_python` 4개, `ament_cmake` 7개다.
빌드 유형 확인과 실제 빌드 성공 여부는 구분한다.

## D. 패키지별 주요 파일 및 구성

각 표는 모두 [코드 확인]이다. 소스·구성 파일 목록과 근거 링크는 저장소 내부 경로다.
설치·진입점 설명의 `setup.py`와 `CMakeLists.txt`는 각 표의 패키지 루트 기준이다.
빌드·설치 설명은 설정에 기재된 동작이지 실제 산출물을 검증한 결과가 아니다.
Launch/YAML/URDF-Xacro 목록은 HEAD에서 확인한 전체 목록이다.
'없음'은 해당 패키지의 HEAD 트리에 해당 형식의 파일이 없다는 뜻이다.

### D-1. `pinky_bringup`

`Pinky`가 속도 명령을 바퀴 RPM으로 변환하고 encoder 기반 odometry를 계산한다. `BatteryPublisher`는 외부 `pinkylib.Battery`에서 읽은 값을 발행한다.

| 항목 | HEAD 파일 및 확인 내용 |
|---|---|
| 실제 패키지 루트 | `pinky_bringup/` |
| 주요 소스·인터페이스 | `pinky_bringup/pinky_bringup/battery_publisher.py`<br>`pinky_bringup/pinky_bringup/bringup.py`<br>`pinky_bringup/pinky_bringup/dynamixel_driver.py` |
| 빌드·설치 파일 | `pinky_bringup/setup.cfg`<br>`pinky_bringup/setup.py` |
| 설치·진입점·플랫폼 조건 | `setup.py:10-15,24-28`에 launch/config 설치와 `bringup`, `battery_publisher` console script 등록. |
| Launch 전체 | `pinky_bringup/launch/bringup_robot.launch.xml`<br>`pinky_bringup/launch/bringup_sim.launch.xml` |
| YAML 전체 | `pinky_bringup/config/pinky_params.yaml` |
| URDF/Xacro 전체 | 없음 |
| 기타 주요 파일·자산 | `pinky_bringup/resource/pinky_bringup`, `pinky_bringup/test/`. |
| 근거 파일·줄 | [pinky_bringup/pinky_bringup/bringup.py:36-95](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L36-L95)<br>[pinky_bringup/pinky_bringup/bringup.py:104-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L205)<br>[pinky_bringup/pinky_bringup/battery_publisher.py:4-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L4-L36)<br>[pinky_bringup/pinky_bringup/dynamixel_driver.py:25-69](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L25-L69)<br>[pinky_bringup/setup.py:10-28](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/setup.py#L10-L28) |
| 확인 상태 | [코드 확인]; 빌드·실행·실제 연결 [미검증] |

### D-2. `pinky_description`

공통 Xacro와 mesh를 제공하고 외부 robot/joint state publisher 및 RViz를 launch로 구성한다. 자체 구동 노드 구현 패키지는 아니다.

| 항목 | HEAD 파일 및 확인 내용 |
|---|---|
| 실제 패키지 루트 | `pinky_description/` |
| 주요 소스·인터페이스 | 없음 |
| 빌드·설치 파일 | `pinky_description/CMakeLists.txt` |
| 설치·진입점·플랫폼 조건 | `CMakeLists.txt:12-19`에서 launch, meshes, rviz, urdf 설치. |
| Launch 전체 | `pinky_description/launch/upload_robot.launch.py`<br>`pinky_description/launch/view_robot.launch.py` |
| YAML 전체 | 없음 |
| URDF/Xacro 전체 | `pinky_description/urdf/common/insert_inertia.urdf.xacro`<br>`pinky_description/urdf/pinky.urdf.xacro`<br>`pinky_description/urdf/pinky_gz.urdf.xacro`<br>`pinky_description/urdf/robot.urdf.xacro` |
| 기타 주요 파일·자산 | `pinky_description/meshes/visual/`의 DAE, `pinky_description/meshes/collision/`의 STL, `pinky_description/rviz/view_robot.rviz`. |
| 근거 파일·줄 | [pinky_description/launch/upload_robot.launch.py:22-59](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/upload_robot.launch.py#L22-L59)<br>[pinky_description/launch/view_robot.launch.py:17-49](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/view_robot.launch.py#L17-L49)<br>[pinky_description/urdf/pinky.urdf.xacro:298-301](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/urdf/pinky.urdf.xacro#L298-L301)<br>[pinky_description/CMakeLists.txt:12-19](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/CMakeLists.txt#L12-L19) |
| 확인 상태 | [코드 확인]; 빌드·실행·실제 연결 [미검증] |

### D-3. `pinky_emotion`

`Emotion` 서비스 인터페이스를 사용하여 GIF 자산을 LCD 표시 코드에 연결한다. 두 console entrypoint가 존재한다.

| 항목 | HEAD 파일 및 확인 내용 |
|---|---|
| 실제 패키지 루트 | `pinky_emotion/` |
| 주요 소스·인터페이스 | `pinky_emotion/pinky_emotion/emotion_server.py`<br>`pinky_emotion/pinky_emotion/pinky_emotion.py`<br>`pinky_emotion/pinky_emotion/pinky_lcd.py` |
| 빌드·설치 파일 | `pinky_emotion/setup.cfg`<br>`pinky_emotion/setup.py` |
| 설치·진입점·플랫폼 조건 | `setup.py:14,24-26`에 GIF 설치와 `emotion_server`, `pinky_emotion` console script 등록. |
| Launch 전체 | 없음 |
| YAML 전체 | 없음 |
| URDF/Xacro 전체 | 없음 |
| 기타 주요 파일·자산 | `pinky_emotion/emotion/`의 `angry.gif`, `basic.gif`, `bored.gif`, `fun.gif`, `happy.gif`, `hello.gif`, `interest.gif`, `sad.gif`; `pinky_emotion/test/`. |
| 근거 파일·줄 | [pinky_emotion/pinky_emotion/emotion_server.py:7-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/pinky_emotion/emotion_server.py#L7-L25)<br>[pinky_emotion/pinky_emotion/emotion_server.py:60-60](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/pinky_emotion/emotion_server.py#L60-L60)<br>[pinky_emotion/pinky_emotion/pinky_lcd.py:1-11](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/pinky_emotion/pinky_lcd.py#L1-L11)<br>[pinky_emotion/setup.py:14-26](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/setup.py#L14-L26) |
| 확인 상태 | [코드 확인]; 빌드·실행·실제 연결 [미검증] |

### D-4. `pinky_gz_sim`

외부 ros_gz simulator·bridge·image bridge와 공통 로봇 모델을 구성한다. lamp plugin을 위한 C++ 소스도 포함한다.

| 항목 | HEAD 파일 및 확인 내용 |
|---|---|
| 실제 패키지 루트 | `pinky_gz_sim/` |
| 주요 소스·인터페이스 | `pinky_gz_sim/plugins/gz_lamp_control_plugin.cpp`<br>`pinky_gz_sim/plugins/gz_lamp_control_plugin.hpp` |
| 빌드·설치 파일 | `pinky_gz_sim/CMakeLists.txt` |
| 설치·진입점·플랫폼 조건 | `CMakeLists.txt:8-10`은 aarch64에서 조기 반환. `:20-36`은 gz-sim8/gz-plugin2 발견 시 lamp 공유 라이브러리를 생성·설치. |
| Launch 전체 | `pinky_gz_sim/launch/launch_sim.launch.xml` |
| YAML 전체 | `pinky_gz_sim/params/pinky_bridge.yaml` |
| URDF/Xacro 전체 | 없음 |
| 기타 주요 파일·자산 | `pinky_gz_sim/worlds/default.sdf`, `pinky_gz_sim/worlds/empty.world`, `pinky_gz_sim/worlds/pinky_factory.world`, `pinky_gz_sim/worlds/pinky_map.world`; `pinky_gz_sim/models/shelf/`, `pinky_gz_sim/models/robot/jetcobot.dae`, `pinky_gz_sim/models/img/`. |
| 근거 파일·줄 | [pinky_gz_sim/launch/launch_sim.launch.xml:8-35](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/launch/launch_sim.launch.xml#L8-L35)<br>[pinky_gz_sim/params/pinky_bridge.yaml:1-41](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/params/pinky_bridge.yaml#L1-L41)<br>[pinky_gz_sim/CMakeLists.txt:8-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/CMakeLists.txt#L8-L36)<br>[pinky_gz_sim/plugins/gz_lamp_control_plugin.cpp:65-65](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/plugins/gz_lamp_control_plugin.cpp#L65-L65) |
| 확인 상태 | [코드 확인]; 빌드·실행·실제 연결 [미검증] |

### D-5. `pinky_imu_bno055`

`PinkyIMUBNO055`에서 BNO055 센서 접근과 메시지 발행을 구현한다.

| 항목 | HEAD 파일 및 확인 내용 |
|---|---|
| 실제 패키지 루트 | `pinky_imu_bno055/` |
| 주요 소스·인터페이스 | `pinky_imu_bno055/src/main_node.cpp` |
| 빌드·설치 파일 | `pinky_imu_bno055/CMakeLists.txt` |
| 설치·진입점·플랫폼 조건 | `CMakeLists.txt:16-29`의 aarch64 조건에서 `main_node` 생성·설치. 일부 find_package는 이 조건문 앞에 있다. |
| Launch 전체 | 없음 |
| YAML 전체 | 없음 |
| URDF/Xacro 전체 | 없음 |
| 기타 주요 파일·자산 | 별도 launch/config/model 자산 없음. |
| 근거 파일·줄 | [pinky_imu_bno055/src/main_node.cpp:10-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_imu_bno055/src/main_node.cpp#L10-L25)<br>[pinky_imu_bno055/CMakeLists.txt:13-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_imu_bno055/CMakeLists.txt#L13-L29) |
| 확인 상태 | [코드 확인]; 빌드·실행·실제 연결 [미검증] |

### D-6. `pinky_interfaces`

`rosidl_generate_interfaces`로 Emotion, SetBrightness, SetLamp, SetLed 서비스를 생성하도록 구성한다. MSG_FILES/ACTION_FILES는 비어 있으며 독립 실행 파일은 없다.

| 항목 | HEAD 파일 및 확인 내용 |
|---|---|
| 실제 패키지 루트 | `pinky_interfaces/` |
| 주요 소스·인터페이스 | `pinky_interfaces/srv/Emotion.srv`<br>`pinky_interfaces/srv/SetBrightness.srv`<br>`pinky_interfaces/srv/SetLamp.srv`<br>`pinky_interfaces/srv/SetLed.srv` |
| 빌드·설치 파일 | `pinky_interfaces/CMakeLists.txt` |
| 설치·진입점·플랫폼 조건 | `CMakeLists.txt:15-37`의 서비스 등록·생성 설정. `package.xml:22`는 rosidl_interface_packages 그룹 선언. |
| Launch 전체 | 없음 |
| YAML 전체 | 없음 |
| URDF/Xacro 전체 | 없음 |
| 기타 주요 파일·자산 | `pinky_interfaces/srv/Emotion.srv`, `pinky_interfaces/srv/SetBrightness.srv`, `pinky_interfaces/srv/SetLamp.srv`, `pinky_interfaces/srv/SetLed.srv`. |
| 근거 파일·줄 | [pinky_interfaces/CMakeLists.txt:15-37](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_interfaces/CMakeLists.txt#L15-L37)<br>[pinky_interfaces/srv/SetLamp.srv:1-1](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_interfaces/srv/SetLamp.srv#L1-L1)<br>[pinky_interfaces/package.xml:22-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_interfaces/package.xml#L22-L25) |
| 확인 상태 | [코드 확인]; 빌드·실행·실제 연결 [미검증] |

### D-7. `pinky_lamp_control`

`PinkyLampControl`에서 SetLamp 서비스와 WS2811 제어를 연결한다.

| 항목 | HEAD 파일 및 확인 내용 |
|---|---|
| 실제 패키지 루트 | `pinky_lamp_control/` |
| 주요 소스·인터페이스 | `pinky_lamp_control/src/main_node.cpp` |
| 빌드·설치 파일 | `pinky_lamp_control/CMakeLists.txt` |
| 설치·진입점·플랫폼 조건 | `CMakeLists.txt:18-35`의 aarch64 조건에서 `main_node` 생성·설치. ws2811 라이브러리와 /usr/local/include 경로 사용. |
| Launch 전체 | 없음 |
| YAML 전체 | 없음 |
| URDF/Xacro 전체 | 없음 |
| 기타 주요 파일·자산 | `pinky_lamp_control/README.md`. |
| 근거 파일·줄 | [pinky_lamp_control/src/main_node.cpp:3-68](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_lamp_control/src/main_node.cpp#L3-L68)<br>[pinky_lamp_control/CMakeLists.txt:15-35](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_lamp_control/CMakeLists.txt#L15-L35) |
| 확인 상태 | [코드 확인]; 빌드·실행·실제 연결 [미검증] |

### D-8. `pinky_led`

SetLed, SetBrightness 서비스 인터페이스와 외부 `pinkylib.LED`를 사용한다.

| 항목 | HEAD 파일 및 확인 내용 |
|---|---|
| 실제 패키지 루트 | `pinky_led/` |
| 주요 소스·인터페이스 | `pinky_led/pinky_led/led_server.py` |
| 빌드·설치 파일 | `pinky_led/setup.cfg`<br>`pinky_led/setup.py` |
| 설치·진입점·플랫폼 조건 | `setup.py:22-23`에 `led_server` console script 등록. |
| Launch 전체 | 없음 |
| YAML 전체 | 없음 |
| URDF/Xacro 전체 | 없음 |
| 기타 주요 파일·자산 | `pinky_led/resource/pinky_led`, `pinky_led/test/`. |
| 근거 파일·줄 | [pinky_led/pinky_led/led_server.py:1-15](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L1-L15)<br>[pinky_led/setup.py:14-23](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/setup.py#L14-L23) |
| 확인 상태 | [코드 확인]; 빌드·실행·실제 연결 [미검증] |

### D-9. `pinky_mujoco`

공통 Xacro를 확장하고 모델·mesh·world를 변환한다. Gazebo world/model 데이터를 읽는 경로도 있다.

| 항목 | HEAD 파일 및 확인 내용 |
|---|---|
| 실제 패키지 루트 | `pinky_mujoco/` |
| 주요 소스·인터페이스 | `pinky_mujoco/pinky_mujoco/bridge.py`<br>`pinky_mujoco/pinky_mujoco/description.py`<br>`pinky_mujoco/pinky_mujoco/meshes.py`<br>`pinky_mujoco/pinky_mujoco/model.py`<br>`pinky_mujoco/pinky_mujoco/world.py` |
| 빌드·설치 파일 | `pinky_mujoco/setup.cfg`<br>`pinky_mujoco/setup.py` |
| 설치·진입점·플랫폼 조건 | `setup.py:8-12`는 aarch64에서 빈 패키지 설정 후 종료. `:33-36`에 `bridge=pinky_mujoco.bridge:main` 등록. |
| Launch 전체 | `pinky_mujoco/launch/launch_sim.launch.py` |
| YAML 전체 | `pinky_mujoco/config/mujoco.yaml`<br>`pinky_mujoco/worlds/room.yaml` |
| URDF/Xacro 전체 | 없음 |
| 기타 주요 파일·자산 | `pinky_mujoco/test/test_physics.py`; `pinky_mujoco/tools/check_ros.py`, `pinky_mujoco/tools/drive_check.py`, `pinky_mujoco/tools/save_image.py`, `pinky_mujoco/tools/stop_sim.sh`. |
| 근거 파일·줄 | [pinky_mujoco/pinky_mujoco/description.py:6-12](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/pinky_mujoco/description.py#L6-L12)<br>[pinky_mujoco/pinky_mujoco/model.py:28-30](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/pinky_mujoco/model.py#L28-L30)<br>[pinky_mujoco/pinky_mujoco/world.py:41-46](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/pinky_mujoco/world.py#L41-L46)<br>[pinky_mujoco/pinky_mujoco/bridge.py:127-144](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/pinky_mujoco/bridge.py#L127-L144)<br>[pinky_mujoco/setup.py:8-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/setup.py#L8-L36) |
| 확인 상태 | [코드 확인]; 빌드·실행·실제 연결 [미검증] |

### D-10. `pinky_navigation`

Nav2 위치추정·계획·제어와 SLAM Toolbox를 launch로 구성한다. Flask 기반 웹 서버의 지도·목표·SLAM 인터페이스를 포함한다. Nav2 핵심 알고리즘 자체 구현은 외부 패키지에 있다.

| 항목 | HEAD 파일 및 확인 내용 |
|---|---|
| 실제 패키지 루트 | `pinky_navigation/` |
| 주요 소스·인터페이스 | `pinky_navigation/scripts/nav2_web_server.py` |
| 빌드·설치 파일 | `pinky_navigation/CMakeLists.txt` |
| 설치·진입점·플랫폼 조건 | `CMakeLists.txt:26-39`에서 웹 서버·HTML·이미지 및 launch/params/map/rviz 설치. |
| Launch 전체 | `pinky_navigation/launch/bringup_launch.xml`<br>`pinky_navigation/launch/gz_bringup_launch.xml`<br>`pinky_navigation/launch/gz_map_building.launch.xml`<br>`pinky_navigation/launch/gz_map_view.launch.xml`<br>`pinky_navigation/launch/gz_nav2_view.launch.xml`<br>`pinky_navigation/launch/gz_web_nav2.launch.xml`<br>`pinky_navigation/launch/gz_web_slam.launch.xml`<br>`pinky_navigation/launch/localization_launch.xml`<br>`pinky_navigation/launch/map_building.launch.xml`<br>`pinky_navigation/launch/map_view.launch.xml`<br>`pinky_navigation/launch/nav2_view.launch.xml`<br>`pinky_navigation/launch/navigation_launch.xml`<br>`pinky_navigation/launch/web_nav2.launch.xml`<br>`pinky_navigation/launch/web_slam.launch.xml` |
| YAML 전체 | `pinky_navigation/map/my_map.yaml`<br>`pinky_navigation/map/pinklab.yaml`<br>`pinky_navigation/params/mapper_params.yaml`<br>`pinky_navigation/params/nav2_params.yaml` |
| URDF/Xacro 전체 | 없음 |
| 기타 주요 파일·자산 | `pinky_navigation/scripts/index.html`, `pinky_navigation/scripts/pinklab_logo.png`; `pinky_navigation/map/my_map.pgm`, `pinky_navigation/map/pinklab.png`; `pinky_navigation/rviz/map_building.rviz`, `pinky_navigation/rviz/nav2_view.rviz`. |
| 근거 파일·줄 | [pinky_navigation/launch/bringup_launch.xml:24-52](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/bringup_launch.xml#L24-L52)<br>[pinky_navigation/launch/localization_launch.xml:17-72](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/localization_launch.xml#L17-L72)<br>[pinky_navigation/launch/navigation_launch.xml:21-182](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/navigation_launch.xml#L21-L182)<br>[pinky_navigation/scripts/nav2_web_server.py:7-33](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/scripts/nav2_web_server.py#L7-L33)<br>[pinky_navigation/scripts/nav2_web_server.py:88-175](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/scripts/nav2_web_server.py#L88-L175)<br>[pinky_navigation/CMakeLists.txt:26-39](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/CMakeLists.txt#L26-L39) |
| 확인 상태 | [코드 확인]; 빌드·실행·실제 연결 [미검증] |

### D-11. `pinky_sensor_adc`

`PinkySensorADC`에서 ADC 읽기와 메시지 변환·발행을 구현한다.

| 항목 | HEAD 파일 및 확인 내용 |
|---|---|
| 실제 패키지 루트 | `pinky_sensor_adc/` |
| 주요 소스·인터페이스 | `pinky_sensor_adc/src/main_node.cpp` |
| 빌드·설치 파일 | `pinky_sensor_adc/CMakeLists.txt` |
| 설치·진입점·플랫폼 조건 | `CMakeLists.txt:15-28`의 aarch64 조건에서 `main_node` 생성·설치. 일부 find_package는 이 조건문 앞에 있다. |
| Launch 전체 | 없음 |
| YAML 전체 | 없음 |
| URDF/Xacro 전체 | 없음 |
| 기타 주요 파일·자산 | 별도 launch/config/model 자산 없음. |
| 근거 파일·줄 | [pinky_sensor_adc/src/main_node.cpp:11-99](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_sensor_adc/src/main_node.cpp#L11-L99)<br>[pinky_sensor_adc/CMakeLists.txt:12-28](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_sensor_adc/CMakeLists.txt#L12-L28) |
| 확인 상태 | [코드 확인]; 빌드·실행·실제 연결 [미검증] |

## E. 선언 의존성과 실제 정적 참조·연결

### E-1. package.xml 선언 의존성

[코드 확인] 아래는 manifest에 실제 선언된 값이다. 각 행의 근거는 C의 동일 패키지 manifest다.
`—`는 해당 태그가 없다는 뜻이다. 빌드 유형이 ament_python이라는 사실과
`buildtool_depend`에 ament_python을 선언했는지는 별개다.

| 패키지 | buildtool_depend | depend | exec_depend |
|---|---|---|---|
| `pinky_bringup` | — | `tf_transformations` | `pinky_mujoco` |
| `pinky_description` | `ament_cmake` | `xacro`<br>`robot_state_publisher`<br>`joint_state_publisher`<br>`joint_state_publisher_gui` | — |
| `pinky_emotion` | — | — | — |
| `pinky_gz_sim` | `ament_cmake` | `ros_gz`<br>`gz_ros2_control` | — |
| `pinky_imu_bno055` | `ament_cmake` | `rclcpp`<br>`sensor_msgs`<br>`std_msgs` | — |
| `pinky_interfaces` | `ament_cmake`<br>`rosidl_default_generators` | `std_msgs`<br>`action_msgs`<br>`builtin_interfaces` | `rosidl_default_runtime` |
| `pinky_lamp_control` | `ament_cmake` | `rclcpp`<br>`std_msgs`<br>`pinky_interfaces` | — |
| `pinky_led` | — | — | — |
| `pinky_mujoco` | `ament_python` | — | `pinky_description`<br>`rclpy`<br>`ament_index_python`<br>`launch`<br>`launch_ros`<br>`xacro`<br>`robot_state_publisher`<br>`builtin_interfaces`<br>`geometry_msgs`<br>`sensor_msgs`<br>`nav_msgs`<br>`rosgraph_msgs`<br>`tf2_ros`<br>`python3-numpy`<br>`python3-yaml`<br>`python3-scipy`<br>`python3-pil`<br>`pinky_gz_sim` |
| `pinky_navigation` | `ament_cmake`<br>`ament_cmake_python` | `navigation2`<br>`nav2_bringup` | — |
| `pinky_sensor_adc` | `ament_cmake` | `rclcpp`<br>`sensor_msgs` | — |

테스트 의존성도 실행 의존성과 구분한다.

| 대상 패키지 | test_depend |
|---|---|
| pinky_bringup, pinky_emotion, pinky_led | `ament_copyright`, `ament_flake8`, `ament_pep257`, `python3-pytest` |
| pinky_description, pinky_gz_sim, pinky_imu_bno055, pinky_interfaces, pinky_lamp_control, pinky_navigation, pinky_sensor_adc | `ament_lint_auto`, `ament_lint_common` |
| pinky_mujoco | `python3-pytest` |

**저장소 내부 패키지 간 선언 관계는 다음 4개다.** 화살표는 의존하는 패키지에서 대상으로 향한다.

| 의존 패키지 | 대상 패키지 | 선언 태그 | 소스 근거 |
|---|---|---|---|
| `pinky_bringup` | `pinky_mujoco` | `exec_depend` | [pinky_bringup/package.xml:18-18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/package.xml#L18-L18) |
| `pinky_mujoco` | `pinky_description` | `exec_depend` | [pinky_mujoco/package.xml:12-12](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/package.xml#L12-L12) |
| `pinky_mujoco` | `pinky_gz_sim` | `exec_depend` | [pinky_mujoco/package.xml:30-30](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/package.xml#L30-L30) |
| `pinky_lamp_control` | `pinky_interfaces` | `depend` | [pinky_lamp_control/package.xml:14-14](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_lamp_control/package.xml#L14-L14) |

### E-2. 소스·Launch·모델의 내부 정적 참조

[코드 확인] include/import/모델·plugin 참조를 manifest 선언과 구분했다.
실제 실행 성공이나 설치 상태를 뜻하지 않는다.

| 참조 관계 | 코드에서 확인한 사용·조건 | 참조하는 쪽 manifest의 대상 의존성 선언 | 소스 근거 |
|---|---|---|---|
| pinky_bringup → pinky_description | 실기 launch에서 모델 upload launch 포함 | 없음 | [pinky_bringup/launch/bringup_robot.launch.xml:8-10](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L8-L10) |
| pinky_bringup → pinky_mujoco | 기본 sim=mujoco에서 launch 포함 | 있음 | [pinky_bringup/launch/bringup_sim.launch.xml:14-40](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L14-L40) |
| pinky_bringup → pinky_gz_sim | sim=gz/gazebo 조건에서 launch 포함 | 없음 | [pinky_bringup/launch/bringup_sim.launch.xml:29-46](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L29-L46) |
| pinky_gz_sim → pinky_description | 모델 upload launch 및 자산 경로 사용 | 없음 | [pinky_gz_sim/launch/launch_sim.launch.xml:11-27](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/launch/launch_sim.launch.xml#L11-L27) |
| pinky_description → pinky_gz_sim plugin | is_sim 조건 Xacro에서 lamp plugin 라이브러리 참조 | 없음 | [pinky_description/urdf/pinky.urdf.xacro:298-301](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/urdf/pinky.urdf.xacro#L298-L301); [pinky_description/urdf/pinky_gz.urdf.xacro:32-34](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/urdf/pinky_gz.urdf.xacro#L32-L34) |
| pinky_mujoco → pinky_description | 공통 Xacro를 is_sim=false로 확장 | 있음 | [pinky_mujoco/pinky_mujoco/description.py:6-12](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/pinky_mujoco/description.py#L6-L12) |
| pinky_mujoco → pinky_gz_sim | world 및 model 데이터 탐색 | 있음 | [pinky_mujoco/launch/launch_sim.launch.py:31-38](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/launch/launch_sim.launch.py#L31-L38); [pinky_mujoco/pinky_mujoco/world.py:41-46](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/pinky_mujoco/world.py#L41-L46) |
| pinky_lamp_control → pinky_interfaces | SetLamp include·서비스 생성·CMake 의존 | 있음 | [pinky_lamp_control/src/main_node.cpp:3-3](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_lamp_control/src/main_node.cpp#L3-L3); [pinky_lamp_control/src/main_node.cpp:62-68](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_lamp_control/src/main_node.cpp#L62-L68); [pinky_lamp_control/CMakeLists.txt:15-20](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_lamp_control/CMakeLists.txt#L15-L20) |
| pinky_led → pinky_interfaces | SetLed·SetBrightness import·사용 | 없음 | [pinky_led/pinky_led/led_server.py:6-15](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L6-L15) |
| pinky_emotion → pinky_interfaces | Emotion import·사용 | 없음 | [pinky_emotion/pinky_emotion/emotion_server.py:7-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/pinky_emotion/emotion_server.py#L7-L25) |

`pinky_description`과 `pinky_gz_sim`의 조건부 모델·plugin 참조를 manifest의 빌드 순환 의존으로 단정하지 않는다.
MuJoCo의 Gazebo 자산 사용은 Gazebo 프로세스 기동 관계가 아니다.

### E-3. 주요 외부 참조와 선언의 차이

[코드 확인] '직접 선언 없음'은 해당 manifest와 소스/빌드 파일을 대조한 결과다.
전이 의존성이나 사전 설치가 제공하는지와 깨끗한 환경의 설치·빌드 성공 여부는 [미검증]이다.

| 패키지 | 실제 외부 참조 및 선언 차이 | 소스 근거 |
|---|---|---|
| pinky_bringup | sllidar_ros2, rclpy, ROS 메시지, tf2_ros, pinkylib, dynamixel_sdk 사용. 각 항목의 직접 선언 없음 | [pinky_bringup/launch/bringup_robot.launch.xml:12-18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L12-L18); [pinky_bringup/pinky_bringup/bringup.py:3-15](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L3-L15); [pinky_bringup/pinky_bringup/battery_publisher.py:1-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L1-L4); [pinky_bringup/pinky_bringup/dynamixel_driver.py:2-2](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L2-L2) |
| pinky_description | xacro·상태 발행기·GUI publisher는 선언. rviz2는 사용하지만 직접 선언 없음 | [pinky_description/package.xml:12-15](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/package.xml#L12-L15); [pinky_description/launch/view_robot.launch.py:17-49](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/view_robot.launch.py#L17-L49) |
| pinky_gz_sim | ros_gz_sim/ros_gz_bridge/ros_gz_image 사용. manifest는 집합 패키지 ros_gz 선언. 빌드에서는 gz-sim8/gz-plugin2 탐색 | [pinky_gz_sim/launch/launch_sim.launch.xml:13-35](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/launch/launch_sim.launch.xml#L13-L35); [pinky_gz_sim/CMakeLists.txt:20-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/CMakeLists.txt#L20-L36) |
| pinky_mujoco | ROS·여러 Python 실행 의존은 선언. mujoco/trimesh/pycollada의 pip 요구사항은 XML 주석이며 의존 태그가 아님. setup.py의 install_requires는 setuptools만 포함 | [pinky_mujoco/package.xml:10-31](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/package.xml#L10-L31); [pinky_mujoco/setup.py:26-26](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/setup.py#L26-L26) |
| pinky_navigation | Nav2 세부 패키지, slam_toolbox, rviz2, Flask/rclpy/TF2/메시지 라이브러리 참조. manifest의 depend 선언은 navigation2/nav2_bringup | [pinky_navigation/package.xml:10-17](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/package.xml#L10-L17); [pinky_navigation/launch/map_building.launch.xml:6-8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/map_building.launch.xml#L6-L8); [pinky_navigation/launch/map_view.launch.xml:6-6](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/map_view.launch.xml#L6-L6); [pinky_navigation/scripts/nav2_web_server.py:7-33](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/scripts/nav2_web_server.py#L7-L33) |
| pinky_imu_bno055 | angles, realtime_tools, wiringPi를 빌드에서 참조하나 직접 manifest 선언 없음 | [pinky_imu_bno055/CMakeLists.txt:13-20](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_imu_bno055/CMakeLists.txt#L13-L20) |
| pinky_sensor_adc | std_msgs, realtime_tools, wiringPi를 빌드에서 참조하나 직접 manifest 선언 없음 | [pinky_sensor_adc/CMakeLists.txt:12-19](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_sensor_adc/CMakeLists.txt#L12-L19) |
| pinky_lamp_control | ws2811 라이브러리 및 /usr/local/include 사용. ws2811 직접 선언 없음 | [pinky_lamp_control/CMakeLists.txt:21-26](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_lamp_control/CMakeLists.txt#L21-L26) |
| pinky_led | rclpy/pinkylib 사용. 실행 의존 직접 선언 없음; install_requires는 setuptools만 포함 | [pinky_led/pinky_led/led_server.py:1-6](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L1-L6); [pinky_led/setup.py:14-14](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/setup.py#L14-L14) |
| pinky_emotion | rclpy/ament_index_python/PIL/spidev/RPi.GPIO/numpy 사용. 실행 의존 직접 선언 없음; install_requires는 setuptools만 포함 | [pinky_emotion/pinky_emotion/emotion_server.py:1-8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/pinky_emotion/emotion_server.py#L1-L8); [pinky_emotion/pinky_emotion/pinky_lcd.py:1-5](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/pinky_emotion/pinky_lcd.py#L1-L5); [pinky_emotion/setup.py:16-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/setup.py#L16-L16) |

### E-4. 주요 데이터 및 실행 연결

다음 표는 [코드 확인]인 정적 구성과 이름 대응이다. 실제 ROS 그래프·메시지 수신·TF·QoS·
namespace/remap 적용 및 simulator 간 호환성은 모두 [미검증]이다.

| 연결 | 확인된 구성·데이터 계약 | 근거 |
|---|---|---|
| 실기 기동 | bringup_robot launch가 description upload, 외부 sllidar_ros2 launch, bringup, battery_publisher를 직접 구성 | [pinky_bringup/launch/bringup_robot.launch.xml:3-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L3-L29) |
| 시뮬레이터 선택 | 기본 MuJoCo 또는 조건부 Gazebo launch 포함. 서로 다른 실행 경로 | [pinky_bringup/launch/bringup_sim.launch.xml:14-46](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L14-L46) |
| navigation 내부 실행 구성 | 자체 localization/navigation launch 및 외부 Nav2 구성을 사용. bringup/simulator를 직접 포함하지 않음 | [pinky_navigation/launch/bringup_launch.xml:24-52](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/bringup_launch.xml#L24-L52); D-10의 launch 전체 목록 |
| 속도 명령·odometry | bringup의 cmd_vel 구독·odom/joint_states 발행과 navigation의 cmd_vel remap·odom/scan 설정이 정적으로 대응 | [pinky_bringup/pinky_bringup/bringup.py:84-95](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L84-L95); [pinky_navigation/launch/navigation_launch.xml:21-93](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/navigation_launch.xml#L21-L93); [pinky_navigation/params/nav2_params.yaml:39-47](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L39-L47); [pinky_navigation/params/nav2_params.yaml:202-234](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L202-L234) |
| Gazebo bridge | cmd_vel의 ROS→GZ, clock/tf/scan/camera_info/joint_states/odom 등의 GZ→ROS 매핑 | [pinky_gz_sim/params/pinky_bridge.yaml:1-41](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/params/pinky_bridge.yaml#L1-L41) |
| MuJoCo bridge | /cmd_vel와 /cmd_vel_stamped 구독, /clock·/odom·/ground_truth·/scan·/joint_states 발행 코드 | [pinky_mujoco/pinky_mujoco/bridge.py:127-144](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/pinky_mujoco/bridge.py#L127-L144) |
| 배터리 정보 | battery_publisher가 pinkylib.Battery에서 읽어 battery/voltage(Float32)를 발행하고 bringup이 같은 이름·타입을 구독하여 저전압 경고에 사용 | [pinky_bringup/pinky_bringup/battery_publisher.py:4-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L4-L36); [pinky_bringup/pinky_bringup/bringup.py:33-34](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L33-L34); [pinky_bringup/pinky_bringup/bringup.py:90-95](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L90-L95); [pinky_bringup/pinky_bringup/bringup.py:197-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L197-L205) |
| ADC 센서 정보 | us_sensor/range(Range), ir_sensor/range(UInt16MultiArray), batt_state(BatteryState) 발행. battery/voltage(Float32)와 구분 | [pinky_sensor_adc/src/main_node.cpp:27-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_sensor_adc/src/main_node.cpp#L27-L29) |
| SLAM 연결 | 자체 mapper YAML을 외부 slam_toolbox의 online_sync launch에 전달 | [pinky_navigation/launch/map_building.launch.xml:4-8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/map_building.launch.xml#L4-L8) |
| 웹 인터페이스 | map/plan/costmap 구독, navigate_to_pose ActionClient, initialpose 발행, SLAM save/reset 서비스 client 생성 코드 | [pinky_navigation/scripts/nav2_web_server.py:88-175](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/scripts/nav2_web_server.py#L88-L175) |
| 공통 모델/서비스 계약 | description 모델을 실기·sim에서 재사용; interfaces 서비스 타입을 lamp/LED/emotion에서 사용 | E-2 및 D-2/D-6의 실제 소스 근거 |

실기 bringup launch에는 IMU/ADC/lamp/LED/emotion을 직접 기동하는 항목이 없다
(`pinky_bringup/launch/bringup_robot.launch.xml:3-29`).
배터리 발행기가 ADC 패키지와 자동으로 연결된다고 가정하지 않는다.
외부 패키지 내부 구현이나 파일 존재·설치 여부는 이 조사 범위에서 확인하지 않았다.

## F. 상세 분석 권장 순서

[추론] 아래는 코드 읽기와 개별 Wiki 작성 순서에 대한 제안이며 빌드 순서·실행 절차가 아니다.
확인한 구현과 참조를 근거로 정리했으며 근거 파일은 D와 E에서 연결했다.

| 순서 | 패키지 | 상세 분석 범위 및 추천 이유 |
|---|---|---|
| 1 | pinky_bringup | 실기/sim 진입점과 cmd_vel → 바퀴 구동 → odom/TF, 배터리 데이터 흐름 파악 |
| 2 | pinky_description | 공통 형상·조인트·프레임 및 실기/sim Xacro 분기 확정 |
| 3 | pinky_gz_sim | ROS bridge 계약과 world/model 자산 파악. MuJoCo도 이 자산을 사용 |
| 4 | pinky_mujoco | 기본 simulator의 모델 변환·world 로딩·물리/센서 bridge 분석 |
| 5 | pinky_navigation | scan/odom/TF와 Nav2 입출력을 연결하고 localization·계획·제어·SLAM·웹을 세분화 |
| 6 | pinky_interfaces | 주변장치가 사용하는 서비스 계약 4종을 공통 기준으로 문서화 |
| 7 | pinky_imu_bno055 | 센서 프레임·단위·I2C·발행 코드와 플랫폼 조건 조사 |
| 8 | pinky_sensor_adc | 초음파·IR·배터리 데이터 변환과 I2C 조사. bringup 배터리 구현과 구분 |
| 9 | pinky_lamp_control | SetLamp 계약과 WS2811 제어 구현 연결 |
| 10 | pinky_led | SetLed/SetBrightness, pinkylib 호출과 의존성 선언 분석 |
| 11 | pinky_emotion | GIF → LCD 표시 경로, 두 entrypoint 차이와 외부 라이브러리 요구사항 분석 |

이 문서로 개별 패키지 상세 분석이나 파라미터 카탈로그가 완료된 것은 아니다.
아직 생성되지 않은 개별 분석 문서의 Wiki 링크는 만들지 않았다.

## G. 미등록 파일 및 향후 검증 항목

### G-1. HEAD 분석에서 제외한 미등록 파일

로컬 Git 조회에서 다음 3개 경로가 `??`로 표시되었다.
기준 HEAD 트리에 없으며, 개수·파일 목록·역할·의존성·설정 분석 근거에서 제외했다.

| 로컬 미등록 경로 | 처리 |
|---|---|
| `pinky_navigation/launch/localization_only_launch.xml` | HEAD의 Launch로 취급하지 않음 |
| `pinky_navigation/nav2_send_goal_basic.py` | HEAD의 소스 구현으로 취급하지 않음 |
| `pinky_navigation/params/amcl_params.yaml` | HEAD의 YAML/파라미터 근거로 취급하지 않음 |

기준 SHA는 커밋 객체를 식별하며 위 로컬 파일의 존재나 내용을 반영하지 않는다.
이 SHA만으로 해당 파일 내용을 재현할 수 없다. 내용·용도·과거 이력은 분석하지 않았다.
미등록 파일의 경계에 대한 운영 원칙은 [분석 기준 문서](source-baseline.md)에 따른다.

### G-2. 향후 검증이 필요한 항목

| 항목 | 확인된 정적 사실 및 남은 검증 | 실제 소스 근거 |
|---|---|---|
| 빌드·설치 의존성 | [코드 확인] E-3의 직접 선언과 참조 차이. [미검증] 설치 버전·전이 의존성·깨끗한 환경의 빌드 성공 | E-1/E-3의 각 manifest·소스·빌드 파일 |
| 플랫폼별 산출물 | [코드 확인] 하드웨어 main_node 생성은 aarch64 조건이며 simulator에도 아키텍처 분기 존재. 일부 find_package는 하드웨어 조건문 앞에 있음. [미검증] 실제 설치 산출물 | [pinky_imu_bno055/CMakeLists.txt:13-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_imu_bno055/CMakeLists.txt#L13-L29); [pinky_sensor_adc/CMakeLists.txt:12-28](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_sensor_adc/CMakeLists.txt#L12-L28); [pinky_lamp_control/CMakeLists.txt:15-35](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_lamp_control/CMakeLists.txt#L15-L35); [pinky_gz_sim/CMakeLists.txt:8-10](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/CMakeLists.txt#L8-L10); [pinky_mujoco/setup.py:8-12](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/setup.py#L8-L12) |
| 바퀴 파라미터 | [코드 확인] YAML과 launch에 wheel_radius/wheel_separation 값·전달 코드가 있음. [미검증] 실제 실행 override 이후 적용값 | [pinky_bringup/config/pinky_params.yaml:1-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/config/pinky_params.yaml#L1-L4); [pinky_bringup/launch/bringup_robot.launch.xml:5-23](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L5-L23) |
| Nav2 params 선택 | [코드 확인] 단독 navigation launch의 기본 params는 외부 nav2_bringup 경로, 상위 bringup은 자체 YAML 전달. [미검증] 실제 진입점의 최종 적용 파일 | [pinky_navigation/launch/navigation_launch.xml:8-8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/navigation_launch.xml#L8-L8); [pinky_navigation/launch/bringup_launch.xml:8-8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/bringup_launch.xml#L8-L8); [pinky_navigation/launch/bringup_launch.xml:45-46](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/bringup_launch.xml#L45-L46) |
| sim-time 전달 | [코드 확인] gz_web_nav2와 gz_web_slam의 웹 서버 use_sim_time 전달이 다름. description upload의 use_sim_time 값은 is_sim 구성을 사용. [미검증] 실제 clock/인수 적용 | [pinky_navigation/launch/gz_web_nav2.launch.xml:9-12](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/gz_web_nav2.launch.xml#L9-L12); [pinky_navigation/launch/gz_web_slam.launch.xml:9-12](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/gz_web_slam.launch.xml#L9-L12); [pinky_description/launch/upload_robot.launch.py:14-59](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/upload_robot.launch.py#L14-L59) |
| Gazebo lamp plugin | [코드 확인] 조건부 빌드 및 /world/pinky_factory/material_color 고정 경로. [미검증] plugin 로딩과 다른 world 동작 | [pinky_gz_sim/CMakeLists.txt:20-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/CMakeLists.txt#L20-L36); [pinky_gz_sim/plugins/gz_lamp_control_plugin.cpp:65-65](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_gz_sim/plugins/gz_lamp_control_plugin.cpp#L65-L65) |
| description remapping 표기 | [코드 확인] robot_descrption / robot_descrpition 문자열 존재. [미검증] 실제 remapping 영향 | [pinky_description/launch/upload_robot.launch.py:55-57](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/launch/upload_robot.launch.py#L55-L57) |
| emotion 기존 entrypoint | [코드 확인] 기존 entrypoint 대상은 set_emotion_callback을 참조하나 정의된 콜백은 lcd_callback. 별도 emotion_server에는 set_emotion_callback 정의. [추론] 초기화에 영향을 줄 가능성. [미검증] 실제 실패 여부 | [pinky_emotion/setup.py:24-26](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/setup.py#L24-L26); [pinky_emotion/pinky_emotion/pinky_emotion.py:15-19](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/pinky_emotion/pinky_emotion.py#L15-L19); [pinky_emotion/pinky_emotion/emotion_server.py:60-60](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_emotion/pinky_emotion/emotion_server.py#L60-L60) |
| Nav2 lifecycle | [코드 확인] 상위 bringup의 lifecycle 목록에는 smoother_server가 없고 navigation launch에는 해당 서버 구성. [미검증] 실제 관리 상태·영향 | [pinky_navigation/launch/bringup_launch.xml:15-20](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/bringup_launch.xml#L15-L20); [pinky_navigation/launch/navigation_launch.xml:32-32](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/navigation_launch.xml#L32-L32); [pinky_navigation/launch/navigation_launch.xml:110-110](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/navigation_launch.xml#L110-L110) |
| ROS 그래프·하드웨어 | [미검증] 실제 Node/Topic/Service/Action 존재·응답, namespace/remap, QoS, TF, clock, 모터·센서·LCD·LED 동작 | D/E의 정적 구현과 구분; 실제 실행 결과 없음 |
| 바이너리 자산·테스트 | [코드 확인] 자산·test/tools 경로와 설치 설정 확인. [미검증] 형상·물리·표시 정확성 및 테스트 성공 | D-2/D-3/D-4/D-9의 파일 목록 |

원본 소스는 읽기 전용으로 취급했다. ROS2·로봇·simulator·test/tools를 실행하지 않았다.
MuJoCo launch에는 실행 시 캐시 디렉터리와 URDF를 쓰는 코드가 있지만
([pinky_mujoco/launch/launch_sim.launch.py:47-50](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_mujoco/launch/launch_sim.launch.py#L47-L50)) 이번에는 그 코드를 읽기만 했다.
문서의 정적 사실을 빌드·실행 성공으로 해석하지 않는다.

## 관련 Wiki 문서

- [문서 색인](../index.md)
- [소스코드 분석 기준](source-baseline.md)
- [프로젝트 안내 및 진행 상황](../README.md)
- [작업 기록](../log.md)
- [LLM 작업 규칙](../AGENTS.md)
- [문서 템플릿 및 연결 규칙](../templates/README.md)
