---
type: code_analysis
title: "pinky_bringup 모터 제어"
source_repo: seljb/pinky_pro
source_commit: "014a09f289e6988894fbffde2624fdb1e257815b"
source_paths:
  - pinky_bringup/pinky_bringup/bringup.py
  - pinky_bringup/pinky_bringup/dynamixel_driver.py
verification: 코드 확인
---

# pinky_bringup 모터 제어

## 1. 분석 대상

STEP 8-4-1·8-4-2에서 분석한 모터 명령·초기화·종료 경로를 구조화했다.
원본 코드는 기준 SHA의 Git 객체로 읽었으며 노드·모터·센서 초기화나 실행은 하지 않았다.

| 항목 | 대상 및 근거 |
|---|---|
| Repository / Commit | `seljb/pinky_pro` / `014a09f289e6988894fbffde2624fdb1e257815b` |
| 명령 수신·변환 | `Pinky.twist_callback()` — [pinky_bringup/pinky_bringup/bringup.py:104-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L125) |
| 노드 초기화·종료 진입 | `Pinky.__init__()` / `main()` — [pinky_bringup/pinky_bringup/bringup.py:36-101](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L36-L101), [pinky_bringup/pinky_bringup/bringup.py:208-219](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L208-L219) |
| 장치 래퍼 | `DynamixelDriver` — [pinky_bringup/pinky_bringup/dynamixel_driver.py:4-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L4-L100) |
| 관련 Node / Topic | 코드 생성 이름 `pinky_bringup` / 상대 이름 `cmd_vel` — [pinky_bringup/pinky_bringup/bringup.py:17-17](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L17-L17), [pinky_bringup/pinky_bringup/bringup.py:38-38](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L38-L38), [pinky_bringup/pinky_bringup/bringup.py:86-86](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L86-L86) |

아래 소스 경로와 줄 번호는 모두 위 전체 Commit SHA 기준이다.
미등록 파일 3개는 [소스코드 분석 기준](../00_Overview/source-baseline.md)의 경계에 따라
분석에서 제외했다. 이 문서의 `verification: 코드 확인`은 정적 구현 확인이며 실제 동작 성공을 뜻하지 않는다.

## 2. 코드의 목적

[코드 확인] `Pinky`는 `Twist` 명령을 좌우 바퀴 RPM으로 변환하고,
`DynamixelDriver`는 그 RPM을 원시 정수·패킷 데이터로 변환해 SDK에 전달한다.
초기화에서는 포트·모터 설정과 초기 0 RPM을 처리하고 종료에서는 정지·torque/LED 해제·포트 닫기를 시도한다.
근거: [pinky_bringup/pinky_bringup/bringup.py:53-80](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L53-L80), [pinky_bringup/pinky_bringup/bringup.py:104-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L125), [pinky_bringup/pinky_bringup/dynamixel_driver.py:31-69](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L31-L69).

실기와 simulator의 실행 구성은 [파이프라인 문서](../01_Architecture/pinky-bringup-pipeline.md),
feedback·Odometry·TF·JointState 계산은 [상태 발행 문서](pinky_bringup--state-publishing.md)가 기준이다.

## 3. 입력 및 출력

[코드 확인] 이 표는 코드에 생성·호출된 인터페이스다. 최종 namespace/remapping과 실제 통신은 [미검증]이다.

