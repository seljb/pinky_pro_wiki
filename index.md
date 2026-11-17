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
