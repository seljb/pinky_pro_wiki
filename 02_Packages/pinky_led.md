---
type: package
title: "pinky_led: pixel and brightness services"
source_repo: seljb/pinky_pro
source_commit: "014a09f289e6988894fbffde2624fdb1e257815b"
source_paths:
  - pinky_led/pinky_led/led_server.py
  - pinky_led/setup.py
  - pinky_led/package.xml
  - pinky_interfaces/srv/SetLed.srv
  - pinky_interfaces/srv/SetBrightness.srv
verification: code-confirmed; runtime-unverified
---

# pinky_led — a ROS service wrapper around pinkylib.LED

## 1. Package overview

`LedServiceServer` adapts service requests to an external `pinkylib.LED` object. Its own responsibility is command routing and response/error handling; hardware details and value limits are delegated to that external library, whose implementation is not in this repository. **[Code confirmed]** [pinky_led/pinky_led/led_server.py:1-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L1-L16) [pinky_led/pinky_led/led_server.py:18-79](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L18-L79)

## 2. Package structure

| File | Role |
|---|---|
| `pinky_led/led_server.py` | Service node, callbacks and shutdown path |
| `setup.py` | Installs console entrypoint `led_server` |
| `package.xml` | `ament_python` metadata; no direct runtime dependency declarations |

The setup file's `install_requires` lists only `setuptools`. Imports of `rclpy`, `pinkylib` and `pinky_interfaces` are actual runtime requirements in the source, distinct from what its manifest declares. [pinky_led/setup.py:14-24](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/setup.py#L14-L24) [pinky_led/package.xml:10-18](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/package.xml#L10-L18) [pinky_led/pinky_led/led_server.py:1-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L1-L16)

## 3. Executable nodes

| Node | Entrypoint | Role | Evidence |
|---|---|---|---|
| `led_service_server` | `led_server=pinky_led.led_server:main` | Creates one LED object and two services | [pinky_led/pinky_led/led_server.py:1-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L1-L16) [pinky_led/setup.py:14-24](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/setup.py#L14-L24) |

The object is constructed before the `try/finally` around spinning; initialization exceptions are not handled by that later cleanup block. Normal shutdown clears LEDs, destroys the node and shuts down ROS. [pinky_led/pinky_led/led_server.py:82-95](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L82-L95)

## 4. Communication interfaces

| Relative service name | Type | Callback |
|---|---|---|
| `set_led` | `pinky_interfaces/srv/SetLed` | `set_led_callback` |
| `set_brightness` | `pinky_interfaces/srv/SetBrightness` | `set_brightness_callback` |

No publishers, subscribers or action servers are created. Both services return `success` and `message`; their schemas do not enforce the external LED library's supported values. [pinky_led/pinky_led/led_server.py:1-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L1-L16) [pinky_interfaces/srv/SetLed.srv:1-11](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_interfaces/srv/SetLed.srv#L1-L11); [pinky_interfaces/srv/SetBrightness.srv:1-4](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_interfaces/srv/SetBrightness.srv#L1-L4)

| `set_led` command | Wrapper behavior | What is not established here |
|---|---|---|
| `set_pixel` | Lowercases command, loops through `pixels`, calls `set_pixel(pixel,(r,g,b))`, then `show()` once | Valid pixel count/order and color bounds |
| `fill` | Calls `fill((r,g,b))` | Whether the library internally flushes to hardware |
| `clear` | Calls `clear()` | Physical output confirmation |
| Anything else | Returns `success=false` and available-command message | No implicit fallback |

The callback catches `IndexError` and other exceptions and returns failure text. In a multi-pixel request, earlier library calls may already have happened before a later index fails; the wrapper has no rollback. **[Inference]** Service failure is not a guarantee of zero side effects. [pinky_led/pinky_led/led_server.py:18-61](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L18-L61)

`set_brightness` passes the integer directly to `LED.set_brightness` and marks success if that call returns normally. The wrapper defines neither a documented numeric range nor a clamping rule. Do not import a guessed range from another LED API. [pinky_led/pinky_led/led_server.py:63-79](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L63-L79)

```python
# 학습용 의사코드: 실제 장치를 호출하지 않는다.
command = request.command.lower()
color = (request.r, request.g, request.b)
# set_pixel은 각 픽셀 값을 모은 뒤 show()를 한 번 호출한다.
# fill/clear의 실제 출력 처리는 외부 pinkylib 구현에 달려 있다.
```

## 5. Dependencies

[Service schemas](pinky_interfaces.md) define the data contract. `pinkylib.LED` owns the hardware operations, while `rclpy` owns service execution. This repository does not establish whether `pinky_led` and the C++ lamp controller address the same physical device; their APIs and drivers differ. [pinky_led/pinky_led/led_server.py:1-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L1-L16) [pinky_lamp_control/src/main_node.cpp:12-27](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_lamp_control/src/main_node.cpp#L12-L27)

## 6. Related pipelines

- [Sensors and expression](../01_Architecture/sensors-and-expression.md)
- [Interface atlas](../04_Features/ros2-interface-atlas.md)
- [Lamp controller](pinky_lamp_control.md)

The tracked real-robot launch does not start `led_server`. A successful package install is not evidence that either service is running. [pinky_bringup/launch/bringup_robot.launch.xml:3-29](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_bringup/launch/bringup_robot.launch.xml#L3-L29)

## 7. Related code and parameters

There are no `declare_parameter` calls in `led_server.py`. Brightness is a per-request service field, and color/pixel values come from `SetLed`; they are not parameters loaded from YAML by this package. See the callback table above for behavior rather than searching for a nonexistent LED parameter file. [pinky_led/pinky_led/led_server.py:1-16](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L1-L16) [pinky_led/pinky_led/led_server.py:18-79](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_led/pinky_led/led_server.py#L18-L79)

## 8. Verification and open questions

**[Code confirmed]** Entrypoint, service names, method calls and exception paths were inspected at the metadata SHA. **[Unverified]** External library version, LED count, accepted brightness range, hardware initialization, namespace/remapping and actual displayed color were not tested.

**Check yourself.** Why does `FILL` work as a command string? **Answer:** The callback lowercases the string before branch selection. Does a successful `fill` response prove a later `show()` call in this wrapper? **Answer:** No; only the `set_pixel` branch explicitly calls `show()` here.

> 작업 메모: pinkylib의 범위 검사와 자동 show 동작은 이 저장소에서 확인할 수 없다. 추측값을 API 계약으로 쓰지 않는다.

[Wiki index](../index.md)