| 구분 | 내용 | 클래스·함수 및 근거 |
|---|---|---|
| ROS 입력 | 상대 Topic `cmd_vel`, `geometry_msgs/msg/Twist`, QoS 인수 `10` | `Pinky.__init__()` / `twist_callback()` — [pinky_bringup/pinky_bringup/bringup.py:8-8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L8-L8), [pinky_bringup/pinky_bringup/bringup.py:17-17](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L17-L17), [pinky_bringup/pinky_bringup/bringup.py:86-86](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L86-L86), [pinky_bringup/pinky_bringup/bringup.py:104-106](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L106) |
| 사용 필드 | `msg.linear.x`, `msg.angular.z`만 읽음 | `Pinky.twist_callback()` — [pinky_bringup/pinky_bringup/bringup.py:104-106](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L106) |
| driver 입력 | 좌·우 RPM `rpm_l`, `rpm_r` | `DynamixelDriver.set_double_rpm()` — [pinky_bringup/pinky_bringup/dynamixel_driver.py:59-64](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L59-L64) |
| driver 출력 | SDK `GroupSyncWrite`에 ID별 4바이트 등록 후 전송. `addParam` 실패 또는 `txPacket()!=COMM_SUCCESS`이면 `False` | `DynamixelDriver.set_double_rpm()` — [pinky_bringup/pinky_bringup/dynamixel_driver.py:59-69](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L59-L69) |
| 장치 대상 | 포트·baud·모터 ID를 `Pinky`의 상수에서 전달 | `Pinky.__init__()` — [pinky_bringup/pinky_bringup/bringup.py:23-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L23-L25), [pinky_bringup/pinky_bringup/bringup.py:53-53](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L53-L53) |

`TwistStamped` 처리와 다른 `Twist` 성분을 읽는 코드는 이 callback에 없다.
SDK의 통신 반환 성공은 모터의 실제 명령 수신·회전 성공을 확인한 결과가 아니다.

## 4. 실행 흐름

### 4.1. 초기화 호출 순서

[코드 확인] 다음은 생성자와 driver 메서드의 순차 호출이다.
전체 launch의 프로세스 준비 완료 순서와는 구분한다.

```text
main()
  → rclpy.init()
  → Pinky.__init__()
      → Node('pinky_bringup'), is_initialized=False
      → wheel_radius / wheel_separation 선언·읽기
      → circumference 계산
      → DynamixelDriver(port, baud, IDs)
          → PortHandler
          → PacketHandler(protocol=2.0)
          → GroupSyncWrite(address=104, length=4)
          → GroupBulkRead
      → driver.begin()
          → openPort()
          → setBaudRate()
      → driver.initialize_motors(profile_accel=200)
          → 각 ID별 reboot() 시도 → sleep(0.5)
          → operating mode 주소11에 1
          → profile acceleration 주소108에 200
          → torque enable 주소64에 1
          → LED 주소65에 1
      → sleep(1.0)
      → set_double_rpm(0, 0)
      → get_feedback()로 초기 encoder 취득
      → pub/sub/TF/timer 생성
      → pose와 last_time 초기화, is_initialized=True
  → rclpy.spin(node)
```

근거: `Pinky.__init__()` / `main()` — [pinky_bringup/pinky_bringup/bringup.py:36-101](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L36-L101), [pinky_bringup/pinky_bringup/bringup.py:208-219](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L208-L219);
`DynamixelDriver.__init__()` / `begin()` / `initialize_motors()` —
[pinky_bringup/pinky_bringup/dynamixel_driver.py:5-34](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L5-L34), [pinky_bringup/pinky_bringup/dynamixel_driver.py:45-57](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L45-L57).

`initialize_motors()`는 각 ID의 reboot 블록에서 `Exception`을 잡아 경고하고 계속 진행한다.
reboot 반환값 자체는 검사하지 않는다. 이후 register write는 반환값의 첫 항목 `[0]`만
`COMM_SUCCESS`와 비교하며 나머지 반환정보는 검사하지 않는다.
근거: [pinky_bringup/pinky_bringup/dynamixel_driver.py:45-57](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L45-L57).

`begin()` 실패는 생성자에서 로그 후 반환한다. 모터 초기화·초기 0 RPM·초기 encoder 취득 실패 경로는
`terminate()` 후 반환한다. 근거: [pinky_bringup/pinky_bringup/bringup.py:55-80](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L55-L80).
초기 feedback에서 왼쪽 encoder의 `None` 여부를 검사한다. 현재 driver의 명시적 실패 반환은 네 항목 모두 `None`이다.
근거: [pinky_bringup/pinky_bringup/bringup.py:75-80](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L75-L80), [pinky_bringup/pinky_bringup/dynamixel_driver.py:78-84](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L78-L84).

### 4.2. cmd_vel에서 바퀴 RPM까지의 실제 수식

