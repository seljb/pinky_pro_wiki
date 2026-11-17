---
type: parameter
title: "pinky_bringup YAML 파라미터"
source_repo: seljb/pinky_pro
source_commit: "014a09f289e6988894fbffde2624fdb1e257815b"
source_paths:
  - pinky_bringup/config/pinky_params.yaml
  - pinky_bringup/pinky_bringup/bringup.py
  - pinky_bringup/launch/bringup_robot.launch.xml
  - pinky_bringup/pinky_bringup/dynamixel_driver.py
  - pinky_bringup/pinky_bringup/battery_publisher.py
verification: 코드 확인
---

# pinky_bringup YAML 파라미터

STEP 8-4-1/8-4-2의 분석을 정리한 설정값·변경 영향 기준 문서다.
템플릿의 기본 정보·역할·코드·관계·영향·검증 항목을 두 파라미터에 각각 적용한다.
`verification: 코드 확인`은 정적 설정·구현에 한정한다. 실제 실행 적용값은 [미검증]이다.
수식의 전체 호출 맥락은 [모터 제어](../05_Code_Analysis/pinky_bringup--motor-control.md)와
[상태 발행](../05_Code_Analysis/pinky_bringup--state-publishing.md)에 둔다.

## 1. 기본 정보

[코드 확인] 설정 파일의 전체 내용은 두 키뿐이다. Node selector `pinky_bringup`과
`ros__parameters`는 YAML 구조이며 별도 ROS 파라미터 키가 아니다.
근거: [pinky_bringup/config/pinky_params.yaml:1-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/config/pinky_params.yaml#L1-L4).

```yaml
pinky_bringup:
  ros__parameters:
    wheel_radius: 0.027
    wheel_separation: 0.0961
```

| 항목 | wheel_radius | wheel_separation | 소스 근거 |
|---|---|---|---|
| 정확한 ROS 파라미터 키 | `wheel_radius` | `wheel_separation` | [pinky_bringup/config/pinky_params.yaml:3-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/config/pinky_params.yaml#L3-L4) |
| YAML 설정값 | `0.027` | `0.0961` | [pinky_bringup/config/pinky_params.yaml:3-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/config/pinky_params.yaml#L3-L4) |
| Python 기본값 | `0.027` | `0.0961` | `Pinky.__init__()`, [pinky_bringup/pinky_bringup/bringup.py:43-44](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L43-L44) |
| Launch 인수 기본값 | `0.027` | `0.0961` | [pinky_bringup/launch/bringup_robot.launch.xml:5-6](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L5-L6) |
| 관련 Node 생성 이름 | `pinky_bringup` | 동일 | `Pinky.__init__()`, [pinky_bringup/pinky_bringup/bringup.py:38](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L38) |
| YAML selector | `pinky_bringup → ros__parameters` | 동일 | [pinky_bringup/config/pinky_params.yaml:1-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/config/pinky_params.yaml#L1-L4) |
| 단위 | YAML·Python 선언에 길이 단위 명시 없음 | 동일 | [pinky_bringup/config/pinky_params.yaml:1-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/config/pinky_params.yaml#L1-L4); [pinky_bringup/pinky_bringup/bringup.py:43-47](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L43-L47) |
| 실제 실행 적용값 | [미검증] | [미검증] | 실행·parameter 조회를 수행하지 않음 |

물리 단위 `m`라는 해석은 ROS 통상 단위와 계산 문맥에 따른 [추론]이며,
소스에 직접 적힌 단위나 실제 기구 치수의 확인 결과가 아니다.
이 문서에서는 물리 단위를 확정하지 않는다.

## 2. 파라미터 역할

### 2.1. wheel_radius

[코드 확인] `self.wheel_radius`에 저장되어 바퀴 둘레와 선속도→바퀴 각속도 변환에 쓰인다.
바퀴 둘레는 encoder 차분을 이동거리로 바꾸는 데 사용한다.
관련 함수: `Pinky.__init__()`, `twist_callback()`, `update_and_publish()`.

| 역할 | 실제 계산식 | 근거 |
|---|---|---|
| 둘레 계산 | `self.circumference = 2 * math.pi * self.wheel_radius` | [pinky_bringup/pinky_bringup/bringup.py:52](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L52) |
| 왼쪽 바퀴 각속도 | `wheel_rads_l = v_l / self.wheel_radius` | [pinky_bringup/pinky_bringup/bringup.py:111](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L111) |
| 오른쪽 바퀴 각속도 | `wheel_rads_r = v_r / self.wheel_radius` | [pinky_bringup/pinky_bringup/bringup.py:112](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L112) |
| 왼쪽 encoder 거리 | `dist_l = (delta_l / PULSE_PER_ROT) * self.circumference` | [pinky_bringup/pinky_bringup/bringup.py:144](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L144) |
| 오른쪽 encoder 거리 | `dist_r = (delta_r / PULSE_PER_ROT) * self.circumference` | [pinky_bringup/pinky_bringup/bringup.py:145](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L145) |

### 2.2. wheel_separation

[코드 확인] `self.wheel_separation`에 저장되어 회전 명령의 좌우 선속도 분배와
좌우 이동거리 차이→yaw 변화 계산에 쓰인다.
관련 함수: `Pinky.twist_callback()`, `update_and_publish()`.

| 역할 | 실제 계산식 | 근거 |
|---|---|---|
| 왼쪽 바퀴 선속도 | `v_l = linear_x - (angular_z * self.wheel_separation / 2.0)` | [pinky_bringup/pinky_bringup/bringup.py:108](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L108) |
| 오른쪽 바퀴 선속도 | `v_r = linear_x + (angular_z * self.wheel_separation / 2.0)` | [pinky_bringup/pinky_bringup/bringup.py:109](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L109) |
| yaw 변화 | `delta_theta = (dist_r - dist_l) / self.wheel_separation` | [pinky_bringup/pinky_bringup/bringup.py:148](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L148) |

두 키 모두 코드 사용이 확인되었다. 파일에는 있으나 코드 사용이 확인되지 않은 YAML 키는 없다.

## 3. 관련 코드

### 3.1. 선언과 읽기

[코드 확인] `Pinky.__init__()`의 실제 선언·읽기·캐시 저장이다.
근거: [pinky_bringup/pinky_bringup/bringup.py:43-52](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L43-L52).

```python
self.declare_parameter('wheel_radius', 0.027)
self.declare_parameter('wheel_separation', 0.0961)

self.wheel_radius = self.get_parameter('wheel_radius').get_parameter_value().double_value
self.wheel_separation = self.get_parameter('wheel_separation').get_parameter_value().double_value

self.circumference = 2 * math.pi * self.wheel_radius
```

### 3.2. Launch 전달

[코드 확인] 실기 Launch는 YAML을 전달한 뒤 같은 키를 개별 param 항목으로 추가한다.
기본값이 모두 같은 사실과 실제 실행 override·형변환·우선순위 결과는 구분한다.
근거: [pinky_bringup/launch/bringup_robot.launch.xml:5-6](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L5-L6), [pinky_bringup/launch/bringup_robot.launch.xml:20-24](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L20-L24).

```xml
<node pkg="pinky_bringup" exec="bringup">
    <param from="$(find-pkg-share pinky_bringup)/config/pinky_params.yaml"/>
    <param name="wheel_radius" value="$(var wheel_radius)"/>
    <param name="wheel_separation" value="$(var wheel_separation)"/>
</node>
```

```text
YAML의 pinky_bringup selector
  → 실기 Launch의 YAML + 개별 param 전달
  → Pinky.__init__의 declare_parameter
  → get_parameter(...).double_value
  → self.wheel_radius / self.wheel_separation / circumference
  → 모터 명령 계산과 encoder 기반 상태 계산
```

[코드 확인] 시뮬레이터 Launch에는 이 YAML과 바퀴 두 값을 child에 전달하는 항목이 없다.
근거: [pinky_bringup/launch/bringup_sim.launch.xml:13-48](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_sim.launch.xml#L13-L48).
실기 Launch의 `use_sim_time`은 launch 인수이며 이 YAML의 파라미터가 아니다.
근거: [pinky_bringup/launch/bringup_robot.launch.xml:4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L4), [pinky_bringup/launch/bringup_robot.launch.xml:8-10](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L8-L10), [pinky_bringup/launch/bringup_robot.launch.xml:20-27](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L20-L27).

## 4. 다른 파라미터와의 관계

[코드 확인] `wheel_separation`이 좌우 선속도 분배에 관여하고,
그 결과를 `wheel_radius`로 나누어 바퀴 각속도를 만든다.
상태 계산에서는 반지름으로 얻은 좌우 이동거리 차이를 간격으로 나눈다.
근거: [pinky_bringup/pinky_bringup/bringup.py:108-115](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L108-L115), [pinky_bringup/pinky_bringup/bringup.py:144-155](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L144-L155).

| 결합 지점 | 읽어야 할 관계 | 근거 |
|---|---|---|
| 명령 변환 | `wheel_separation`으로 좌우 v를 계산한 뒤 `wheel_radius`로 나눔 | [pinky_bringup/pinky_bringup/bringup.py:108-112](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L108-L112) |
| 회전 상태 | `circumference`로 얻은 좌우 거리 차이를 `wheel_separation`으로 나눔 | [pinky_bringup/pinky_bringup/bringup.py:144-148](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L144-L148) |
| 후속 명령 처리 | RPM 변환 뒤 100 RPM 동일비율 제한 및 raw 정수 변환이 이어짐 | [pinky_bringup/pinky_bringup/bringup.py:114-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L114-L125); [pinky_bringup/pinky_bringup/dynamixel_driver.py:59-69](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L59-L69) |

### 4.1. YAML 밖의 하드코딩 설정

아래는 [코드 확인]인 **모듈 상수·지역 상수·함수 기본 인수·직접 지정값**이다.
이 YAML에서 노출한 ROS 파라미터라고 표시하지 않는다.

| 설정 | 코드 값 | 사용·근거 |
|---|---|---|
| 시리얼 포트 | `/dev/ttyAMA4` | `Pinky.__init__()` → driver, [pinky_bringup/pinky_bringup/bringup.py:23-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L23-L25), [pinky_bringup/pinky_bringup/bringup.py:53](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L53) |
| Baud rate / 모터 ID | `1000000` / `[1, 2]` | [pinky_bringup/pinky_bringup/bringup.py:24-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L24-L25) |
| 프로토콜 | `2.0` | `DynamixelDriver.__init__()`, [pinky_bringup/pinky_bringup/dynamixel_driver.py:18-26](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L18-L26) |
| encoder 기준 | `PULSE_PER_ROT = 4096` | [pinky_bringup/pinky_bringup/bringup.py:30](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L30), [pinky_bringup/pinky_bringup/bringup.py:144-145](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L144-L145) |
| RPM→각속도 | `RPM2RAD = 2 * math.pi / 60` | [pinky_bringup/pinky_bringup/bringup.py:31](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L31), [pinky_bringup/pinky_bringup/bringup.py:193](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L193) |
| 원시 속도 변환 | `RPM_TO_VALUE_SCALE = 1 / 0.229` | [pinky_bringup/pinky_bringup/dynamixel_driver.py:23](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L23), [pinky_bringup/pinky_bringup/dynamixel_driver.py:64](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L64), [pinky_bringup/pinky_bringup/dynamixel_driver.py:94-98](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L94-L98) |
| 명령 RPM 상한 | `MAX_RPM = 100.0` | `Pinky.twist_callback()`의 지역 상수, [pinky_bringup/pinky_bringup/bringup.py:117-122](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L117-L122) |
| Profile acceleration | 기본 인수 `profile_accel=200` | `DynamixelDriver.initialize_motors()`, [pinky_bringup/pinky_bringup/dynamixel_driver.py:45-56](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L45-L56); 물리 단위 미검증 |
| 저전압 기준 | `LOW_BATTERY_THRESHOLD = 6.8` | `Pinky.battery_voltage_callback()`, [pinky_bringup/pinky_bringup/bringup.py:33-34](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L33-L34), [pinky_bringup/pinky_bringup/bringup.py:197-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L197-L205); 로그의 전압 단위는 V |
| 상태 timer | `1.0 / 30.0`초 | [pinky_bringup/pinky_bringup/bringup.py:88](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L88) |
| 배터리 timer | `5.0`초, 별도 두 개 | [pinky_bringup/pinky_bringup/battery_publisher.py:24-26](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L24-L26) |
| 모터 준비 대기 | `1.0`초 | [pinky_bringup/pinky_bringup/bringup.py:67](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L67) |
| reboot 뒤 대기 | 각 ID `0.5`초 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:45-51](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L45-L51) |
| 종료 대기 | `0.1`초 두 번 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:36-43](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L36-L43) |
| odom frame | `odom` / `base_footprint` | [pinky_bringup/pinky_bringup/bringup.py:20-21](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L20-L21) |
| Joint 이름 | `left_wheel_joint` / `right_wheel_joint` | [pinky_bringup/pinky_bringup/bringup.py:27-28](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L27-L28) |

Dynamixel register 주소·길이·초기화 쓰기 값은 [모터 제어 문서](../05_Code_Analysis/pinky_bringup--motor-control.md)에 둔다.
위 상수의 물리 사양 적합성·외부 SDK 처리·실제 timer 주기는 [미검증]이다.

## 5. 값 변경 시 예상 영향

아래는 명시한 입력·상태를 고정했을 때 원래 수식으로부터 얻은 [추론]이다.
실제 장치에서 값을 변경해 검증한 결과가 아니다.

| 파라미터 | 예상 영향과 조건 | 수식 근거 |
|---|---|---|
| wheel_radius | 같은 Twist와 간격에서 제한 전 바퀴 각속도/RPM은 반지름을 분모로 사용. 100 RPM 제한과 정수 양자화 이후 최종 출력 전체를 단순 역비례라고 확정하지 않음 | [pinky_bringup/pinky_bringup/bringup.py:108-122](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L108-L122); [pinky_bringup/pinky_bringup/dynamixel_driver.py:64](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L64) |
| wheel_radius | 같은 encoder 차분과 간격에서는 `dist_l/r`, `delta_distance`, `delta_theta`가 반지름에 비례. 그러나 갱신된 theta의 cos/sin이 x/y 적분에도 들어가므로 위치 변화까지 단순 비례라고 할 수 없음 | [pinky_bringup/pinky_bringup/bringup.py:144-152](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L144-L152) |
| wheel_separation | 같은 Twist와 반지름에서 회전 명령의 좌우 선속도 차이에 영향. 그 뒤 RPM 제한이 있으므로 실제 최종 명령을 단순 비례라고 확정하지 않음 | [pinky_bringup/pinky_bringup/bringup.py:108-122](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L108-L122) |
| wheel_separation | 같은 좌우 이동거리 차이를 고정하면 yaw 변화 계산의 분모에 영향. 바뀐 theta는 후속 x/y 적분에도 사용 | [pinky_bringup/pinky_bringup/bringup.py:148-152](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L148-L152) |

둘레는 초기화에서 한 번 계산한다. 이후 설정 저장값만 바뀌어도 계산 멤버와 둘레가 함께
갱신된다고 가정할 수 없다. 근거: [pinky_bringup/pinky_bringup/bringup.py:43-52](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L43-L52), [pinky_bringup/pinky_bringup/bringup.py:104-161](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L161).

## 6. 관련 문서

- [패키지 개요·인터페이스](../02_Packages/pinky_bringup.md)
- [전체 실행·데이터 파이프라인](../01_Architecture/pinky-bringup-pipeline.md)
- [모터 제어 함수와 수식](../05_Code_Analysis/pinky_bringup--motor-control.md)
- [상태 발행 함수와 수식](../05_Code_Analysis/pinky_bringup--state-publishing.md)
- [전체 패키지 구조](../00_Overview/package-inventory.md)
- [소스코드 분석 기준](../00_Overview/source-baseline.md)

## 7. 검증 및 주의사항

- [코드 확인] 두 YAML 키, Python·Launch 기본값, 읽기 위치와 사용 함수가 확인되었다.
- [코드 확인] 자체 코드에는 변경 callback·재조회·계산 멤버의 동적 재적용이 없다.
  이는 ROS 파라미터 저장값 자체가 변경 불가능하다는 뜻이 아니다.
- [코드 확인] 반지름·간격의 0·양수·유효 범위 검사 코드가 없다.
  [추론] 나눗셈·정수 변환에서 예외가 생길 가능성은 있으나 실제 재현은 하지 않았다.
  근거: [pinky_bringup/pinky_bringup/bringup.py:43-47](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L43-L47), [pinky_bringup/pinky_bringup/bringup.py:108-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L108-L125), [pinky_bringup/pinky_bringup/bringup.py:148](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L148), [pinky_bringup/pinky_bringup/dynamixel_driver.py:64](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L64).
- [미검증] 실제 런타임 적용값·override 결과·namespace/name 변경 후 selector 적용,
  실제 기구 치수·보정과 단위, 장치 동작·주행 영향.
- 원본 코드·ROS2·모터·센서·simulator·빌드·테스트를 실행하지 않았다.
  기준 SHA 밖의 미등록 파일은 이 문서의 근거로 사용하지 않았다.
