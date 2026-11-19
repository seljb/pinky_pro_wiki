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

## [2026-11-17] docs | pinky_bringup detailed analysis (STEP 8-4-3)

- 작업: AGENTS.md, 템플릿 규칙·문서 유형별 템플릿, 소스 분석 기준·전체 패키지 구조·색인 및 진행 상황 확인
- 작업: STEP 8-4-1/8-4-2의 기존 분석을 Package·Architecture·Code Analysis·Parameter 문서로 구조화; 필요한 근거만 고정 SHA에서 읽기 전용 재확인
- 원본 소스: seljb/pinky_pro (`/home/ljb/dev_ws/pinky/src/pinky_pro`)
- 기준 브랜치: `feature/nav2-study`
- 기준 Commit: `014a09f289e6988894fbffde2624fdb1e257815b`
- 시작 상태: 원본 HEAD 일치, 추적 파일 변경 없음, 기존 미등록 3개 유지; Wiki 변경 없음
- 생성 문서: [패키지](02_Packages/pinky_bringup.md), [파이프라인](01_Architecture/pinky-bringup-pipeline.md), [모터 제어](05_Code_Analysis/pinky_bringup--motor-control.md), [상태 발행](05_Code_Analysis/pinky_bringup--state-publishing.md), [파라미터](03_Parameters/pinky_bringup-parameters.md)
- 기존 갱신: index.md에 5개 링크·설명 추가, README.md에 pinky_bringup 문서화만 완료 반영; 나머지 10개 패키지 상세 분석은 미완료 유지
- 내용 분리: 개요·인터페이스는 Package, 전체 흐름은 Architecture, 함수·수식은 Code Analysis, 설정값·변경 영향은 Parameter를 기준으로 연결
- 검증 범위: 정적 소스·설정·함수 호출·선언 의존성과 실제 참조; [코드 확인]·[추론]·[미검증] 구분
- 미등록 제외: `pinky_navigation/launch/localization_only_launch.xml`, `pinky_navigation/nav2_send_goal_basic.py`, `pinky_navigation/params/amcl_params.yaml`은 HEAD 코드 근거로 사용하지 않음
- 기존 문서 관계: 전체 구조 문서의 코드 사실과 모순은 확인하지 않았으며, 원본 분석 기준·전체 구조 문서는 변경하지 않음
- 최종 범위 검사: 신규 문서 5/5, 전체 변경 8/8, 모든 신규 문서의 source_commit·type·source_paths 및 템플릿 절 구성 확인; Git staging 없음
- 링크 검사: 상대경로 Wiki 링크 86개 존재 확인, 전체 SHA 고정 소스 링크 432개의 HEAD 파일·줄 범위 확인; 5개 문서 각각에서 나머지 4개 문서로 연결 확인; 원격 URL 접속 검증은 미수행
- 수식·값 검사: 모터 속도/RPM·100 RPM 제한·SDK raw 전송·feedback signed 변환·Odometry/JointState·파라미터 선언/읽기를 원본 구문과 정적으로 대조해 일치 확인; YAML 두 키와 설정값 원문 일치
- 형식 검사: git diff --check 종료 코드 0 및 출력 없음; 신규 문서 5개를 포함한 8개 파일의 마지막 공백·불필요한 연속/EOF 빈 줄 검사 통과
- 보존 검사: 기존 index 링크·설명 및 log 이력 보존, 허용 범위 밖 기존 Wiki 파일 19개 불변; 원본 브랜치·HEAD·Git 상태가 작업 시작 시점과 동일
- 남은 미검증: 실제 런타임 설정값·물리 단위·기구 보정·외부 SDK/Battery/LiDAR·QoS/TF·모터/센서 동작, Joint 이름·부호·초기화 실패/종료 처리의 실제 영향
- 실행 범위: 원본 수정, ROS2·로봇·모터·센서·simulator 실행, 빌드·테스트·설치·시스템 설정 변경 및 Git add/commit/push/checkout 미수행
- 다음 작업: 작성 문서 검토와 미검증 사항의 정적 추가 분석, 이후 권장 순서의 다음 패키지 상세 분석; 실제 실행 검증은 이번 범위 밖

## [2026-11-18] docs | English learning route and full package coverage