[코드 확인] `Pinky.twist_callback()`의 수식과 계산 순서를 그대로 보존했다.
근거: [pinky_bringup/pinky_bringup/bringup.py:104-122](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L122).

```python
linear_x = msg.linear.x
angular_z = msg.angular.z

v_l = linear_x - (angular_z * self.wheel_separation / 2.0)
v_r = linear_x + (angular_z * self.wheel_separation / 2.0)

wheel_rads_l = v_l / self.wheel_radius
wheel_rads_r = v_r / self.wheel_radius

rpm_l = wheel_rads_l * 60.0 / (2 * math.pi)
rpm_r = -wheel_rads_r * 60.0 / (2 * math.pi)

max_val = max(abs(rpm_l), abs(rpm_r))
MAX_RPM = 100.0
if max_val > MAX_RPM:
    scale = MAX_RPM / max_val
    rpm_l *= scale
    rpm_r *= scale
```

| 단계 | 코드에서 확인한 의미 | 근거 |
|---|---|---|
| 좌우 바퀴 선속도 | 회전에 따른 `angular_z * wheel_separation / 2.0`을 왼쪽은 빼고 오른쪽은 더함 | [pinky_bringup/pinky_bringup/bringup.py:108-109](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L108-L109) |
| 바퀴 각속도 | 좌우 선속도를 `wheel_radius`로 나눔 | [pinky_bringup/pinky_bringup/bringup.py:111-112](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L111-L112) |
| RPM 변환 | `60/(2π)`를 곱하며 오른쪽만 추가 음수 부호 | [pinky_bringup/pinky_bringup/bringup.py:114-115](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L114-L115) |
| 제한 판단 | 좌우 RPM의 최대 절댓값과 `100.0` 비교 | [pinky_bringup/pinky_bringup/bringup.py:117-119](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L117-L119) |
| 제한 적용 | 최대값이 100 초과이면 두 RPM에 동일한 `100/max_val`을 곱함 | [pinky_bringup/pinky_bringup/bringup.py:120-122](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L120-L122) |
| 전송 실패 | driver가 `False`이면 warning. 이 분기에 재시도·자동 정지 호출 없음 | [pinky_bringup/pinky_bringup/bringup.py:124-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L124-L125) |

양쪽을 독립적으로 clip하는 방식이 아니라 RPM 비율을 보존해 함께 축소한다.
이 제한은 `twist_callback()` 안에 있고 `set_double_rpm()` 안에는 없다.
따라서 driver 메서드를 다른 경로에서 호출할 때 같은 제한이 자동 적용된다고 해석하지 않는다.

길이 단위는 YAML·선언 코드에 직접 명시되지 않았다.
`m`·`m/s`·`rad/s`라는 통상 단위 해석은 소스의 명시적 단위 선언·실측과 구분한다.
`60/(2π)`의 각속도→RPM 변환 연산은 확인했지만 실제 기구 치수·방향·물리 보정은 [미검증]이다.

### 4.3. RPM에서 Dynamixel 원시 명령까지

[코드 확인] 호출 관계는 다음과 같다.

```text
cmd_vel / Twist
  → Pinky.twist_callback()
      → 선속도·각속도 → 좌우 RPM
      → 오른쪽 부호 반전
      → 100 RPM 동일비율 제한
      → DynamixelDriver.set_double_rpm()
          → clearParam()
          → int(RPM * RPM_TO_VALUE_SCALE)
          → SDK 매크로로 4바이트 구성
          → addParam(ID, bytes)
          → txPacket() == COMM_SUCCESS
```

근거: [pinky_bringup/pinky_bringup/bringup.py:104-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L125), [pinky_bringup/pinky_bringup/dynamixel_driver.py:23-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L23-L29), [pinky_bringup/pinky_bringup/dynamixel_driver.py:59-69](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L59-L69).

`DynamixelDriver.set_double_rpm()`의 실제 변환·전송 코드는 다음과 같다.
`RPM_TO_VALUE_SCALE = 1 / 0.229`는 생성자 상수다.

