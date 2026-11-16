# Wiki Document Templates & Linking Rules

## 1. 목적

LLM이 Pinky Pro 소스코드를 분석할 때
일관된 형식으로 Wiki 문서를 생성하고,
문서 사이의 관계를 유지하기 위한 규칙이다.

## 2. 문서 유형

| 유형 | 템플릿 | 저장 위치 |
|---|---|---|
| Architecture | architecture.md | 01_Architecture/ |
| Package | package.md | 02_Packages/ |
| Parameter | parameter.md | 03_Parameters/ |
| Feature | feature.md | 04_Features/ |
| Code Analysis | code-analysis.md | 05_Code_Analysis/ |

템플릿 파일은 직접 분석 결과를 저장하는 곳이 아니다.

분석 결과는 각 문서 유형의 저장 폴더에 생성한다.

## 3. 파일 이름 규칙

파일 이름은 가급적 소문자와 하이픈을 사용하고,
동일한 대상을 항상 같은 이름으로 관리한다.

예시:

01_Architecture/navigation-pipeline.md
02_Packages/pinky_navigation.md
03_Parameters/pinky_navigation--controller--desired_linear_vel.md
04_Features/narrow-passage.md
05_Code_Analysis/pinky_bringup--bringup.py.md

파라미터는 이름이 중복될 수 있으므로
패키지와 관련 Node 또는 설정 그룹을 함께 기록한다.

기존 문서가 있으면 중복 생성하지 않고
해당 문서를 확인한 후 갱신한다.

## 4. 문서 연결 규칙

1. 모든 문서는 관련 Wiki 문서의 상대경로 링크를 사용한다.
2. 새 문서를 만들면 index.md에 등록한다.
3. 가능하면 관련 문서에서도 새 문서로 연결한다.
4. 링크 대상 파일이 실제 존재하는지 확인한다.
5. 정보가 없는 문서의 링크를 임의로 만들지 않는다.
6. 동일한 설정값 설명은 Parameter 문서를 기준으로 관리한다.
7. Feature 문서에는 관련 파이프라인과 코드의 연결을 기록한다.

Markdown 링크 예시:

[Navigation Package](../02_Packages/pinky_navigation.md)

## 5. 원본 코드 근거

원본 코드 근거는 반드시 Wiki 링크와 구분한다.

- Repository: seljb/pinky_pro
- Source Commit: 실제 분석한 Git SHA
- Source Path: 저장소 내부 상대경로
- Source Lines: 가능한 경우 시작~끝 줄 번호
- Verification: 코드 확인 / 추론 / 실행 확인 / 미검증

GitHub 원본 코드 링크에는 가능한 경우
브랜치 이름 대신 Commit SHA를 사용한다.

예시 형식:

https://github.com/seljb/pinky_pro/blob/<COMMIT_SHA>/<FILE_PATH>#L10-L20

## 6. 문서 갱신 절차

1. index.md에서 관련 문서를 검색한다.
2. 기존 Wiki 설명을 읽는다.
3. 실제 소스코드와 관련 의존성을 확인한다.
4. 분석 기준 Commit을 확인한다.
5. 기존 문서를 갱신하거나 새 문서를 생성한다.
6. 관련 문서의 링크를 확인하고 갱신한다.
7. index.md를 갱신한다.
8. log.md에 작업 내용을 기록한다.
9. 검증되지 않은 내용은 미검증으로 표시한다.

## 7. 문서별 역할 구분

README.md:
프로젝트 목표 및 구축 진행 상황.

index.md:
문서의 위치와 주요 내용을 찾기 위한 색인.

log.md:
Wiki 분석 및 문서 변경 작업의 시간순 기록.

AGENTS.md:
LLM 에이전트의 작업 규칙.

templates/:
문서 작성 양식과 연결 규칙.

## 8. 유지관리

- 분석 결과와 추론을 구분한다.
- 실제 실행 결과를 임의로 생성하지 않는다.
- 새 코드 버전이 확인되면 관련 문서를 검토한다.
- 기존 기록과 변경 이유를 보존한다.
- 문서 간 모순 및 잘못된 링크를 점검한다.
