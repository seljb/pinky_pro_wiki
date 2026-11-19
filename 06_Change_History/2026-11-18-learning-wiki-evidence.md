---
type: evidence
title: "Learning Wiki evidence and validation — 2026-11-18"
source_repo: seljb/pinky_pro
source_commit: "014a09f289e6988894fbffde2624fdb1e257815b"
source_paths: []
verification: "Static documentation checks; runtime unverified"
---

# Learning Wiki evidence and validation — 2026-11-18

## 1. Baseline and method

The existing Wiki structure, rules, templates, seven analysis documents and history were read before authoring. New learning material adapts the existing Architecture, Package, Parameter, Feature and Code Analysis templates. The learning roadmap adapts their common metadata/evidence sections because there is no Overview-specific template.

| Item | Recorded baseline |
|---|---|
| Source repository | `seljb/pinky_pro` |
| Source path | `/home/ljb/dev_ws/pinky/src/pinky_pro` |
| Source branch | `feature/nav2-study` |
| Source commit | `014a09f289e6988894fbffde2624fdb1e257815b` |
| Wiki path | `/home/ljb/dev_ws/pinky_pro_wiki` |
| Wiki starting HEAD | `512c40ab5bd947406d392ab9ba48bfd1127c6cc3` |
| Source tracked changes at start | None |
| Source untracked files | Three, excluded below |
| Wiki untracked item at start | `main`, preserved |

Local untracked files excluded from commit-based analysis:

- `pinky_navigation/launch/localization_only_launch.xml`
- `pinky_navigation/nav2_send_goal_basic.py`
- `pinky_navigation/params/amcl_params.yaml`

The files exist locally but are not in the recorded commit. Their paths are recorded for provenance; no lesson treats them as committed implementation.

Source links are pinned to the full commit. Local validation checks their paths and line ranges against Git objects without fetching remote pages. It does not prove that every GitHub URL is publicly reachable. Implementation claims were reviewed against source; conceptual synthesis and arithmetic examples are labeled separately. External library behavior remains unverified.

## 2. Exact created files

- [00_Overview/learning-roadmap.md](../00_Overview/learning-roadmap.md)
- [01_Architecture/system-overview.md](../01_Architecture/system-overview.md)
- [04_Features/motion-and-feedback.md](../04_Features/motion-and-feedback.md)
- [01_Architecture/robot-description-and-tf.md](../01_Architecture/robot-description-and-tf.md)
- [01_Architecture/simulation-pipeline.md](../01_Architecture/simulation-pipeline.md)
- [01_Architecture/navigation-pipeline.md](../01_Architecture/navigation-pipeline.md)
- [01_Architecture/sensors-and-expression.md](../01_Architecture/sensors-and-expression.md)
- [04_Features/ros2-interface-atlas.md](../04_Features/ros2-interface-atlas.md)
- [02_Packages/pinky_description.md](../02_Packages/pinky_description.md)
- [02_Packages/pinky_gz_sim.md](../02_Packages/pinky_gz_sim.md)
- [02_Packages/pinky_mujoco.md](../02_Packages/pinky_mujoco.md)
- [02_Packages/pinky_navigation.md](../02_Packages/pinky_navigation.md)
- [02_Packages/pinky_imu_bno055.md](../02_Packages/pinky_imu_bno055.md)
- [02_Packages/pinky_sensor_adc.md](../02_Packages/pinky_sensor_adc.md)
- [02_Packages/pinky_interfaces.md](../02_Packages/pinky_interfaces.md)
- [02_Packages/pinky_lamp_control.md](../02_Packages/pinky_lamp_control.md)
- [02_Packages/pinky_led.md](../02_Packages/pinky_led.md)
- [02_Packages/pinky_emotion.md](../02_Packages/pinky_emotion.md)
- [03_Parameters/navigation-parameters.md](../03_Parameters/navigation-parameters.md)
- [05_Code_Analysis/navigation-web-control.md](../05_Code_Analysis/navigation-web-control.md)
- [06_Change_History/2026-11-18-learning-wiki-evidence.md](../06_Change_History/2026-11-18-learning-wiki-evidence.md)

## 3. Exact updated files