```python
velocities = [rpm_l, rpm_r]
self.groupSyncWrite.clearParam()

for i, dxl_id in enumerate(self.DXL_IDS):
    dxl_vel = int(velocities[i] * self.RPM_TO_VALUE_SCALE)
    param = [DXL_LOBYTE(DXL_LOWORD(dxl_vel)), DXL_HIBYTE(DXL_LOWORD(dxl_vel)),
             DXL_LOBYTE(DXL_HIWORD(dxl_vel)), DXL_HIBYTE(DXL_HIWORD(dxl_vel))]
    if not self.groupSyncWrite.addParam(dxl_id, param): return False

return self.groupSyncWrite.txPacket() == COMM_SUCCESS
```

근거: [pinky_bringup/pinky_bringup/dynamixel_driver.py:23-23](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L23-L23), [pinky_bringup/pinky_bringup/dynamixel_driver.py:59-69](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L59-L69).
`int()` 변환이며 반올림 함수 사용이 아니다. SDK 매크로의 실제 음수 처리·라이브러리 버전과
모터 control table의 물리적 호환성은 [미검증]이다.

### 4.4. 종료 경로

[코드 확인] `main()`의 `finally`는 `node.driver.terminate()`,
`node.destroy_node()`, `rclpy.shutdown()`을 차례로 호출한다.
근거: [pinky_bringup/pinky_bringup/bringup.py:208-219](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L208-L219).

```text
DynamixelDriver.terminate()
  → set_double_rpm(0, 0)
  → sleep(0.1)
  → 각 ID에 torque enable 주소64 = 0
  → 각 ID에 LED 주소65 = 0
  → sleep(0.1)
  → closePort()
```

근거: [pinky_bringup/pinky_bringup/dynamixel_driver.py:36-43](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L36-L43).
이 메서드는 0 RPM 쓰기와 torque/LED 쓰기의 반환값을 검사하지 않는다.
종료 명령이 실제 장치에 적용되었는지는 [미검증]이다.

## 5. 의존 코드

| 구분 | 연결 | 실제 근거 |
|---|---|---|
| 로컬 코드 | `Pinky`가 `.dynamixel_driver.DynamixelDriver`를 import하고 호출 | [pinky_bringup/pinky_bringup/bringup.py:15-15](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L15-L15), [pinky_bringup/pinky_bringup/bringup.py:53-80](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L53-L80), [pinky_bringup/pinky_bringup/bringup.py:124-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L124-L125) |
| 외부 ROS | `rclpy.Node`와 `geometry_msgs.msg.Twist`를 사용 | [pinky_bringup/pinky_bringup/bringup.py:3-8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L3-L8), [pinky_bringup/pinky_bringup/bringup.py:86-86](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L86-L86) |
| 외부 SDK | `dynamixel_sdk`에서 핸들러·그룹 I/O·매크로·`COMM_SUCCESS` 사용 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:2-2](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L2-L2), [pinky_bringup/pinky_bringup/dynamixel_driver.py:25-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L25-L29), [pinky_bringup/pinky_bringup/dynamixel_driver.py:53-69](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L53-L69) |
| 표준 라이브러리 | `math`로 수식, `time`으로 초기화·종료 대기 | [pinky_bringup/pinky_bringup/bringup.py:5-6](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L5-L6), [pinky_bringup/pinky_bringup/dynamixel_driver.py:1-1](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L1-L1) |
| feedback 경계 | `get_feedback()`의 RPM/encoder 반환값을 초기화와 상태 발행에서 사용 | [pinky_bringup/pinky_bringup/bringup.py:75-80](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L75-L80), [pinky_bringup/pinky_bringup/bringup.py:132-136](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L132-L136), [pinky_bringup/pinky_bringup/dynamixel_driver.py:71-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L71-L100) |

manifest 선언과 실제 import/include의 차이는 [패키지 문서](../02_Packages/pinky_bringup.md)의 의존 관계를 따른다.
feedback 원시값 해석·Odometry 적분·JointState의 오른쪽 부호 비교는
[상태 발행 문서](pinky_bringup--state-publishing.md)에 모았다.
외부 SDK·모터 firmware 내부의 제어·timeout·보호 동작은 이번 분석 범위에서 확인하지 않았다.

