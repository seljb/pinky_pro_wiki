---
type: code_analysis
title: "pinky_bringup 상태 발행"
source_repo: seljb/pinky_pro
source_commit: "014a09f289e6988894fbffde2624fdb1e257815b"
source_paths:
  - pinky_bringup/pinky_bringup/dynamixel_driver.py
  - pinky_bringup/pinky_bringup/bringup.py
  - pinky_bringup/pinky_bringup/battery_publisher.py
  - pinky_description/urdf/pinky.urdf.xacro
verification: 코드 확인
---

# pinky_bringup 상태 발행

## 1. 분석 대상

STEP 8-4-1·8-4-2의 기존 분석을 문서화하고 필요한 구간만 기준 Commit에서 다시 읽었다.
이 문서의 모든 소스 경로·줄 번호·원본 링크는
`014a09f289e6988894fbffde2624fdb1e257815b` 기준이다. [코드 확인]은 정적 구현 확인이며 실행 성공을 뜻하지 않는다.

| 소스 파일 | 클래스·함수와 분석 범위 | 코드 근거 |
|---|---|---|
| `pinky_bringup/pinky_bringup/dynamixel_driver.py` | `DynamixelDriver.get_feedback()`: 모터 속도·encoder 취득과 반환 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:71-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L71-L100) |
| `pinky_bringup/pinky_bringup/bringup.py` | `Pinky.__init__()`: 초기 encoder·pose 및 통신·timer 구성 | [pinky_bringup/pinky_bringup/bringup.py:75-101](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L75-L101) |
| `pinky_bringup/pinky_bringup/bringup.py` | `update_and_publish()`, `_publish_tf()`, `_publish_odometry()`, `_publish_joint_states()`, `battery_voltage_callback()` | [pinky_bringup/pinky_bringup/bringup.py:127-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L127-L205) |
| `pinky_bringup/pinky_bringup/battery_publisher.py` | `BatteryPublisher`, callback 2개 및 `main()` | [pinky_bringup/pinky_bringup/battery_publisher.py:6-49](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L6-L49) |
| `pinky_description/urdf/pinky.urdf.xacro` | `insert_wheel` 매크로의 Joint 이름과 호출을 정적으로 비교 | [pinky_description/urdf/pinky.urdf.xacro:33-63](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/urdf/pinky.urdf.xacro#L33-L63) |

미등록 파일 3개는 [분석 기준 문서](../00_Overview/source-baseline.md)의 경계에 따라 제외했다.
목표 RPM을 만드는 수식은 [모터 제어 분석](pinky_bringup--motor-control.md),
YAML 설정값·기본값·변경 영향은 [파라미터 문서](../03_Parameters/pinky_bringup-parameters.md)를 기준으로 한다.

## 2. 코드의 목적

[코드 확인] 모터의 Present Velocity·Present Position을 읽고, encoder 차분에서
로봇의 위치·회전각·속도를 계산해 Odometry·TF·JointState로 전달한다.
배터리 경로는 별도 `BatteryPublisher`가 외부 `pinkylib.Battery`에서 읽은 값을
발행하고, `Pinky`가 전압을 구독해 경고하는 구성이다.

Odometry의 속도는 encoder 차분으로 계산한다. JointState의 속도는
Present Velocity에서 얻은 RPM을 사용하므로 두 데이터의 출처가 다르다.
근거: [pinky_bringup/pinky_bringup/bringup.py:132-159](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L132-L159), [pinky_bringup/pinky_bringup/bringup.py:185-195](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L185-L195), [pinky_bringup/pinky_bringup/battery_publisher.py:28-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L28-L36).

## 3. 입력 및 출력

아래는 모두 [코드 확인]이다. 일반 Topic 이름은 선행 `/`가 없는 상대 이름이며,
최종 namespace·CLI remapping·실제 통신 결과는 [미검증]이다.

| Node 생성 이름 | 입력·출력 | 타입·내용 | 함수 | 코드 근거 |
|---|---|---|---|---|
| `pinky_bringup` | driver feedback 입력 | `(rpm_l, rpm_r, encoder_l, encoder_r)` 또는 모두 None | `DynamixelDriver.get_feedback()` → `Pinky.update_and_publish()` | [pinky_bringup/pinky_bringup/dynamixel_driver.py:71-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L71-L100); [pinky_bringup/pinky_bringup/bringup.py:132-136](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L132-L136) |
| `pinky_bringup` | `odom` 발행 | `nav_msgs/msg/Odometry`, QoS 인수 10 | `_publish_odometry()` | [pinky_bringup/pinky_bringup/bringup.py:18-18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L18-L18); [pinky_bringup/pinky_bringup/bringup.py:84-84](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L84-L84); [pinky_bringup/pinky_bringup/bringup.py:174-183](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L174-L183) |
| `pinky_bringup` | TF 송신 | `TransformStamped` 인수, `odom → base_footprint` | `_publish_tf()` → `sendTransform()` | [pinky_bringup/pinky_bringup/bringup.py:20-21](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L20-L21); [pinky_bringup/pinky_bringup/bringup.py:87-87](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L87-L87); [pinky_bringup/pinky_bringup/bringup.py:163-172](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L163-L172) |
| `pinky_bringup` | `joint_states` 발행 | `sensor_msgs/msg/JointState`, QoS 인수 10 | `_publish_joint_states()` | [pinky_bringup/pinky_bringup/bringup.py:19-19](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L19-L19); [pinky_bringup/pinky_bringup/bringup.py:85-85](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L85-L85); [pinky_bringup/pinky_bringup/bringup.py:185-195](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L185-L195) |
| `pinky_bringup` | `battery/voltage` 구독 | `std_msgs/msg/Float32`, QoS 인수 10 | `battery_voltage_callback()` | [pinky_bringup/pinky_bringup/bringup.py:33-34](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L33-L34); [pinky_bringup/pinky_bringup/bringup.py:90-95](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L90-L95); [pinky_bringup/pinky_bringup/bringup.py:197-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L197-L205) |
| `battery_publihser` | `battery/percent` 발행 | `std_msgs/msg/Float32`, QoS 인수 10 | `percentage_callback()` | [pinky_bringup/pinky_bringup/battery_publisher.py:8-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L8-L16); [pinky_bringup/pinky_bringup/battery_publisher.py:28-31](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L28-L31) |
| `battery_publihser` | `battery/voltage` 발행 | `std_msgs/msg/Float32`, QoS 인수 10 | `voltage_callback()` | [pinky_bringup/pinky_bringup/battery_publisher.py:18-22](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L18-L22); [pinky_bringup/pinky_bringup/battery_publisher.py:33-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L33-L36) |

배터리 Node의 실제 철자는 `battery_publihser`이며 실행 파일 `battery_publisher`와 구분한다.
TF transport Topic 이름과 QoS는 이 패키지에서 직접 지정하지 않고
외부 `tf2_ros.TransformBroadcaster`에 위임한다.

일반 pub/sub의 정수 QoS 인수 외 세부 정책이나 외부 TF transport 동작을
이 소스에서 직접 확인한 것으로 확정하지 않는다.

## 4. 실행 흐름

### 4.1. 초기 기준과 주기

[코드 확인] `Pinky.__init__()`는 최초 feedback의 encoder를
`self.last_encoder_l/r`에 저장한다. 초기 pose는 `x=y=theta=0.0`이며,
encoder 자체를 0으로 만드는 코드는 아니다.
근거: [pinky_bringup/pinky_bringup/bringup.py:75-101](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L75-L101).

| 처리 | 코드 설정 | 실제 호출·발행 결과 | 코드 근거 |
|---|---|---|---|
| 상태 갱신 timer | `1.0 / 30.0`초, 명목 30 Hz | [미검증] 실제 주기·I/O 지연·지터 | [pinky_bringup/pinky_bringup/bringup.py:88-88](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L88-L88) |
| 백분율 timer | `5.0`초, 명목 0.2 Hz | [미검증] 실제 sample·주기 | [pinky_bringup/pinky_bringup/battery_publisher.py:24-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L24-L25) |
| 전압 timer | 별도 `5.0`초 timer, 명목 0.2 Hz | [미검증] 백분율과의 동기성·주기 | [pinky_bringup/pinky_bringup/battery_publisher.py:24-26](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L24-L26) |

```text
Pinky.__init__()
  → 최초 get_feedback()의 encoder 저장
  → pub/sub/TF 및 상태 timer 생성
  → x=y=theta=0, last_time 설정

상태 timer
  → Pinky.update_and_publish()
      → clock.now(), dt
      → DynamixelDriver.get_feedback()
      → encoder 차분·이동거리·yaw·x/y·속도 계산
      → _publish_tf()
      → _publish_odometry()
      → _publish_joint_states()
      → last_time 갱신
```

위 흐름의 근거는 [pinky_bringup/pinky_bringup/bringup.py:75-101](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L75-L101), [pinky_bringup/pinky_bringup/bringup.py:127-161](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L127-L161)이다.
`dt <= 0`이면 feedback도 읽기 전에 반환한다. feedback 실패에서는 경고 후 반환하며
직전 encoder와 `last_time`을 유지한다. 다음 정상 갱신에서는 마지막 성공 시점 이후의
차분과 시간 간격을 함께 사용한다. 출력 stamp는 하드웨어 읽기 완료 후 새로 취득한 시각이
아니라 callback 시작 시의 `current_time`이다.

### 4.2. Dynamixel feedback 취득

[코드 확인] `get_feedback()`는 BulkRead 파라미터를 초기화하고 각 모터에
주소 128부터 8바이트를 등록한다. 통신 실패 또는 어느 한쪽의 가용성 검사 실패이면
`(None, None, None, None)`을 반환한다. `addParam()` 반환값은 검사하지 않는다.
근거: [pinky_bringup/pinky_bringup/dynamixel_driver.py:71-84](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L71-L84).

| 데이터 | 주소·길이 | 코드 처리·반환 | 코드 근거 |
|---|---|---|---|
| Present Velocity | 128, 4바이트 | raw 값을 부호 보정한 뒤 `RPM_TO_VALUE_SCALE`로 나눠 RPM 반환 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:11-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L11-L16); [pinky_bringup/pinky_bringup/dynamixel_driver.py:86-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L86-L100) |
| Present Position | 132, 4바이트 | raw 값을 부호 보정한 encoder 위치 반환 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:11-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L11-L16); [pinky_bringup/pinky_bringup/dynamixel_driver.py:86-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L86-L100) |
| 환산 계수 | `RPM_TO_VALUE_SCALE = 1 / 0.229` | 명령 변환 계수의 역변환으로 feedback RPM 계산 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:23-23](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L23-L23); [pinky_bringup/pinky_bringup/dynamixel_driver.py:92-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L92-L100) |

