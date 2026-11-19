# Pinky Pro Learning Wiki

Start with the [learning roadmap](00_Overview/learning-roadmap.md), then follow the [system overview](01_Architecture/system-overview.md). The English lessons explain the implementation with diagrams, worked examples, questions and answers. Source links provide evidence; reading the raw code is optional.

## Open in Obsidian

Choose **Open folder as vault** and select `/home/ljb/dev_ws/pinky_pro_wiki`. Open this `README.md`, then the roadmap. Use the [full index](index.md) to jump between lessons and packages. Mermaid diagrams are embedded in Markdown fences for reading view.

## Read from another PC

Open the [GitHub repository](https://github.com/seljb/pinky_pro_wiki) in a browser and follow the [learning roadmap](00_Overview/learning-roadmap.md) or [full index](index.md). The Wiki is stored as Markdown files in this repository.

For Obsidian, first download a local copy from a terminal in your chosen parent folder:

```bash
git clone https://github.com/seljb/pinky_pro_wiki.git
```

In Obsidian, choose **Open folder as vault**, select the cloned `pinky_pro_wiki` folder, and open `README.md` or `00_Overview/learning-roadmap.md`. The source-code checkout is optional for reading the lessons.

To receive later published updates, run this from the same parent folder:

```bash
git -C pinky_pro_wiki pull --ff-only
```

## Current learning coverage — 2026-11-19

The English lessons already present locally have been preserved and extended. Begin with the roadmap's **first complete study unit**: follow one motion request through command, feedback, frames and launch selection. The latest review adds a full TF ownership chain, precise node identities, hardware/Gazebo/MuJoCo launch comparisons, and simulator command examples. See the [current local review](06_Change_History/2026-11-19-learning-wiki-review.md) for the exact changes and checks performed in this session.

All 11 repository packages are covered in the learning route: the English [motion and feedback lesson](04_Features/motion-and-feedback.md) explains bringup, and ten new package pages cover description, both simulators, navigation, sensors, interfaces and expression. The existing detailed bringup/package inventory pages remain available in their original Korean form as optional references.

| Read next | What you will understand |
|---|---|
| [Motion and feedback](04_Features/motion-and-feedback.md) | A requested velocity, wheel RPM, encoders, odometry and TF |
| [Robot description and TF](01_Architecture/robot-description-and-tf.md) | Body geometry, joints and frame relationships |
| [Simulation](01_Architecture/simulation-pipeline.md) | MuJoCo and Gazebo paths, contracts and differences |
| [Navigation](01_Architecture/navigation-pipeline.md) | SLAM, localization, saved maps, planning and control |
| [Sensors and expression](01_Architecture/sensors-and-expression.md) | Peripheral nodes and service contracts |
| [Interface atlas](04_Features/ros2-interface-atlas.md) | Topics, services, actions and TF across the system |

Learning prose, diagrams and tables use English. Educational code comments and working notes use Korean. Each lesson distinguishes source-confirmed facts, interpretation and unverified runtime behavior.

## Evidence and maintenance

Source: `seljb/pinky_pro`, commit `014a09f289e6988894fbffde2624fdb1e257815b`, read from `/home/ljb/dev_ws/pinky/src/pinky_pro`. Three local untracked navigation files are excluded from commit-based evidence. See the [dated evidence report](06_Change_History/2026-11-18-learning-wiki-evidence.md) for exact files and validation.

This is static source analysis. No ROS nodes, robot commands, simulator, build or repository tests were run. Source configuration values are distinguished from effective runtime values. The source repository is preserved. [Operation log](log.md) records Korean working notes; [agent rules](AGENTS.md) and [templates](templates/README.md) retain the existing maintenance workflow.

---

## Historical setup notes — preserved from 2026-11-17

The original README below is retained verbatim. Its pending-work checkboxes describe the earlier state; the dated section above is the current learning status.

# Pinky Pro LLM Wiki

LLM-powered documentation and code analysis for Pinky Pro ROS2.

## 1. 프로젝트 목적

Pinky Pro의 ROS2 소스코드를 LLM이 분석하여
사용자가 시스템 구조를 이해하고,
원하는 기능을 구현하기 위한 관련 코드와
파라미터를 찾을 수 있도록 지원한다.

### 핵심 기능

- ROS2 패키지와 시스템 파이프라인 분석
- Node, Topic, Service, Action, TF 관계 파악
- 파일, 클래스, 함수 및 의존성 분석
- 주요 파라미터의 역할과 현재 설정값 탐색
- 자연어 요구사항과 관련 코드 연결
- 코드 변경 이력 및 실험 결과 관리
- Wiki 지식 축적과 지속적인 업데이트

## 2. 구축 단계

| 버전 | 목표 | 상태 |
|---|---|---|
| 준비 | GitHub 및 개발 환경 설정 | 완료 |
| V1 | 요구사항 기반 코드·파라미터 탐색 | 진행 중 |
| V2 | 파라미터 조정 및 실험 지원 | 예정 |
| V3 | 구현 방법 조사 및 비교 | 예정 |
| V4 | 코드 수정 설계 및 변경 이력 | 예정 |
| V5 | Wiki 자동 업데이트 | 예정 |

## 3. V1 주요 요구사항

- F-01: 자연어 요구사항 분석
- F-02: 관련 ROS2 파이프라인 탐색
- F-03: 관련 소스코드 탐색
- F-04: 주요 파라미터 및 현재 값 분석
- F-05: 시스템 구조 학습 지원
- F-06: 코드 근거 및 검증 상태 기록

V1에서는 자동 코드 수정이나 최적값 추천보다
정확한 코드 탐색과 시스템 이해에 집중한다.

## 4. 저장소

- Source: https://github.com/seljb/pinky_pro
- Wiki: https://github.com/seljb/pinky_pro_wiki
- Upstream: https://github.com/pinklab-art/pinky_pro

## 5. Wiki 문서

- [Wiki Index](index.md)
- [Agent Instructions](AGENTS.md)
- [Operation Log](log.md)
- [소스코드 분석 기준](00_Overview/source-baseline.md)
- [전체 ROS2 패키지 구조](00_Overview/package-inventory.md)
- [pinky_bringup 상세 분석](02_Packages/pinky_bringup.md)

주요 분석 문서는 앞으로 index.md에서 관리한다.

## 6. 현재 진행 상황

### 완료

- [x] Pinky Pro 개인 Fork 생성
- [x] 기존 ROS2 작업 공간 확인
- [x] Git origin / upstream 설정
- [x] feature/nav2-study 브랜치 생성
- [x] Wiki GitHub 저장소 생성 및 Clone
- [x] Wiki 기본 디렉터리 생성
- [x] index.md 생성
- [x] log.md 생성
- [x] AGENTS.md 초안 생성
- [x] 소스코드 접근 경로 확인 및 분석 기준 Git Commit SHA 문서화
- [x] 전체 ROS2 패키지 구조 조사 및 Wiki 문서화 (11개, STEP 8-3)
- [x] pinky_bringup 상세 분석 결과를 문서 유형별 5개 문서로 작성 (STEP 8-4-3)

### 진행 예정

- [x] Wiki 문서 템플릿 설계
- [x] 문서 간 연결 규칙 정의
- [ ] LLM 분석 환경 설정
- [ ] Pinky Pro 전체 패키지 분석 (개별 패키지 상세 분석)
- [ ] ROS2 파이프라인 및 코드 분석
- [ ] 파라미터 카탈로그 구축
- [ ] V1 질의응답 시스템 구축
- [ ] Wiki 정확성 및 일관성 검증

## 7. 다음 작업

STEP 7: LLM 소스코드 분석 환경 구축

- [x] Pinky Pro 소스코드 접근 경로 확인
- [x] 분석 기준 Git Commit SHA 기록
- [ ] LLM Agent 실행 환경 확인
- [ ] Wiki 작성 및 갱신 워크플로 설정

STEP 8: 전체 패키지 구조 문서화 및 상세 분석 준비

- [x] 전체 ROS2 패키지 11개 구조 조사 (STEP 8-1)
- [x] 선언 의존성과 코드·Launch 정적 참조를 구분하여 Wiki 문서화 (STEP 8-3)
- [x] pinky_bringup Package·Architecture·Code Analysis 2개·Parameter 문서 작성 (STEP 8-4-3)
- [ ] 권장 순서에 따라 나머지 10개 패키지 상세 분석 문서 작성
- [ ] 패키지 사이의 데이터·실행 연결과 파라미터 상세 분석

현재 완료 범위는 전체 패키지의 정적 구조 조사와 pinky_bringup의 상세 분석 문서화이다.
빌드·실행·런타임 동작은 미검증이며, 전체 패키지 상세 분석 완료를 의미하지 않는다.

## 8. 관리 원칙

- README.md: 프로젝트 목표와 진행 상황
- index.md: Wiki 문서 색인
- log.md: 분석 및 작업 이력
- AGENTS.md: LLM의 작업 규칙

작업이 완료될 때마다 README의 진행 상황과
log.md의 작업 기록을 업데이트한다.