- 작업: 기존 AGENTS.md, 문서 템플릿, 색인, README, 로그와 분석 문서를 확인하고 기존 폴더 구조를 이어서 사용했다.
- 접근: 이 로컬 세션에서 원본과 Wiki를 직접 읽었다. 별도의 ChatGPT Work 창이나 파일 권한 팝업을 열었다고 기록하지 않는다.
- 언어: 학습 본문·그림·표는 영어, 설명용 코드 주석과 작업 메모는 한국어로 작성했다.
- 원본: `/home/ljb/dev_ws/pinky/src/pinky_pro`, 기준 SHA `014a09f289e6988894fbffde2624fdb1e257815b`.
- 범위: 영어 학습 로드맵, 전체 구조, bringup 통합 학습, 나머지 10개 패키지, TF·시뮬레이션·탐색·센서 파이프라인, 인터페이스 지도, 탐색 파라미터와 웹 코드 분석.
- 보존: 기존 분석 문서 7개와 규칙·템플릿·미등록 `main`을 보존했다. README/index는 기존 내용을 날짜가 있는 역사 기록으로 보존하고 최신 영어 안내를 앞에 추가했다. 과거 로그는 보존했다.
- 근거: 모든 분석은 기준 커밋의 파일과 줄 번호에 연결했다. 미등록 navigation 파일 3개는 기준 SHA 코드 근거에서 제외했다.
- 검증 상태: 코드 확인·추론·미검증을 구분했다. YAML 설정과 실제 적용값, 요청 발행과 비동기 완료 결과를 구분했다.
- 최종 변경 목록과 실제 검증 결과: [Evidence and validation](06_Change_History/2026-11-18-learning-wiki-evidence.md).
- 실행 범위: 문서와 그림 검증만 수행했다. ROS2·로봇·시뮬레이터·빌드·저장소 테스트·Git add/commit/push/checkout은 수행하지 않았다.

## [2026-11-19] docs | Local learning unit review and extension

- 작업: 실제 소스와 Wiki 경로, Git 상태, AGENTS.md와 templates/README.md를 확인했다. 원본 HEAD는 기준 SHA와 일치했다.
- 시작 상태: Wiki에는 2026-11-18 영어 학습 문서와 미커밋 변경이 이미 있었다. 이를 새로 만든 것으로 보고하지 않고 전체 기존 파일을 먼저 백업했다.
- 언어: 학습 본문·Mermaid·표는 영어, 설명용 코드 주석·작업 메모는 한국어로 유지했다.
- 갱신: 첫 완결 학습 단위와 bringup 연결, 전체 구조·시작 준비, Node/Topic/Service/Action/TF 관계, 실물/Gazebo/MuJoCo launch 흐름, 두 시뮬레이터 명령·피드백 예제를 보완했다.
- 발견: hardware의 use_sim_time 전달과 description의 is_sim 사용이 다른 점을 코드 확인으로 기록했다. 실제 launch 실패나 적용 시각은 미검증으로 남겼다. Gazebo 그림에서 scan과 odom의 독립 생산자를 구분하고 MuJoCo setup 순서를 정정했다.
- 실제 변경: 기존 문서 10개 갱신, 이번 세션 증거 보고서 1개 생성. 상세 경로·검증 결과는 [2026-11-19 검토 보고서](06_Change_History/2026-11-19-learning-wiki-review.md)에 기록한다.
- 보존: 이전 log 전체, README/index의 과거 기록, 기존 bringup 분석·규칙·템플릿·main과 원본의 미등록 3개 파일을 보존했다.
- 실행 범위: 정적 코드 대조·문서·그림 검증만 수행했다. ROS2·로봇·시뮬레이터·빌드·저장소 테스트는 실행하지 않았다. 원본 수정과 Git add/commit/push/checkout도 수행하지 않았다.
- 다음 학습: Obsidian에서 learning-roadmap의 첫 단위와 self-check를 읽는다. 이후 navigation/sensor/expression 순서로 깊이를 넓히며 런타임 검증은 별도 근거가 생길 때 기록한다.

## [2026-11-19] docs | GitHub publication preparation

- 승인: 사용자가 기존 영어 학습 자료를 `seljb/pinky_pro_wiki`의 `main`으로 push하여 집 PC에서도 확인하도록 명시적으로 승인했다.
- 확인: 실제 Wiki 경로·main 브랜치·Git 상태·origin을 확인했다. GitHub main을 fetch한 결과 기존 기준 커밋 `512c40ab5bd947406d392ab9ba48bfd1127c6cc3`과 일치하여 원격과 분기되지 않았다.
- 게시 범위: 이미 로컬에 있던 영어 Markdown 22개와 README/index/log 변경만 포함한다. 새 학습 자료를 과거 작업인 것처럼 재생성하지 않았다. 빈 미등록 파일 `main`은 보존하고 커밋에서 제외한다.
- 검증: 상대 링크·제목 앵커, 기준 SHA에 고정된 소스 링크 1,272개와 81개 소스 파일의 경로·줄 범위, 색인 등록, 학습 본문 영어·설명용 코드 주석 한국어, 비밀정보 패턴과 공백 오류를 점검했다. 별도 읽기 전용 검토에서도 게시 차단 문제가 없었다.
- 보존: 게시 전 Wiki 파일 전체를 별도 백업했다. 과거 README/index/log 내용을 그대로 보존했고 원본 소스 파일 160개·HEAD·Git 상태가 시작 시점과 같음을 확인했다. 원본의 미등록 3개 파일도 보존했다.
- 안내: README에 집 PC의 웹 열람, 최초 clone, Obsidian Vault 열기 및 이후 `pull --ff-only` 방법을 영어로 추가했다.
- 실행 범위: 문서 정적 검증과 Git 동기화 준비만 수행했다. 로봇·ROS 노드·시뮬레이터·빌드·원본 코드 변경은 수행하지 않았다. 이 항목은 커밋 전 준비 기록이며 push 성공 여부와 최종 SHA는 실제 GitHub 조회 후 작업 결과로 보고한다.