실제 부호 보정·환산 코드는 다음과 같다.

```python
if vel_raw_l > 2**31: vel_raw_l -= 2**32
if pos_raw_l > 2**31: pos_raw_l -= 2**32
rpm_l = vel_raw_l / self.RPM_TO_VALUE_SCALE

if vel_raw_r > 2**31: vel_raw_r -= 2**32
if pos_raw_r > 2**31: pos_raw_r -= 2**32
rpm_r = vel_raw_r / self.RPM_TO_VALUE_SCALE

return rpm_l, rpm_r, pos_raw_l, pos_raw_r
```

근거: [pinky_bringup/pinky_bringup/dynamixel_driver.py:92-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L92-L100). 조건은 `>=`가 아니라 **`>`**이다.
SDK의 raw 값 표현·모터 모드·제어표 호환성과 실제 측정 정확성은 [미검증]이다.

### 4.3. Encoder 차분과 위치·회전각 적분

[코드 확인] `PULSE_PER_ROT = 4096`,
`self.circumference = 2 * math.pi * self.wheel_radius`를 사용한다.
근거: [pinky_bringup/pinky_bringup/bringup.py:30-31](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L30-L31), [pinky_bringup/pinky_bringup/bringup.py:52-52](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L52-L52).

`Pinky.update_and_publish()`의 계산·호출 순서를 보존한 코드는 다음과 같다.

