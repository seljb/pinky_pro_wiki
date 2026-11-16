---
type: overview
title: "Pinky Pro 소스코드 분석 기준"
source_repo: seljb/pinky_pro
source_commit: "014a09f289e6988894fbffde2624fdb1e257815b"
source_paths: []
verification: 미검증
---

# Pinky Pro 소스코드 분석 기준

## 1. 목적 및 문서 범위

앞으로 LLM Wiki가 Pinky Pro 소스코드를 분석할 때 사용할 Git 기준과
읽기 전용 작업 범위를 기록한다. 확인일은 2026-11-17이다.

이 문서는 저장소 수준의 기준 문서다. Overview 전용 템플릿이 없으므로
[코드 분석 템플릿](../templates/code-analysis.md)의 공통 메타데이터,
관련 문서 및 코드 근거·검증 상태 형식을 기준 문서에 맞게 적용했다.
개별 소스 파일, 함수, Node, Topic 및 파라미터 분석은 수행하지 않았으므로
`source_paths`는 비워 두었다. `verification: 미검증`은 코드 동작에 대한
상태이며, 아래 Git 상태는 로컬 Git 명령으로 별도 확인했다.

## 2. 저장소 및 분석 기준 버전

| 항목 | 기준 및 확인 결과 |
|---|---|
| 원본 Repository | `seljb/pinky_pro` |
| 원본 GitHub 주소 | <https://github.com/seljb/pinky_pro> |
| 원본 로컬 경로 | `/home/ljb/dev_ws/pinky/src/pinky_pro` |
| 분석 기준 브랜치 | `feature/nav2-study` |
| 분석 기준 전체 Commit SHA | `014a09f289e6988894fbffde2624fdb1e257815b` |
| Wiki Repository | `seljb/pinky_pro_wiki` |
| Wiki GitHub 주소 | <https://github.com/seljb/pinky_pro_wiki> |
| Wiki 로컬 경로 | `/home/ljb/dev_ws/pinky_pro_wiki` |
| 추적 파일의 staged 변경 | 없음 |
| 추적 파일의 unstaged 변경 | 없음 |
| 미등록 파일 | 3개, 다음 절에 기록 |

분석 기준은 브랜치명과 함께 기록한 전체 Commit SHA로 식별한다.
브랜치가 이후 이동하더라도 이 SHA가 가리키는 기준 커밋은 바뀌지 않는다.
GitHub 주소는 [프로젝트 README](../README.md)에 기재된 주소이며,
이번 작업에서는 원격 저장소 상태나 로컬 커밋의 원격 존재 여부를 조회하지 않았다.

## 3. 미등록 파일과 기준 커밋의 경계

확인 시점의 `git status --porcelain=v1 --untracked-files=all`에서
다음 경로가 모두 `??`(미등록 파일)로 표시되었다.

| 원본 저장소 내부 상대경로 | 현재 HEAD 트리 포함 여부 |
|---|---|
| `pinky_navigation/launch/localization_only_launch.xml` | 포함되지 않음 |
| `pinky_navigation/nav2_send_goal_basic.py` | 포함되지 않음 |
| `pinky_navigation/params/amcl_params.yaml` | 포함되지 않음 |

`git ls-tree`로 위 세 경로가 현재 HEAD 트리에 없음을 확인했다.
현재 미등록 상태의 로컬 파일은 기준 커밋의 구성에 포함되지 않는다.
Commit SHA는 커밋 객체를 식별하므로 이러한 로컬 파일의 존재나 내용을
반영하지 않으며, 이 SHA만으로 위 파일 내용을 재현할 수 없다.

따라서 위 파일을 HEAD에 포함된 코드나 SHA로 고정된 코드 근거로 취급하지 않는다.
별도 분석이 필요하면 로컬 미등록 파일임을 명시하고 기준 커밋의 코드와 구분한다.
이번에는 파일 내용·용도, 과거 커밋이나 다른 브랜치에 존재했는지,
빌드·실행 중 사용되는지는 확인하지 않았다.

## 4. 읽기 전용 작업 원칙

- 원본 소스는 분석 대상으로만 읽는다. 원본 파일의 생성·수정·삭제를 수행하지 않는다.
- 원본 저장소에서 Git add, commit, push, checkout 등 상태를 바꾸는 명령을 실행하지 않는다.
- 로봇 동작 코드를 임의로 실행하지 않는다. 이번 작업에서는 ROS2 실행과 로봇 제어를 수행하지 않았다.
- Wiki 분석 문서는 [문서 색인](../index.md)에서 기존 내용을 먼저 찾고,
  필요한 소스 및 관련 의존성을 읽어 근거를 확인한 뒤 작성한다.
- 코드 근거에는 Repository, 실제 확인한 전체 Commit SHA, 저장소 내부 파일 경로,
  가능한 경우 함수·클래스·줄 번호, 관련 Node·Topic·파라미터를 기록한다.
- 현재 파일의 설정값과 실제 런타임 적용값을 구분한다.
  런타임 적용 여부는 파일에 값이 있다는 사실만으로 확정하지 않는다.
- 이후 분석 전에는 브랜치, HEAD, 추적 파일 변경 및 미등록 파일을 다시 확인한다.
  기준이 달라지면 새 근거와 변경 이유를 기록하고 과거 이력을 보존한다.

이번 문서 작성의 변경 범위는 `00_Overview/source-baseline.md`,
`index.md`, `log.md` 세 파일이다.

## 5. 근거 및 검증 상태

Git 상태는 다음 명령을 원본 저장소에서 읽기 전용으로 실행해 확인했다.
모든 명령은 정상 종료했으며, 원본 소스코드나 로봇 동작을 실행한 것은 아니다.

```bash
git --no-optional-locks rev-parse --show-toplevel
git --no-optional-locks branch --show-current
git --no-optional-locks rev-parse HEAD
git --no-optional-locks status --porcelain=v1 --untracked-files=all
git --no-optional-locks diff --name-status
git --no-optional-locks diff --cached --name-status
git --no-optional-locks ls-files --others --exclude-standard
git --no-optional-locks ls-tree -r --name-only HEAD -- pinky_navigation/launch/localization_only_launch.xml pinky_navigation/nav2_send_goal_basic.py pinky_navigation/params/amcl_params.yaml
```

| 상태 | 적용 기준 및 이번 작업의 범위 |
|---|---|
| 로컬 Git 조회 확인 | 저장소 경로, 현재 브랜치, HEAD SHA, 추적 파일 변경 없음, 미등록 파일 3개 및 해당 경로의 HEAD 트리 부재를 확인했다. 코드 동작 검증과 구분한다. |
| [코드 확인] | 실제 소스 파일과 관련 의존성에서 확인한 사실에만 사용한다. 이번에는 코드 내용을 분석하지 않았으므로 이 상태의 동작 설명을 작성하지 않았다. |
| [추론] | 코드나 문서에 근거해 해석한 내용에 사용하며, 직접 확인한 사실과 구분한다. |
| [실행 확인] | 실제 실행 결과가 있는 경우에만 사용한다. 이번에는 ROS2·로봇 실행 및 테스트를 수행하지 않았다. |
| [미검증] | 코드 동작, 빌드 성공 여부, 런타임 설정·연결·동작, 미등록 파일 내용 및 원격 저장소 상태는 이번 작업에서 확인하지 않았다. |

## 6. 관련 Wiki 문서

- [Wiki 문서 색인](../index.md)
- [프로젝트 안내](../README.md)
- [LLM 작업 규칙](../AGENTS.md)
- [문서 템플릿 및 연결 규칙](../templates/README.md)
- [작업 기록](../log.md)