## 6. 주요 파라미터

YAML의 두 ROS 파라미터 설정값·Python/Launch 기본값·물리 단위 확인 여부·변경 영향·동적 재적용 여부는
[파라미터 문서](../03_Parameters/pinky_bringup-parameters.md)를 기준으로 한다.

| ROS 파라미터 | 이 문서의 구동 계산과 연결 | 읽기·사용 근거 |
|---|---|---|
| `wheel_radius` | 좌우 선속도를 바퀴 각속도로 바꾸는 분모 | `Pinky.__init__()` / `twist_callback()` — [pinky_bringup/pinky_bringup/bringup.py:43-47](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L43-L47), [pinky_bringup/pinky_bringup/bringup.py:111-112](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L111-L112) |
| `wheel_separation` | 각속도의 좌우 선속도 분배에 사용하는 바퀴 간 거리 | `Pinky.__init__()` / `twist_callback()` — [pinky_bringup/pinky_bringup/bringup.py:43-47](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L43-L47), [pinky_bringup/pinky_bringup/bringup.py:108-109](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L108-L109) |

다음은 이 코드 경로의 하드코딩 상수·Python 기본 인수이며 **YAML의 ROS 파라미터가 아니다**.

| 설정 | 코드 값 | 용도·근거 |
|---|---|---|
| `SERIAL_PORT_NAME` | `/dev/ttyAMA4` | `Pinky.__init__()`에서 driver로 전달 — [pinky_bringup/pinky_bringup/bringup.py:23-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L23-L25), [pinky_bringup/pinky_bringup/bringup.py:53-53](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L53-L53) |
| `BAUDRATE` | `1000000` | 같은 전달 경로 및 `begin()` — [pinky_bringup/pinky_bringup/bringup.py:24-24](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L24-L24), [pinky_bringup/pinky_bringup/dynamixel_driver.py:31-34](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L31-L34) |
| `DYNAMIXEL_IDS` | `[1, 2]` | 주석상 왼쪽·오른쪽 순서 — [pinky_bringup/pinky_bringup/bringup.py:25-25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L25-L25) |
| `PROTOCOL_VERSION` | `2.0` | `PacketHandler` 생성 — [pinky_bringup/pinky_bringup/dynamixel_driver.py:18-26](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L18-L26) |
| `ADDR_OPERATING_MODE` | `11`, 초기 write 값 `1` | [pinky_bringup/pinky_bringup/dynamixel_driver.py:6-6](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L6-L6), [pinky_bringup/pinky_bringup/dynamixel_driver.py:53-53](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L53-L53) |
| `ADDR_TORQUE_ENABLE` | `64`, 초기 `1` / 종료 `0` | [pinky_bringup/pinky_bringup/dynamixel_driver.py:7-7](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L7-L7), [pinky_bringup/pinky_bringup/dynamixel_driver.py:40-40](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L40-L40), [pinky_bringup/pinky_bringup/dynamixel_driver.py:55-55](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L55-L55) |
| `ADDR_LED_RED` | `65`, 초기 `1` / 종료 `0` | [pinky_bringup/pinky_bringup/dynamixel_driver.py:8-8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L8-L8), [pinky_bringup/pinky_bringup/dynamixel_driver.py:41-41](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L41-L41), [pinky_bringup/pinky_bringup/dynamixel_driver.py:56-56](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L56-L56) |
| `ADDR_GOAL_VELOCITY` / 길이 | `104` / `4`바이트 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:9-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L9-L16), [pinky_bringup/pinky_bringup/dynamixel_driver.py:28-28](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L28-L28) |
| `ADDR_PROFILE_ACCEL` | `108` | [pinky_bringup/pinky_bringup/dynamixel_driver.py:10-10](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L10-L10), [pinky_bringup/pinky_bringup/dynamixel_driver.py:54-54](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L54-L54) |
| `profile_accel` | 함수 기본 인수 `200` | 호출부에서 변경하지 않음. 물리 가속도 단위 미확인 — [pinky_bringup/pinky_bringup/bringup.py:61-61](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L61-L61), [pinky_bringup/pinky_bringup/dynamixel_driver.py:45-54](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L45-L54) |
| `ADDR_PRESENT_VELOCITY` / 길이 | `128` / `4`바이트 | feedback 입력 주소 — [pinky_bringup/pinky_bringup/dynamixel_driver.py:11-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L11-L16) |
| `ADDR_PRESENT_POSITION` / 길이 | `132` / `4`바이트 | feedback 입력 주소 — [pinky_bringup/pinky_bringup/dynamixel_driver.py:12-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L12-L16) |
| `RPM_TO_VALUE_SCALE` | `1 / 0.229` | RPM→원시 정수 변환 — [pinky_bringup/pinky_bringup/dynamixel_driver.py:23-23](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L23-L23), [pinky_bringup/pinky_bringup/dynamixel_driver.py:64-64](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L64-L64) |
| `MAX_RPM` | `100.0` | `twist_callback()` 지역 상수 — [pinky_bringup/pinky_bringup/bringup.py:117-122](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L117-L122) |
| 초기 대기 | `1.0`초 | `Pinky.__init__()` — [pinky_bringup/pinky_bringup/bringup.py:67-67](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L67-L67) |
| reboot 뒤 대기 | 각 ID `0.5`초 | `initialize_motors()` — [pinky_bringup/pinky_bringup/dynamixel_driver.py:48-49](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L48-L49) |
| 종료 대기 | `0.1`초씩 두 번 | `terminate()` — [pinky_bringup/pinky_bringup/dynamixel_driver.py:36-43](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L36-L43) |

