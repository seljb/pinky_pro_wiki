---
type: evidence
title: "Local learning Wiki review — 2026-11-19"
source_repo: seljb/pinky_pro
source_commit: "014a09f289e6988894fbffde2624fdb1e257815b"
source_paths: []
verification: "Static source and document checks; runtime unverified"
updated: 2026-11-19
---

# Local learning Wiki review — 2026-11-19

## 1. What existed before this session

Both requested local paths exist: source at `/home/ljb/dev_ws/pinky/src/pinky_pro`, Wiki at `/home/ljb/dev_ws/pinky_pro_wiki`. The source HEAD matches the recorded SHA. Wiki HEAD is `512c40ab5bd947406d392ab9ba48bfd1127c6cc3`.

The local Wiki already contained the English roadmap, architecture, ten package lessons and other material recorded in the [2026-11-18 evidence report](2026-11-18-learning-wiki-evidence.md). Those files were present as uncommitted work when this session started. This session extends that existing work; it does not claim to have newly created those pages. Existing modified README/index/log, untracked learning pages, empty `main`, rules and templates were backed up before editing. No file was deleted.

Source tracked files were clean. Three local untracked files were preserved and excluded from fixed-commit evidence: `pinky_navigation/launch/localization_only_launch.xml`, `pinky_navigation/nav2_send_goal_basic.py`, and `pinky_navigation/params/amcl_params.yaml`.

## 2. Actual changes in this session

| Updated file | Learning improvement |
|---|---|
| [Learning roadmap](../00_Overview/learning-roadmap.md) | Complete first reading unit, straight-motion paper exercise with answers, and links to all five preserved bringup analyses |
| [System overview](../01_Architecture/system-overview.md) | Process selection versus startup readiness; description/motor roles and clock argument mismatch |
| [Robot description and TF](../01_Architecture/robot-description-and-tf.md) | Full intended TF chain, per-edge ownership and web consumer lookup |
| [Simulation pipeline](../01_Architecture/simulation-pipeline.md) | Real/Gazebo/MuJoCo launch flow, separate navigation entrypoints, time/environment/generated-file distinctions; corrected MuJoCo setup order |
| [ROS 2 interface atlas](../04_Features/ros2-interface-atlas.md) | Concrete executable/node identities, separate command and feedback paths and exact topic spelling |
| [MuJoCo package](../02_Packages/pinky_mujoco.md) | Worked command clamp/kinematics, receipt timeout, invalid-message handling and measured state |
| [Gazebo package](../02_Packages/pinky_gz_sim.md) | Transport boundary walkthrough; independent odometry, joint and lidar paths; corrected diagram |
| [README](../README.md) | Current local learning status and entry route |
| [Index](../index.md) | Updated route description and registration of this report |
| [Log](../log.md) | Appended Korean work record; prior entries preserved |

New file: **this report**, `06_Change_History/2026-11-19-learning-wiki-review.md`. All other files retain their previous bytes. Earlier evidence reports remain historical records of their own sessions.

## 3. Source findings and evidence labels

The revised lessons cite immutable source paths and line ranges. **[Code confirmed]** means inspected source declarations/control flow, **[Inference]** means a calculation or conceptual consequence, and **[Unverified]** means runtime or external behavior was not observed. No robot or simulator behavior has been promoted to **[Execution verified]**.

The hardware parent forwards `use_sim_time` while the description child consumes `is_sim`. The lessons document this name mismatch without claiming a measured launch failure. Gazebo scan has a different producer from drive odometry; its diagram now shows that distinction. MuJoCo command receipt, accepted/clamped command, actuator target and simulated feedback are different quantities. Read the linked lessons for the source evidence and worked answers.

Independent static cross-reviews checked the new motion arithmetic/startup claims and the simulator package examples. No material issues remained after those reviews.

## 4. Validation of the saved document set

| Check | Result |
|---|---|
| Markdown documents / parsed frontmatter | 39 / 34 |
| Relative Markdown link occurrences, including heading anchors | 404 passed |
| Source link occurrences pinned to the exact SHA | 1,272 passed, covering 81 source files |
| Mermaid diagrams parsed and rendered | 22 / 22 passed; four representative final diagrams visually reviewed |
| Code fences, final newlines and changed-file whitespace | Passed |
| New page registered in index | Passed |
| Source files and file set | 160 / 160 unchanged, including untracked files |
| Source HEAD and Git status | Unchanged |
| Prior Wiki files kept / files removed | 49 / 0 |
| Unchanged Wiki files / updated / newly created | 39 / 10 / 1 |
| Prior history, rules, templates and original bringup analyses | Preserved |
| Saved Wiki compared with validated staging set | All 50 files match |
| Wiki Git HEAD / tracked whitespace check | Unchanged / passed |

No validation errors remained. These results describe this session's final file set; the previous report retains its own dated counts.

Link validation compares pinned paths and line bounds with local Git objects. It does not check remote URL accessibility. Mermaid rendering uses the installed VS Code Mermaid bundle in a temporary headless Chrome profile; it is not an Obsidian interaction test. The documentation checks do not validate ROS startup, external plugins, physical calibration, effective parameters, TF timing or navigation success.

## 5. Preservation and scope

The complete pre-edit Wiki file set was backed up. Historical README/index sections, all prior log entries, AGENTS.md, templates and original bringup analyses were preserved. The source file set, file hashes, Git HEAD and Git status were compared before and after work. The Wiki was saved locally without staging, commit or push; its prior HEAD remains unchanged.

No ROS node, simulator, robot command, build, repository test, package install or source edit was performed. This is a completed local documentation and static verification unit. Coverage across all eleven packages remains available, with further learning depth and runtime investigations tracked as future work rather than claimed complete.

[Home](../README.md) · [Roadmap](../00_Overview/learning-roadmap.md) · [Index](../index.md)

> 작업 메모: 이번 시작 시 이미 존재한 영어 문서를 신규 생성으로 집계하지 않았다. 기존 변경분을 먼저 백업한 뒤 실제 변경 파일만 기록하고, 사용자 승인 없는 push는 수행하지 않았다.
