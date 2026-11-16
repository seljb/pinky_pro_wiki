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

## [2026-11-17] docs | Package inventory (STEP 8-3)

- 작업: AGENTS.md, templates/README.md, 소스코드 분석 기준 및 기존 Wiki 문서 확인
- 작업: STEP 8-1 조사 결과를 검토하고 전체 ROS2 패키지 구조 문서 작성
- 관련 문서: [전체 ROS2 패키지 구조](00_Overview/package-inventory.md)
- 변경 경로: 00_Overview/package-inventory.md, index.md, log.md, README.md
- 원본 소스: seljb/pinky_pro (`/home/ljb/dev_ws/pinky/src/pinky_pro`)
- 기준 브랜치: `feature/nav2-study`
- 기준 Commit: `014a09f289e6988894fbffde2624fdb1e257815b`
- 작업 시작 시 Git 확인: 기준 HEAD 일치, 추적 파일 변경 없음, 기존 미등록 파일 3개 유지
- 문서 범위: ROS2 패키지 11개 전체 목록, 주요 파일·역할·빌드 유형, Launch/YAML/URDF 구성
- 관계 구분: package.xml의 내부 선언 의존 관계 4개와 코드·Launch의 정적 참조를 별도 기록
- 분석 상태: [코드 확인]과 상세 분석 순서의 [추론]을 구분하고, 빌드·실행·런타임은 [미검증]으로 기록
- 미등록 파일: `pinky_navigation/launch/localization_only_launch.xml`, `pinky_navigation/nav2_send_goal_basic.py`, `pinky_navigation/params/amcl_params.yaml`
- 제외 원칙: 미등록 파일 3개는 HEAD 분석과 기준 SHA의 코드 근거에서 제외
- 문서 연결: index.md에 상대경로 링크 등록, README.md의 구조 조사·문서화 완료 및 상세 분석 대기 상태 반영
- 최종 확인 결과: 패키지 11/11, 내부 선언 의존 4/4 및 11개 manifest 선언표 대조 통과; Launch 20/20, YAML 8/8, URDF/Xacro 4/4 포함 확인
- 링크 검사: 상대경로 Wiki 링크 21개 존재 확인, 기준 Commit 소스 링크 129개의 HEAD 파일 존재 및 줄 범위 확인; 원격 URL 접속 검증은 미수행
- 변경 검사: git diff --check 종료 코드 0 및 출력 없음; 신규 파일을 포함한 공백 검사 통과, 변경 범위는 지정된 4개 파일
- 원본 보존 확인: 작업 전후 브랜치·HEAD·Git 변경 상태 동일; 추적 파일 변경 없음, 미등록 3개 유지; source-baseline.md와 작업 규칙 파일의 내용 불변 확인
- 실행 범위: 원본은 읽기 전용으로 취급; ROS2·로봇 제어·빌드·테스트·패키지 설치 미수행, Git add/commit/push/checkout 미수행
- 다음 작업: 변경 문서 검토 후 권장 순서에 따른 개별 패키지 상세 분석