Register 주소와 값은 코드 확인이다. 모터 모델·firmware별 호환성 및 `profile_accel` 값의
물리 가속도·실제 제한 효과는 [미검증]이다.
encoder 변환 상수와 상태 타이머 등은 [파라미터 문서](../03_Parameters/pinky_bringup-parameters.md)의 별도 상수 표와
[상태 발행 문서](pinky_bringup--state-publishing.md)를 참조한다.

## 7. 관련 문서

- [pinky_bringup 패키지](../02_Packages/pinky_bringup.md)
- [실기·시뮬레이터 및 데이터 파이프라인](../01_Architecture/pinky-bringup-pipeline.md)
- [상태 발행 코드 분석](pinky_bringup--state-publishing.md)
- [pinky_bringup 파라미터와 하드코딩 설정](../03_Parameters/pinky_bringup-parameters.md)
- [전체 패키지 구조](../00_Overview/package-inventory.md)
- [소스코드 분석 기준](../00_Overview/source-baseline.md)

## 8. 코드 근거 및 검증 상태

Repository는 `seljb/pinky_pro`, Source Commit은
`014a09f289e6988894fbffde2624fdb1e257815b`이며 위 모든 소스 링크는 이 SHA로 고정했다.
기준 Commit의 소스를 읽기만 했고 원본 파일을 수정하거나 코드를 실행하지 않았다.