- [README.md](../README.md) — English home, Obsidian instructions and current coverage; original content preserved as historical notes.
- [index.md](../index.md) — current English learning index and links; original index preserved as historical notes.
- [log.md](../log.md) — appended Korean work record; previous entries preserved.

No existing analysis document was replaced or translated destructively. The English motion lesson supplies a complete learning explanation while linking the original detailed bringup analyses. All other existing Wiki files, including rules, templates and untracked `main`, retain their original bytes. No source files were changed. No files were removed.

## 4. Validation results

- Markdown documents checked: **38** (including existing references and templates).
- New learning/evidence documents: **21**; existing files updated: **3**; removed: **0**.
- Front matter parsed: **33** documents; new-page metadata and tracked source paths checked.
- Relative link occurrences checked: **367**; linked files and heading anchors exist.
- SHA-pinned source link occurrences checked: **1218**, covering **81** source files; paths and line ranges match Git objects.
- Code fences, final newline and changed-file whitespace checks: **passed**.
- Mermaid diagrams parsed and rendered: **19/19**, using the installed VS Code Mermaid bundle in a temporary clean headless Chrome profile. Wide diagrams were adjusted and rendered again; representative final images were visually inspected.
- All new documents registered in the current index: **passed**.
- Original source file hashes/file set compared: **160/160 unchanged**, including the three local untracked files; source HEAD and Git status unchanged.
- Existing analysis/rules/templates and untracked Wiki `main`: **byte-preserved**; old README/index content and previous log entries retained.
- Cross-review: separate static reviews checked the motion calculations, navigation configuration and description/simulation/peripheral contracts; minor learning wording/pseudocode issues were corrected.
- Validation errors: **0**. These counts describe the complete staged Markdown set that is copied to the Wiki; the final delivery report records the saved-file comparison.

The tests above validate documents and diagram rendering. They do not validate ROS startup, algorithm correctness, physical calibration, sensor units, lifecycle activation, effective parameters, QoS negotiation, TF timing, navigation success or real motor behavior. Obsidian-specific interaction was not exercised; Mermaid was checked with the installed renderer described above.

## 5. Important source-level findings

- Physical bringup and simulator bringup are different entry paths; `gz_` navigation filenames do not themselves start Gazebo.
- Hardware wheel JointState names differ from the Xacro joint names; consequences require runtime evidence.
- MuJoCo and Gazebo expose overlapping interfaces with different sensor coverage, sample counts and model settings.
- Peripheral packages are not automatically launched by the real-robot bringup; ADC `batt_state` and bringup battery topics are separate paths.
- Navigation YAML depends on the selected entry launch, and upper bringup overrides the lifecycle list while omitting `smoother_server`.
- Browser request-issued flags do not establish goal/service completion; the web lesson records TF-cache and coordinate-overlay limitations.

These findings are explained and source-cited in the [system overview](../01_Architecture/system-overview.md), [motion lesson](../04_Features/motion-and-feedback.md), [TF lesson](../01_Architecture/robot-description-and-tf.md), [simulation lesson](../01_Architecture/simulation-pipeline.md), [sensor lesson](../01_Architecture/sensors-and-expression.md), [navigation lesson](../01_Architecture/navigation-pipeline.md) and [web analysis](../05_Code_Analysis/navigation-web-control.md).

## 6. Verification vocabulary and scope

| Label | Meaning |
|---|---|
| Code-confirmed / Code verified / [코드 확인] | Definition, constant, configuration or control flow inspected in source |
| Inference / [추론] | Educational synthesis or predicted consequence of source/configuration |
| Execution verified / [실행 확인] | Requires actual execution evidence; no robot/simulator claims carry this label here |
| Unverified / [미검증] | A runtime or external-behavior question still requiring evidence |

Learning coverage spans all 11 packages; this is not a claim of exhaustive formal verification or completed runtime testing. Source code remained read-only. No ROS node, simulator, motor/sensor command, build or repository test ran; no Git staging, commit, push or checkout occurred.

[Home](../README.md) · [Roadmap](../00_Overview/learning-roadmap.md) · [Index](../index.md) · [Operation log](../log.md)

> 작업 메모: 기존 학습 자료와 작업 이력은 보존했다. 원본의 추적·미등록 파일을 모두 해시로 비교하고, 검증 결과는 실제 수행한 범위만 기록한다.
