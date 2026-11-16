# LLM Wiki Operation Log

Wiki 생성 및 유지관리 작업을 시간순으로 기록합니다.

## [2026-11-16] setup | Wiki structure

- 작업: Wiki 기본 구조 및 운영 파일 준비
- 대상: pinky_pro_wiki
- 분석 소스: 없음
- 코드 검증: 미수행
- 상태: 초기 구조 구성
- 다음 작업: LLM 분석 규칙 및 문서 템플릿 확정

## [2026-11-17] setup | Document templates and linking rules

- 작업: Wiki 문서 템플릿 5개 생성
- 작업: 문서 이름 및 상대경로 연결 규칙 정의
- 작업: AGENTS.md에 문서 관리 규칙 연결
- 관련 경로: templates/, AGENTS.md
- 소스코드 분석: 미수행
- 상태: STEP 6 구성 완료
- 다음 작업: STEP 7 LLM 분석 환경 구축

## [2026-11-17] setup | Source analysis baseline

- 작업: AGENTS.md와 templates/README.md의 기존 규칙 및 문서 형식 확인
- 작업: 원본 저장소의 Git 상태를 읽기 전용으로 재확인하고 소스코드 분석 기준 문서 작성
- 관련 경로: 00_Overview/source-baseline.md, index.md, log.md
- 원본 소스: seljb/pinky_pro (`/home/ljb/dev_ws/pinky/src/pinky_pro`)
- 기준 브랜치: `feature/nav2-study`
- 기준 Commit: `014a09f289e6988894fbffde2624fdb1e257815b`
- Git 확인: 추적 파일의 staged/unstaged 변경 없음, 미등록 파일 3개
- 미등록 파일: `pinky_navigation/launch/localization_only_launch.xml`, `pinky_navigation/nav2_send_goal_basic.py`, `pinky_navigation/params/amcl_params.yaml`
- 기준 범위: 미등록 파일 3개는 현재 HEAD 트리에 없으며, 기준 SHA에 포함된 코드로 취급하지 않음
- 문서 변경: 신규 기준 문서 생성 및 index.md에 상대경로 링크 등록, 기존 작업 이력 보존
- 검증 범위: 로컬 Git 상태 및 HEAD 트리 확인; 소스코드 동작·빌드·런타임·원격 저장소 상태는 미검증
- 실행: ROS2 및 로봇 제어 미수행, 원본 파일 변경 및 Git add/commit/push 미수행
- 상태: STEP 7-6 분석 기준 문서 작성 완료
- 다음 작업: 이 기준 Commit과 작업 트리 상태를 재확인한 뒤 패키지 및 파이프라인 분석