| 항목 | [코드 확인] 직접 확인한 사실 | [추론] 또는 [미검증] 경계 | 근거 |
|---|---|---|---|
| 명령 상한 | callback의 동일비율 100 RPM 제한 | 실제 장치 회전속도 상한·회전 성공은 미검증 | [pinky_bringup/pinky_bringup/bringup.py:117-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L117-L125), [pinky_bringup/pinky_bringup/dynamixel_driver.py:59-69](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L59-L69) |
| 명령 timeout | 수신시각/watchdog/명령 단절시 강제 0 RPM 구현 없음 | SDK·firmware·장치 전체의 안전 기능이 없다는 뜻은 아님. 외부 보호는 미검증 | [pinky_bringup/pinky_bringup/bringup.py:104-161](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L161), [pinky_bringup/pinky_bringup/dynamixel_driver.py:59-100](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L59-L100) |
| 입력 검사 | NaN/Inf와 r/b의 0·양수 검사 없음 | [추론] 산술·정수 변환 예외 가능성. 실제 실패 재현은 미검증 | [pinky_bringup/pinky_bringup/bringup.py:43-47](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L43-L47), [pinky_bringup/pinky_bringup/bringup.py:108-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L108-L125), [pinky_bringup/pinky_bringup/bringup.py:148-148](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L148-L148), [pinky_bringup/pinky_bringup/dynamixel_driver.py:64-64](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L64-L64) |
| 가속도 처리 | raw `profile_accel=200` write 존재; 별도 소프트웨어 slew 계산 없음 | raw 값의 물리 단위·장치 동작은 미검증 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:45-57](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L45-L57), [pinky_bringup/pinky_bringup/bringup.py:104-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L104-L125) |
| 전송 실패 | 경고 후 반환; 자동 재시도·복구·정지 없음 | 실제 통신 장애의 장치 반응은 미검증 | [pinky_bringup/pinky_bringup/bringup.py:124-125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L124-L125) |
| feedback 실패 | 상태 갱신을 건너뛰며 이 분기에서 정지 명령을 보내지 않음 | 실제 장치 보호·동작은 미검증 | [pinky_bringup/pinky_bringup/bringup.py:132-135](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L132-L135) |
| 초기화 실패 후 spin | 생성자가 조기 return해도 main은 `is_initialized`를 검사하지 않음 | [추론] 미완성 Node가 spin할 가능성. 실제 실행 결과 아님 | [pinky_bringup/pinky_bringup/bringup.py:39-39](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L39-L39), [pinky_bringup/pinky_bringup/bringup.py:55-80](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L55-L80), [pinky_bringup/pinky_bringup/bringup.py:101-101](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L101-L101), [pinky_bringup/pinky_bringup/bringup.py:208-219](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L208-L219) |
| begin 실패 자원 정리 | baud 설정 실패시 `begin()` 자체에 close가 없고 caller는 바로 return | 실제 포트 상태와 정리 결과 미검증 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:31-34](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L31-L34), [pinky_bringup/pinky_bringup/bringup.py:55-58](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L55-L58) |
| cleanup 예외 안전성 | `node=None`에서 시작하며 finally는 무조건 `node.driver` 접근 | [추론] 생성자 예외를 cleanup 예외가 가릴 가능성. terminate 예외시 뒤 정리도 중단될 수 있음 | [pinky_bringup/pinky_bringup/bringup.py:208-219](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L208-L219) |
| 중복 종료 | 일부 생성자 실패 경로와 main finally 모두 terminate 호출 | [추론] 동일 자원을 다시 종료할 수 있음. 실제 영향 미검증 | [pinky_bringup/pinky_bringup/bringup.py:63-63](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L63-L63), [pinky_bringup/pinky_bringup/bringup.py:72-79](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L72-L79), [pinky_bringup/pinky_bringup/bringup.py:217-217](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L217-L217) |
| 종료 결과 | 0 RPM·torque/LED 쓰기 결과 검사 없음 | 실제 정지·torque 해제 결과 미검증 | [pinky_bringup/pinky_bringup/dynamixel_driver.py:36-43](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/dynamixel_driver.py#L36-L43) |
| 저전압 | `<=6.8`에서 경고 로그만; 속도 제한·정지·shutdown 호출 없음 | 하드웨어 전체의 저전압 보호와 구분. 실제 장치 반응 미검증 | [pinky_bringup/pinky_bringup/bringup.py:33-34](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L33-L34), [pinky_bringup/pinky_bringup/bringup.py:197-205](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/pinky_bringup/bringup.py#L197-L205) |

이 문서의 '구현 없음'은 확인한 Python 코드 범위의 사실이다.
모터 자체의 폐루프 제어·timeout·보호, SDK 내부 재시도·예외 동작, 현재 설치 버전과
하드웨어 방향·치수·register 사양은 추가 근거가 필요하다.
빌드·테스트·ROS2·모터·센서·시뮬레이터를 실행하지 않았으므로 실행 성공이나
안전 동작을 `[실행 확인]`으로 기록하지 않는다.