```python
current_time = self.get_clock().now()
dt = (current_time - self.last_time).nanoseconds / 1e9
if dt <= 0: return

feedback = self.driver.get_feedback()
if feedback[0] is None:
    self.get_logger().warn("Failed to read motor data. Skipping update cycle.")
    return
rpm_l, rpm_r, encoder_l, encoder_r = feedback

delta_l = encoder_l - self.last_encoder_l
delta_r = -(encoder_r - self.last_encoder_r)

self.last_encoder_l = encoder_l
self.last_encoder_r = encoder_r

dist_l = (delta_l / PULSE_PER_ROT) * self.circumference
dist_r = (delta_r / PULSE_PER_ROT) * self.circumference

delta_distance = (dist_r + dist_l) / 2.0
delta_theta = (dist_r - dist_l) / self.wheel_separation

self.theta += delta_theta
self.x += delta_distance * math.cos(self.theta)
self.y += delta_distance * math.sin(self.theta)

v_x = delta_distance / dt if dt > 0 else 0.0
vth = delta_theta / dt if dt > 0 else 0.0

self._publish_tf(current_time)
self._publish_odometry(current_time, v_x, vth)
self._publish_joint_states(current_time, rpm_l, rpm_r)

self.last_time = current_time
```

근거: [pinky_bringup/pinky_bringup/bringup.py:127-161](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L127-L161).

