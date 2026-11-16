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

### 진행 예정

- [ ] Wiki 문서 템플릿 설계
- [ ] 문서 간 연결 규칙 정의
- [ ] LLM 분석 환경 설정
- [ ] Pinky Pro 전체 패키지 분석
- [ ] ROS2 파이프라인 및 코드 분석
- [ ] 파라미터 카탈로그 구축
- [ ] V1 질의응답 시스템 구축
- [ ] Wiki 정확성 및 일관성 검증

## 7. 다음 작업

STEP 6: 문서 템플릿 및 연결 규칙 구축

- Architecture 문서
- Package 문서
- Code Analysis 문서
- Parameter 문서
- Feature 문서

## 8. 관리 원칙

- README.md: 프로젝트 목표와 진행 상황
- index.md: Wiki 문서 색인
- log.md: 분석 및 작업 이력
- AGENTS.md: LLM의 작업 규칙

작업이 완료될 때마다 README의 진행 상황과
log.md의 작업 기록을 업데이트한다.