오른쪽 encoder 차분만 반전한다. x/y 적분에는 **이미 갱신한 theta**를 사용하며,
중간 yaw로 대체한 수식을 구현했다고 서술하지 않는다.
`v_x/vth`는 encoder 차분 기반이며 feedback RPM은 JointState 속도에 사용한다.
encoder wrap/rollover 보정, delta 이상치 제한, theta 정규화 코드는 이 함수에 없다.

길이의 물리 단위는 YAML·선언 코드에서 명시적으로 확인되지 않았다.
ROS 관례에 따른 단위 해석과 실제 기구·encoder 보정은 구분하며, 물리적 정확성은 [미검증]이다.

### 4.4. TF와 Odometry 메시지

[코드 확인] 두 함수는 같은 `current_time`과 `x/y/theta`를 사용한다.

| 항목 | `Pinky._publish_tf()` | `Pinky._publish_odometry()` |
|---|---|---|
| stamp | `current_time.to_msg()` | 동일 |
| parent/header frame | `odom` | `odom` |
| child frame | `base_footprint` | `base_footprint` |
| 위치 | `translation.x/y = self.x/y` | `pose.pose.position.x/y = self.x/y` |
| 회전 | `quaternion_from_euler(0, 0, self.theta)` | 같은 yaw quaternion |
| 속도 | 별도 속도 성분 대입 없음 | `twist.twist.linear.x = v_x`, `angular.z = vth` |
| 출력 | `sendTransform(t)` | `odom_pub.publish(odom_msg)` |
| 원본 파일·줄 | [pinky_bringup/pinky_bringup/bringup.py:163-172](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L163-L172) | [pinky_bringup/pinky_bringup/bringup.py:174-183](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L174-L183) |

TF의 translation.z, Odometry의 나머지 위치·속도 성분과 pose/twist covariance를
명시적으로 대입하는 코드는 없다. 외부 메시지 기본값을 별도 설정한 값으로 표현하지 않는다.
`Pinky`의 상태 처리 코드에는 IMU 융합·필터·외부 절대 위치 보정 구현이 없다.
전체 TF tree·중복 발행·시간 일관성·실제 통신은 [미검증]이다.

### 4.5. JointState와 Xacro Joint 이름 비교

[코드 확인] `JOINT_NAME_WHEEL_L/R`는 `left_wheel_joint`, `right_wheel_joint`이고,
`RPM2RAD = 2 * math.pi / 60`이다. 근거: [pinky_bringup/pinky_bringup/bringup.py:27-31](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L27-L31).

```python
joint_msg = JointState()
joint_msg.header.stamp = current_time.to_msg()
joint_msg.name = [JOINT_NAME_WHEEL_L, JOINT_NAME_WHEEL_R]

pos_l_rad = (self.last_encoder_l / PULSE_PER_ROT) * (2 * math.pi)
pos_r_rad = (self.last_encoder_r / PULSE_PER_ROT) * (2 * math.pi)
joint_msg.position = [pos_l_rad, pos_r_rad]
joint_msg.velocity = [rpm_l * RPM2RAD, rpm_r * RPM2RAD]

self.joint_pub.publish(joint_msg)
```

근거: `Pinky._publish_joint_states()`, [pinky_bringup/pinky_bringup/bringup.py:185-195](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L185-L195).

position은 초기 encoder를 뺀 변위가 아니라 **현재 raw encoder의 각도 변환**이다.
velocity는 **Present Velocity에서 얻은 RPM의 각속도 변환**이다.
header.frame_id와 effort는 명시적으로 대입하지 않는다.
오른쪽 position/velocity에는 odometry 차분과 달리 추가 부호 반전이 없다.

| 비교 대상 | 왼쪽 | 오른쪽 | 코드 근거 |
|---|---|---|---|
| bringup JointState | `left_wheel_joint` | `right_wheel_joint` | [pinky_bringup/pinky_bringup/bringup.py:27-28](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L27-L28); [pinky_bringup/pinky_bringup/bringup.py:188-188](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L188-L188) |
| Xacro, 빈 namespace | `l_wheel_joint` | `r_wheel_joint` | [pinky_description/urdf/pinky.urdf.xacro:54-63](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/urdf/pinky.urdf.xacro#L54-L63) |

Xacro는 `${namespace}${side}_wheel_joint` 템플릿에 `side="l"`, `side="r"`를 사용한다.
따라서 Joint 이름의 정적 불일치는 [코드 확인]이다.
[추론] JointState 소비·모델 표시·wheel TF에 영향 가능성이 있으며,
실제 소비·표시·TF 영향은 [미검증]이다.
오른쪽 부호 차이도 실제 encoder 방향과 모델 axis를 함께 검토해야 하며,
부호 차이만으로 물리적으로 틀렸다고 확정하지 않는다.

### 4.6. 배터리 발행·구독

[코드 확인] `BatteryPublisher.__init__()`는 `Battery()`를 만든 뒤 publisher 두 개와
각각 5초인 별도 timer 두 개를 만든다. 생성자에서 즉시 발행하는 호출은 없다.
근거: [pinky_bringup/pinky_bringup/battery_publisher.py:6-26](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L6-L26).

```text
BatteryPublisher.__init__()
  → Node("battery_publihser")
  → 외부 pinkylib.Battery()
  → battery/percent 및 battery/voltage publisher
  → percentage timer / voltage timer

percentage_callback()
  → Battery.battery_percentage()
  → float(...)
  → Float32 battery/percent 발행

voltage_callback()
  → Battery.get_voltage()
  → float(...)
  → Float32 battery/voltage 발행
       → Pinky.battery_voltage_callback()
       → 전압 저장 및 경고 판단
```

근거: [pinky_bringup/pinky_bringup/battery_publisher.py:28-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L28-L36), [pinky_bringup/pinky_bringup/bringup.py:90-95](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L90-L95), [pinky_bringup/pinky_bringup/bringup.py:197-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L197-L205).

`battery_voltage_callback()`는 `self.current_voltage = msg.data` 후 None 여부를 확인하고,
`LOW_BATTERY_THRESHOLD = 6.8` 이하이면 전압을 V로 표시하는 경고를 기록한다.
경고만 기록하며 속도 제한·모터 정지·torque 해제·shutdown 호출은 없다.
근거: [pinky_bringup/pinky_bringup/bringup.py:33-34](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L33-L34), [pinky_bringup/pinky_bringup/bringup.py:197-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L197-L205).

경고 throttle이나 메시지 미수신 timeout/stale 감시는 구현하지 않는다.
None 검사도 수신 callback 안에서만 실행하므로 메시지를 아예 못 받은 상태를
주기적으로 감지하는 검사가 아니다. 배터리 두 값은 별도 호출이므로 동일 시점의
동기화 sample이라고 보장하지 않는다.

외부 `Battery`의 센서 접근·보정·백분율 산식·범위와 읽기 성공은 [미검증]이다.
`battery/voltage(Float32)` 경로를 ADC 패키지의 별도 배터리 발행과 자동 연결된 것으로
취급하지 않는다. 패키지 사이의 실행·데이터 경계는 [전체 파이프라인](../01_Architecture/pinky-bringup-pipeline.md)을 따른다.

## 5. 의존 코드

| 역할 | 정적 연결 | 코드 근거 |
|---|---|---|
| Feedback 취득 | `Pinky`가 `DynamixelDriver.get_feedback()`를 호출하고 외부 SDK 객체로 읽음 | [pinky_bringup/pinky_bringup/bringup.py:15-15](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L15-L15); [pinky_bringup/pinky_bringup/bringup.py:132-136](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L132-L136); [pinky_bringup/pinky_bringup/dynamixel_driver.py:25-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L25-L29); [pinky_bringup/pinky_bringup/dynamixel_driver.py:71-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L71-L100) |
| 메시지·TF | geometry/nav/sensor/std 메시지, tf2_ros 및 tf_transformations 사용 | [pinky_bringup/pinky_bringup/bringup.py:8-13](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L8-L13) |
| 배터리 | 외부 `pinkylib.Battery` import와 메서드 호출 | [pinky_bringup/pinky_bringup/battery_publisher.py:1-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L1-L4); [pinky_bringup/pinky_bringup/battery_publisher.py:10-10](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L10-L10); [pinky_bringup/pinky_bringup/battery_publisher.py:28-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L28-L36) |
| 공통 모델 | Xacro의 wheel Joint 이름과 발행 이름 비교 | [pinky_description/urdf/pinky.urdf.xacro:54-63](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/urdf/pinky.urdf.xacro#L54-L63); [pinky_bringup/pinky_bringup/bringup.py:27-28](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L27-L28) |

package.xml의 선언 의존성과 실제 import/include 차이는
[패키지 문서](../02_Packages/pinky_bringup.md)를 기준으로 한다.
목표 명령의 SDK 전송·초기화·종료는 [모터 제어 분석](pinky_bringup--motor-control.md)에 분리했다.
외부 SDK·Battery·TF 라이브러리의 설치 버전이나 내부 구현을 이 기준 Commit의 코드로 간주하지 않는다.

## 6. 주요 파라미터

`wheel_radius`와 `wheel_separation`은 초기화 때 읽어 저장하고,
상태 계산에서 바퀴 둘레와 `delta_theta`의 분모로 사용한다.
근거: [pinky_bringup/pinky_bringup/bringup.py:43-52](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L43-L52), [pinky_bringup/pinky_bringup/bringup.py:144-148](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L144-L148).

정확한 YAML/Python/Launch 값, 단위 확인 여부, 다른 파라미터와의 관계,
변경 영향 및 동적 재적용 여부는 [파라미터 문서](../03_Parameters/pinky_bringup-parameters.md)를 기준으로 한다.
이 문서 4절의 회전당 pulse·RPM 변환·timer·저전압 상수는 YAML의 ROS 파라미터로 표시하지 않는다.
설정 파일의 값과 실제 런타임 적용값은 구분하며 후자는 [미검증]이다.

## 7. 관련 문서

- [pinky_bringup 패키지](../02_Packages/pinky_bringup.md)
- [실기·시뮬레이터 파이프라인](../01_Architecture/pinky-bringup-pipeline.md)
- [모터 제어 코드 분석](pinky_bringup--motor-control.md)
- [pinky_bringup 파라미터](../03_Parameters/pinky_bringup-parameters.md)
- [전체 패키지 구조](../00_Overview/package-inventory.md)
- [소스코드 분석 기준](../00_Overview/source-baseline.md)
- [문서 색인](../index.md)

## 8. 코드 근거 및 검증 상태

Repository는 `seljb/pinky_pro`, 분석 Commit은
`014a09f289e6988894fbffde2624fdb1e257815b`이다. 본문 원본 링크는 이 전체 SHA로 고정했고,
파일·줄 번호는 해당 Git 객체를 읽어 확인했다. 원격 GitHub 접속·실행 검증은 수행하지 않았다.

| 검토 항목 | 확인 사실·구분 | 남은 검증과 근거 |
|---|---|---|
| Feedback 경계 | [코드 확인] signed 변환은 `> 2**31`, encoder delta wrap 보정 없음 | [추론] 정확한 경계값 해석·경계 통과 delta 영향 검토. 실제 모드·데이터는 [미검증]. [pinky_bringup/pinky_bringup/dynamixel_driver.py:92-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L92-L100); [pinky_bringup/pinky_bringup/bringup.py:138-152](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L138-L152) |
| 적분 순서 | [코드 확인] theta를 먼저 갱신해 x/y에 사용; theta 정규화 없음 | 정확도·장기 누적 영향 [미검증]. [pinky_bringup/pinky_bringup/bringup.py:147-155](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L147-L155) |
| Joint 계약 | [코드 확인] left/right와 Xacro l/r 이름 불일치, 오른쪽 odom/Joint 부호 차이 | [추론] 소비·방향·TF 영향 가능. 실제 모델·장치 동작 [미검증]. [pinky_bringup/pinky_bringup/bringup.py:138-139](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L138-L139); [pinky_bringup/pinky_bringup/bringup.py:185-195](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L185-L195); [pinky_description/urdf/pinky.urdf.xacro:54-63](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_description/urdf/pinky.urdf.xacro#L54-L63) |
| 출력 시간·품질 | [코드 확인] callback 시작 timestamp, 명목 30 Hz; covariance 별도 설정 없음 | 실제 주기·QoS·TF·불확실성·odom 정확성 [미검증]. [pinky_bringup/pinky_bringup/bringup.py:88-88](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L88-L88); [pinky_bringup/pinky_bringup/bringup.py:127-183](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L127-L183) |
| 배터리 sample | [코드 확인] 외부 메서드 별도 호출과 5초 timer 두 개 | 실제 단위 보정·백분율 범위·동기성·읽기 성공 [미검증]. [pinky_bringup/pinky_bringup/battery_publisher.py:10-36](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L10-L36) |
| 배터리 경고 | [코드 확인] 저전압 로그만, 미수신 감시·정지 없음 | 장치 전체의 보호·안전 동작은 [미검증]이며 보호가 없다고 확대하지 않음. [pinky_bringup/pinky_bringup/bringup.py:197-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L197-L205) |
| 배터리 예외·정리 | [코드 확인] callback에 읽기/float 예외 처리 없음, Node 생성은 main의 try 밖. finally는 destroy/shutdown만 호출 | [추론] 생성·callback 오류와 외부 Battery 자원 정리 경로 검토. 실행 결과 [미검증]. [pinky_bringup/pinky_bringup/battery_publisher.py:28-49](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/battery_publisher.py#L28-L49) |
| 런타임 설정·연결 | [코드 확인] 초기 설정을 읽는 위치·정적 Topic/frames 확인 | 실제 파라미터·namespace/remap·전체 ROS 그래프·하드웨어 응답은 [미검증]. [pinky_bringup/pinky_bringup/bringup.py:43-52](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L43-L52); [pinky_bringup/pinky_bringup/bringup.py:84-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L84-L100) |

상태를 읽지 못했을 때 자동 정지시키는 동작과 실제 모터 정지 결과는 별개다.
해당 실패 분기는 경고 후 상태 갱신만 건너뛴다([pinky_bringup/pinky_bringup/bringup.py:132-135](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L132-L135)).
명령 timeout/watchdog·초기화 실패와 종료 정리의 상세 범위는
[모터 제어 분석](pinky_bringup--motor-control.md)의 확인 사실·미검증 항목을 따른다.

이번 작업에서는 원본 소스·ROS2·모터·센서·시뮬레이터·빌드·테스트를 실행하지 않았다.
[실행 확인]으로 분류할 동작 결과는 없다.
